# 06 Framework Integrations

## Original File: anchor_integration.md
---
# Anchor Integration Guide

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CTAButton from "../../src/components/CTAButton";

This guide will show you how to integrate an Anchor Program into your React Native dApp, using the [Anchor Counter dApp](https://github.com/solana-mobile/tutorial-apps/tree/main/AnchorCounterDapp) as reference.

<CTAButton label="Example App Repo" to="https://github.com/solana-mobile/tutorial-apps/tree/main/AnchorCounterDapp" />

## What you will learn

- How to import an Anchor Program into a React Native project
- How to create an Anchor Wallet and Provider with Mobile Wallet Adapter
- How to sign and submit transactions with an Anchor Program IDL
- How to generate instructions with an Anchor Program IDL

## Prerequisites

- [React Native setup](../react-native/setup) and [Anchor setup](https://book.anchor-lang.com/getting_started/installation.html)
- Basic understanding of [Anchor Programs](https://book.anchor-lang.com/) and IDL.
- An existing [deployed](https://book.anchor-lang.com/anchor_in_depth/milestone_project_tic-tac-toe.html#deployment) Anchor Program.

## Installation

Add the Anchor library to your React Native Project:

:::caution
React Native apps should use Anchor v0.28.0 because later versions of the library have a polyfill issue on React Native.
:::
<Tabs>
<TabItem value="yarn" label="yarn">

```shell
yarn add @coral-xyz/anchor@0.28.0
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
npm install @coral-xyz/anchor@0.28.0
```

</TabItem>
</Tabs>

## Create an Anchor Wallet with Mobile Wallet Adapter

:::tip
The Anchor Counter Program example app shows how to create an Anchor wallet that is integrated
with a more complex state management framework.

<CTAButton label="View sample" to="https://github.com/solana-mobile/tutorial-apps/blob/main/AnchorCounterDapp/src/utils/useAnchorWallet.tsx#L23" />

<div />

:::

To create an `AnchorWallet`, use Mobile Wallet Adapter `transact` to implement the required signing functions.

A simple implementation:

```tsx
import * as anchor from "@coral-xyz/anchor";
import {
  transact,
  Web3MobileWallet,
} from "@solana-mobile/mobile-wallet-adapter-protocol-web3js";

const anchorWallet = useMemo(() => {
  return {
    signTransaction: async (transaction: Transaction) => {
      return transact(async (wallet: Web3MobileWallet) => {
        const authorizationResult = await wallet.authorize({
              cluster: RPC_ENDPOINT,
              identity: APP_IDENTITY,
        }));

        const signedTransactions = await wallet.signTransactions({
          transactions: [transaction],
        });
        return signedTransactions[0];
      });
    },
    signAllTransactions: async (transactions: Transaction[]) => {
      return transact(async (wallet: Web3MobileWallet) => {
        const authorizationResult = await wallet.authorize({
              cluster: RPC_ENDPOINT,
              identity: APP_IDENTITY,
        }));

        const signedTransactions = await wallet.signTransactions({
          transactions: transactions,
        });
        return signedTransactions;
      });
    },
    get publicKey() {
      return userPubKey;
    },
  } as anchor.Wallet;
}, []);
```

## Importing an Anchor Program in Typescript

### Generating an Anchor Program IDL

If you have an Anchor project in your local workspace, build the program and generate the Typescript IDL with:

```shell
anchor build
```

If the Anchor program is already deployed and you know its address, you can use the [Anchor CLI](https://book.anchor-lang.com/anchor_references/cli.html?highlight=idl#idl) to fetch it:

```shell
anchor idl fetch GrAkKfEpTKQuVHG2Y97Y2FF4i7y7Q5AHLK94JBy7Y5yv
```

### Instantiate your Anchor Program

Once your IDL has been generated, you can import it and create an instance of your `Program` in Typescript.

- Import your generated IDL file, in this case from `/target/types/basic_counter.ts`
- Use the `anchorWallet` from the previous step to create an `AnchorProvider`.

<CTAButton label="See example" to="https://github.com/solana-mobile/tutorial-apps/blob/main/AnchorCounterDapp/src/components/counter/counter-data-access.tsx#L15" />

```tsx
import { BasicCounter as BasicCounterProgram } from "../../basic-counter/target/types/basic_counter";
import { AnchorProvider, Program } from "@coral-xyz/anchor";

const COUNTER_PROGRAM_ID = "ADraQ2ENAbVoVZhvH5SPxWPsF2hH5YmFcgx61TafHuwu";

// Address of the devnet-deployed Counter Program
const counterProgramId = useMemo(() => {
  return new PublicKey(COUNTER_PROGRAM_ID);
}, []);

// Create an AnchorProvider with the anchorWallet.
const provider = useMemo(() => {
  if (!anchorWallet) {
    return null;
  }
  return new AnchorProvider(connection, anchorWallet, {
    preflightCommitment: "confirmed",
    commitment: "processed",
  });
}, [anchorWallet, connection]);

// Create an instance of your Program.
const counterProgram = useMemo(() => {
  if (!provider) {
    return null;
  }

  return new Program<BasicCounterProgram>(
    idl as BasicCounterProgram,
    counterProgramId,
    provider
  );
}, [counterProgramId, provider]);
```

## Sign transactions manually with Mobile Wallet Adapter

With an instantiated `Program`, you can:

- Generate serialized program instructions.
- Construct a `Transaction` with the generated instructions.
- Manually sign the `Transaction` with Mobile Wallet Adapter.

In the following example, we generate an `incrementInstruction` from the program then sign it within a Mobile Wallet Adapter
session.

```tsx
const {counterProgram, counterPDA} = useCounterProgram();

const signIncrementTransaction = async () => {
  return await transact(async (wallet: Web3MobileWallet) => {
    const authorizationResult = wallet.authorize({
      cluster: RPC_ENDPOINT,
      identity: APP_IDENTITY,
    }));

    const latestBlockhash = await connection.getLatestBlockhash();

    // Generate the increment ix from the Anchor program
    const incrementInstruction = await counterProgram.methods
        .increment(new anchor.BN(amount))
        .accounts({
          counter: counterPDA,
        })
        .instruction();

    // Build a transaction containing the instruction
    const incrementTransaction = new Transaction({
      ...latestBlockhash,
      feePayer: authorizationResult.publicKey,
    }).add(incrementInstruction);

    // Sign a transaction and receive
    const signedTransactions = await wallet.signTransactions({
      transactions: [incrementTransaction],
    });

    return signedTransactions[0];
  });
}
```

This approach is flexible and allows you to fully utilize the Mobile Wallet Adapter session.

## Sign transactions using a Mobile Wallet Adapter signer

With an instantiated `Program`, you can also use the Anchor provided `rpc()` function to sign and submit an Anchor transaction to an RPC.

<CTAButton label="See example" to="https://github.com/solana-mobile/tutorial-apps/blob/main/AnchorCounterDapp/src/components/counter/counter-data-access.tsx#L89" />

```tsx
const { counterProgram, counterPDA } = useCounterProgram();

const incrementCounter = async () => {
  // Submit an increment transaction to the RPC endpoint
  const signature = await counterProgram.methods
    .increment(new anchor.BN(amount))
    .accounts({
      counter: counterPDA,
    })
    .rpc();

  return signature;
};
```

Calling the `rpc()` will generate and sign the transaction using the interface methods (`signTransaction`, `signAllTransactions`) of the Anchor Wallet that the program was instantiated with.


---

## Original File: metaplex_integration.md
---
# Metaplex Integration Guide

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CTAButton from "../../src/components/CTAButton";

[**Metaplex**](https://docs.metaplex.com/) provides a collection of on-chain tools/programs for creating and managing NFTs on Solana. In addition, Metaplex provides multiple [Client SDKs](https://docs.metaplex.com/sdks/) that makes it easier for developers to interact and use their on chain programs.

This guide will focus on integrating with the [Metaplex JS SDK](https://docs.metaplex.com/sdks/js/) in a React Native app with Mobile Wallet Adapter.

<CTAButton label="Example App Repo" to="https://github.com/solana-mobile/tutorial-apps/tree/main/MobileNFTMinter" />

## Installation
Install the Metaplex JS package to your project.

<Tabs>
<TabItem value="yarn" label="yarn">

```shell
yarn add @metaplex-foundation/js
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
npm install @metaplex-foundation/js
```

</TabItem>
</Tabs>

### Polyfill installation

The Metaplex JS SDK was originally written for a Browser/Node environment, so certain dependencies aren't immediately available on React Native. These polyfill libraries will fill in the missing libraries and enable React Native compatibility.

#### 1. Install polyfills
```shell
yarn add \
    assert \
    crypto-browserify \
    readable-stream \
    zlib \
    react-native-url-polyfill
```

#### 2. Add polyfills to resolver in metro.config.js
Adding the `resolver` property lets the Metro know which packages to substitute with when seeing a `require`.
```js
module.exports = {
  resolver: {
    extraNodeModules: {
      crypto: require.resolve('crypto-browserify'),
      stream: require.resolve('readable-stream'),
      zlib: require.resolve('browserify-zlib'),
      path: require.resolve('path-browserify'),
      url: require.resolve('react-native-url-polyfill'),
    },
  },
  transformer: {
    getTransformOptions: async () => ({
      transform: {
        experimentalImportSupport: false,
        inlineRequires: true,
      },
    }),
  },
};
```

#### 3. Add imports to index.js
```tsx
import 'react-native-url-polyfill/auto'; // Add this before the 'App' import!

import {AppRegistry} from 'react-native';
import App from './App';
import {name as appName} from './app.json';

AppRegistry.registerComponent(appName, () => App);
```

## Usage

### Creating a Metaplex Instance

The entry point to the JavaScript SDK is a `Metaplex` instance that will give you access to its API. It provides a convenient API to interact with on-chain programs, simplifying actions like minting an NFT.

It accepts a `Connection` instance from `@solana/web3.js` that will be used to communicate with the cluster.

```tsx
import {Metaplex} from '@metaplex-foundation/js';
import {Connection} from '@solana/web3.js';

const metaplex = Metaplex.make(connection);
```

### Using MWA as an Identity Driver

Metaplex also allows you to further customize who the SDK should interact on behalf of, by providing an "Identity Driver". You can use the Mobile Wallet Adapter methods to implement an Identity Driver. With this provided, Metaplex SDK can request signing for transactions/messages when needed. 

#### Create an MWA Identity Signer:

```tsx
import {IdentitySigner, Metaplex} from '@metaplex-foundation/js';
import {
  transact,
  Web3MobileWallet,
} from '@solana-mobile/mobile-wallet-adapter-protocol-web3js';
import {Connection, Transaction} from '@solana/web3.js';

const mwaIdentitySigner: IdentitySigner = {
    publicKey: signersPublicKey,
    signMessage: async (message: Uint8Array): Promise<Uint8Array> => {
        return await transact(async (wallet: Web3MobileWallet) => {
            const authResult = await wallet.authorize({
                cluster: RPC_ENDPOINT,
                identity: APP_IDENTITY,
            }); 
            
            // Optionally, double check authResult returns the matching account to 'signersPublicKey'

            const signedMessages = await wallet.signMessages({
                addresses: [selectedAccount.publicKey.toBase58()],
                payloads: [message],
            });

            return signedMessages[0];
        });
    },
    signTransaction: async (
        transaction: Transaction,
    ): Promise<Transaction> => {
        return await transact(async (wallet: Web3MobileWallet) => {
            const authResult = await wallet.authorize({
                cluster: RPC_ENDPOINT,
                identity: APP_IDENTITY,
             });

            // Optionally, double check authResult returns the matching account to 'signersPublicKey'

            const signedTransactions = await wallet.signTransactions({
                transactions: [transaction],
            });

            return signedTransactions[0];
        });
    },
    signAllTransactions: async (
        transactions: Transaction[],
    ): Promise<Transaction[]> => {
        return transact(async (wallet: Web3MobileWallet) => {
            const authResult = await wallet.authorize({
                cluster: RPC_ENDPOINT,
                identity: APP_IDENTITY,
            })

            // Optionally, double check authResult returns the matching account to 'signersPublicKey'

            const signedTransactions = await wallet.signTransactions({
                transactions: transactions,
            });
            return signedTransactions;
        });
    },
};
```

#### Using the Identity Driver  
Then you need to wrap the implemented `IdentitySigner` within a `MetaplexPlugin`. Now you can call the `use` method on the Metaplex instance and supply the identity driver plugin.

The Metaplex instance requires passing in a `MetaplexPlugin`, so you need to first create a plugin that wraps the identity signer. Lastly, you can call the `use` method on the `Metaplex` instance and supply the identity driver plugin.

```tsx
// Create a Metaplex Plugin for the identity driver
const mobileWalletAdapterIdentity = (
  mwaIdentitySigner: IdentitySigner,
): MetaplexPlugin => ({
  install(metaplex: Metaplex) {
    metaplex.identity().setDriver(mwaIdentitySigner);
  },
});

// Finally, create the Metaplex instance with the identity driver.
const metaplex = Metaplex.make(connection).use(
    mobileWalletAdapterIdentity(mwaIdentitySigner),
);
```

### Storage Drivers

On Browser/Node environments, the SDK also allows you to specify a "Storage Driver" that conveniently integrates various de/centralized storage options (e.g: Bundlr, IPFS, AWS), for uploading media assets and metadata.

**For React Native, the usual storage driver plugins will not work**. Read the note below!

:::warning
The existing 3rd party storage drivers provided by Metaplex **are not compatible with React Native**, due to reliance on Node libraries! For example, the `bundlrStorage()` and `nftStorage()` plugins will not work and throw an error.
:::

:::tip
The workaround solution is to manually interact with the storage providers directly, without relying on a prebuilt Metaplex SDK/plugin.
For example, you can upload directly to IPFS using [NFT.storage's REST API](https://nft.storage/api-docs/). You can see an code example of this in the [example app](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/ipfs/uploadToIPFS.ts#L7).
:::

### Viewing owned NFTs

Now with the Metaplex instance, you can access the `nfts()` module that provides easy interaction and signing with onchain programs.


```tsx
const myNfts = await metaplex.nfts().findAllByOwner({
    owner: metaplex.identity().publicKey
});
```

### Minting an NFT


For example, to mint an NFT:

```tsx
// Uses the identity driver to request signing from a wallet.
const {nft, response} = await metaplex.nfts().create({
    name: 'NFT Name',
    uri: 'https://someService.com/path/to/metadata/json',
    sellerFeeBasisPoints: 0,
    tokenOwner: signersPublicKey,
});
```
The `nfts()` module provides plenty of other APIs. Look through the Metaplex [docs](https://github.com/metaplex-foundation/js#nfts) for full details.







