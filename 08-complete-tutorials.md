# 08 Complete Tutorials

## Original File: first_app_tutorial.md
---
--- 
title: Your first React Native dApp
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

In this tutorial, you'll learn how to build a React Native dApp that sends a message to the Solana network.

## What you will learn
- How to use [**Mobile Wallet Adapter**](/developers/overview#mobile-wallet-adapter) to connect to an installed wallet app.
- How to connect to devnet, check your wallet balance, and request an airdrop of SOL.
- How to use the memo program to write your message to the network
- View your message on the [Solana Explorer](https://explorer.solana.com/)!

## Prerequisites
Read the [prerequisite setup](../developers/development-setup) guide before starting the tutorial. 
You'll need:
- a running Android emulator or device to build and launch your app.
- an MWA-compatible wallet installed on the same device.
- an IDE/Editor of your choice.


This tutorial will be using the [fakewallet](../developers/development-setup#3-install-a-wallet-app) app to test your app's integration with Mobile Wallet Adapter.


## Clone the React Native dApp Scaffold

This dApp will build off the **[React Native Scaffold dApp](/react-native/setup#solana-mobile-dapp-scaffold)** which already has a simple user interface that allows you to connect to a mobile wallet, request an airdrop, and sign transactions. 

#### Step 1. Initialize a template app

```shell
npx react-native init FirstDappTutorial --template https://github.com/solana-mobile/solana-mobile-dapp-scaffold.git
```

#### Step 2. Enter the directory and install the project dependencies.

<Tabs>
<TabItem value="yarn" label="yarn">

```shell
cd FirstDappTutorial && yarn install 
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
cd FirstDappTutorial && npm install 
```

</TabItem>
</Tabs>

#### Step 3. Make sure your emulator/device is running, then build and launch the app.

```shell
npx react-native run-android
```

### First run

At this point, your app should build, install into your device, and launch automatically. 
You should also see the Metro Bundler console window pop up. This is where you can read the logs and access the debug menu.

With React Native's *fast refresh* feature, you can edit the React components, save your changes, and immediately see your app UI update!

## Scaffold dApp Components

Now lets quickly go over the features of the dApp Scaffold. If you want to skip to building the memo transaction, then jump to this [section](/react-native/first_app_tutorial#send-a-memo-transaction).

### Connect Button
Clicking on the *Connect Wallet* button will 'connect' you to a locally installed MWA-compatible wallet. It uses the Mobile Wallet Adapter 
SDK to request [`authorization`](/react-native/quickstart#authorizing-a-wallet) from the wallet and receives your wallet account's info, like the public key.

On click, it starts a wallet session with `transact` and calls `authorizeSession` from the [`AuthorizationProvider`](https://github.com/solana-mobile/tutorial-apps/blob/main/first-mobile-dapp/components/AuthorizationProvider.tsx) class.
```tsx
await transact(async wallet => {
    await authorizeSession(wallet);
});
```

[`AuthorizationProvider`](https://github.com/solana-mobile/tutorial-apps/blob/main/first-mobile-dapp/components/AuthorizationProvider.tsx) is a helper class that manages wallet authorization. It calls `wallet.authorize()` on first connect, and for subsequent connects it re-uses
the `authToken` in `wallet.reauthorize()`.

```tsx
const authorizeSession = useCallback(
    async (wallet: AuthorizeAPI & ReauthorizeAPI) => {
        const authorizationResult = await (authorization
        ? wallet.reauthorize({
            auth_token: authorization.authToken,
            identity: APP_IDENTITY,
            })
        : wallet.authorize({
            cluster: APP_CLUSTER,
            identity: APP_IDENTITY,
            }));
        console.log(authorizationResult);
        return (await handleAuthorizationResult(authorizationResult))
        .selectedAccount;
    },
    [authorization, handleAuthorizationResult],
);
```

### Account Info

This is a simple component takes a balance in [*lamports*](https://docs.solana.com/terminology#lamport) and converts it to units of [*SOL*](https://docs.solana.com/terminology#sol) for display. 

#### Balance fetching
In the `MainScreen.tsx` component, we fetch the user's wallet balance when its available, and pass it into the `AccountInfo` component.
To do so, we use the `connection` class and just call the `getBalance` function, which is part of the [API spec](https://docs.solana.com/api/http#getbalance).

```tsx
const {connection} = useConnection();
const fetchAndUpdateBalance = useCallback(
    async (account: Account) => {
        const fetchedBalance = await connection.getBalance(account.publicKey);
        setBalance(fetchedBalance);
    },
    [connection],
);
```

### Airdrop Button

This component takes in a user's wallet `publicKey` and requests an airdrop of lamports to that address on click. Again, we use the `connection` class
and call the `requestAirdrop` RPC method, as part of the [API spec](https://docs.solana.com/api/http#requestairdrop).

```tsx
const requestAirdrop = useCallback(async () => {
    const signature = await connection.requestAirdrop(
        selectedAccount.publicKey,
        LAMPORTS_PER_AIRDROP,
    );
    return await connection.confirmTransaction(signature);
}, [connection, selectedAccount]);
```

### Sign Transaction/Message Button

The `SignMessageButton` component takes in a `messageBuffer` byte array and calls `wallet.signMessages()`. This requests the
connected wallet to sign the message with the user's private key.

The `SignTransactionButton` component does several things on click. Within the wallet session, it constructs a `Transaction` with a
`SystemProgram.transfer` instruction, then requests the wallet to provide a signature in the transaction.

<Tabs>
<TabItem value="Sign Message" label="Sign Message">

```tsx
const signMessage = useCallback(
    async (messageBuffer: Uint8Array) => {
        return await transact(async (wallet: Web3MobileWallet) => {
        // First, request for authorization from the wallet.
        const authorizationResult = await authorizeSession(wallet);

        // Sign the payload with the provided address from authorization.
        const signedMessages = await wallet.signMessages({
            addresses: [authorizationResult.address],
            payloads: [messageBuffer],
        });

        return signedMessages[0];
        });
    },
    [authorizeSession],
);
```

</TabItem>
<TabItem value="Sign Transaction" label="Sign Transaction">

```tsx
const signTransaction = useCallback(async () => {
    return await transact(async (wallet: Web3MobileWallet) => {
        const connection = new Connection(clusterApiUrl('devnet'), 'confirmed');

        // First, request for authorization from the wallet and fetch the latest
        // blockhash for building the transaction.
        const [authorizationResult, latestBlockhash] = await Promise.all([
            authorizeSession(wallet),
            connection.getLatestBlockhash(),
        ]);

        // Construct a transaction. This transaction uses web3.js `SystemProgram`
        // to create a transfer that sends lamports to randomly generated address.
        const keypair = Keypair.generate();
        const randomTransferTransaction = new Transaction({
            ...latestBlockhash,
            feePayer: authorizationResult.publicKey,
        }).add(
        SystemProgram.transfer({
            fromPubkey: authorizationResult.publicKey,
            toPubkey: keypair.publicKey,
            lamports: 1_000,
        }),
        );

        // Sign a transaction and receive
        const signedTransactions = await wallet.signTransactions({
            transactions: [randomTransferTransaction],
        });

        return signedTransactions[0];
    }, [authorizeSession]);
});
```

</TabItem>
</Tabs>

## Send a memo transaction

Now that we've gone over the existing scaffold, lets add some new functionality to it. 

Instead of a random transfer transaction, lets create a new transaction that records an immutable message on the Solana blockchain, 
using the [Memo program](https://spl.solana.com/memo). After that, we can view our message on the [Solana Explorer](https://explorer.solana.com/).

### Copy over the SignTransactionButton

Lets build off our existing `SignTransactionButton`, and copy it over into a new component renamed to `SendMemoButton`. Then, rename the `signTransaction` helper function into `sendMemo`.


### Construct a memo program transaction

First, let's replace the existing `randomTransferTransaction` with a new transaction that calls the Solana `Memo` program address. Within the `transact` block, 
add this code and remove `randomTransferTransaction`.

```tsx
import {TextEncoder} from 'text-encoding'; // TextEncoder library to convert stirng to buffer.

// Construct a message buffer from a string.
const message = "Hello Solana!";
const messageBuffer = new TextEncoder().encode(message) as Buffer

// Construct a 'Hello World' transaction and replace `randomTransferTransaction
const memoProgramTransaction = new Transaction({
    ...latestBlockhash,
    feePayer: authorizationResult.publicKey,
}).add(
    new TransactionInstruction({
    data: messageBuffer,
    keys: [],
    programId: new PublicKey(
        'MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr', // Memo Program address
    ),
    }),
);
```

### Request the wallet to sign and send a transaction

Next, instead of `wallet.signTransactions()`, now lets request our wallet to send the transaction for us, with `wallet.signAndSendTransactions()`. 

This is an optional feature that MWA wallets can choose to support, and `fakewallet` does support this. You can also directly use `connection`
to [send and confirm a transaction](https://solanacookbook.com/references/basic-transactions.html#how-to-send-sol).

```tsx
// Changed to `signAndSendTransactions.` and pass in `memoProgramTransaction`.
const transactionSignatures = await wallet.signAndSendTransactions({
    transactions: [memoProgramTransaction],
});
```

### Confirm the transaction

Now, the last step is to confirm that the transaction was processed by the network. Add this to the send
of the `transact` session and return the both `[signature, confirmationRepsonse]`. Read the docs learn more about [transaction confirmation](https://docs.solana.com/developing/transaction_confirmation).

```tsx
// Add this step to confirm that the transaction was proccessed by the network.
const confirmationResponse = await connection.confirmTransaction({
    signature: signature,
    ...latestBlockhash,
});

return [signature, confirmationResponse];
```

### Add Solana Explorer link navigation

After transaction confirmation, we can now view the message on the Solana blockchain itself! To do so,
we'll use the public tool [Solana Explorer](https://explorer.solana.com/), construct an explorer URL, and prompt the user to
navigate to the link through an alert.

```tsx
// Show an alert with an explorer link when we have a confirmed memo transaction.
function showExplorerAlert(memoTransactionSignature: string, cluster: string) {
  const explorerUrl =
    'https://explorer.solana.com/tx/' +
    memoTransactionSignature +
    '?cluster=' +
    cluster;
  Alert.alert(
    'Success!',
    'Your message was successfully recorded. View your message on Solana Explorer:',
    [
      {text: 'View', onPress: () => Linking.openURL(explorerUrl)},
      {text: 'Cancel', style: 'cancel'},
    ],
  );
}
```


### Update the button onPress

Almost done! Now just update the `onPress` handler in the button component to call `sendMemo`, handle errors, and show the explorer URL.
Here is what the final code should look like at this step for `sendMemo` and the button component.


<Tabs>
<TabItem value="SendMemoButton" label="SendMemoButton">

```tsx
return (
    <Button
        title="Send Memo!"
        disabled={signingInProgress}
        onPress={async () => {
            if (signingInProgress) {
                return;
            }
            setSigningInProgress(true);
            try {
                const [memoTransactionSignature, confirmationResponse] = await sendMemo();
                const err = confirmationResponse.value.err;
                if (err) {
                console.log(
                    'Failed to record message:' +
                    (err instanceof Error ? err.message : err),
                );
                } else {
                    // APP_CLUSTER is either 'devnet', 'testnet', 'mainnet-beta'.
                    showExplorerAlert(memoTransactionSignature, APP_CLUSTER);
                }
            } finally {
                setSigningInProgress(false);
            }
        }}
    />
);
```

</TabItem>
<TabItem value="sendMemo" label="sendMemo">

```tsx
const sendMemo = useCallback(
    async (
        messageBuffer: Buffer,
    ): Promise<[string, RpcResponseAndContext<SignatureResult>]> => {
        const latestBlockhash = await connection.getLatestBlockhash();
        const signature = await transact(async (wallet: Web3MobileWallet) => {
        const authorizationResult = await authorizeSession(wallet);

        const memoProgramTransaction = new Transaction({
            ...latestBlockhash,
            feePayer: authorizationResult.publicKey,
        }).add(
            new TransactionInstruction({
            data: messageBuffer,
            keys: [],
            programId: new PublicKey(
                'MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr', // Memo Program address
            ),
            }),
        );

        const transactionSignatures = await wallet.signAndSendTransactions({
            transactions: [memoProgramTransaction],
        });
            return transactionSignatures[0];
        });

        // Add this step to confirm that the transaction was proccessed by the network.
        const confirmationResponse = await connection.confirmTransaction({
            signature: signature,
            ...latestBlockhash,
        });

        return [signature, confirmationResponse];
    },
    [authorizeSession, connection],
);
```

</TabItem>
</Tabs>

### Finishing touches

Last step. All that's left is to render the new `SendMemoButton` in the app's `MainScreen`. Just change the existing `SignTransactionButton` 
component into the `SendMemoButton` component and you're done!

Make sure you request an airdrop of SOL before pressing the `SendMemoButton`, as 
you need to pay a small fee to send transactions on the network.

**Congratulations!**

You've finished the tutorial and built your first mobile dApp! Play around with the new `SendMemoButton` and view your message on the explorer. Make sure, you request 
an airdrop of SOL before trying to send the transaction.

## Next steps

Explore guides and SDK references to learn more and create more advanced applications. Here are some links to explore:

### Sample App Collection
- If you want to see more examples of dApps, then check out this [curated list](/sample-apps/sample_app_overview) of Solana mobile sample apps. It also includes a more [robust version of the app](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/examples/example-react-native-app) built in this tutorial.

### Guides/References
- [web3.js Javascript SDK reference](https://solana-labs.github.io/solana-web3.js/)
- [Writing your own Solana programs](https://docs.solana.com/developing/on-chain-programs/overview)
- [Hello World Tutorial](/react-native/hello_world_tutorial): A lengthier tutorial that teaches how to write MWA UI components.







---

## Original File: hello_world_tutorial.md
---
--- 
title: Hello World React Native Tutorial
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

In this tutorial, we'll walk you through the process of setting up an Android React Native project and use the [Mobile Wallet Adapter Javascript library](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages) to build a simple user interface that allows you to connect to a mobile wallet, request an airdrop, and send a message to the Solana network. 

By the end of this tutorial, you'll have an understanding of how to use the Solana Mobile SDK to build dApps that can interact with the Solana Blockchain.

## What you will learn
- How to set up a React Native Android project and integrate the Mobile Wallet Adapter library.
- How to use Mobile Wallet Adapter to connect to an installed wallet app.
- How to connect to devnet, check your wallet balance, and request an airdrop of SOL.
- How to use the memo program to write your message to the network and see your message on the blockchain!

## Prerequisites
Read the [**prerequisite setup**](../developers/development-setup) guide before starting the tutorial. This tutorial will be using the [fakewallet](../developers/development-setup#3-install-a-wallet-app) app to test your app's integration with Mobile Wallet Adapter.

### Clone the tutorial repo

Clone the [tutorial repo](https://github.com/solana-mobile/tutorial-apps/tree/main/SolanaReactNativeTutorial) from github. 
```shell
git clone https://github.com/solana-mobile/tutorial-apps.git
cd SolanaReactNativeTutorial
```
There should be two folders:
- `SolanaReactNativeTutorialStarter`: A boilerplate app with the MWA packages/dependencies ready and starter code that we'll be building up throughout the tutorial.
- `SolanaReactNativeTutorialComplete`: The complete version of the app and the end product of the tutorial.

### First run

Move into the starter project directory, install dependencies, and try running the app.

```shell
cd SolanaReactNativeTutorialStarter && yarn install && npx react-native start
```

In the Metro bundler menu, select the android option to build and launch the app in your device. Make changes to `MainScreen.tsx` and you can see your app update immediately, due to React Native's [*Fast Refresh*](https://reactnative.dev/docs/fast-refresh) feature.

## Connect to a wallet

Wallet apps manage your wallet's private key and can do actions like signing and sending transactions/messages. You will learn how use the [Mobile Wallet Adapter JS library](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages/mobile-wallet-adapter-protocol) to connect your dApp to the `fakewallet` app.

### Build a connect button
In `ConnectButton.tsx`:
- Use the `transact` function to start a session with a wallet app. 
- Then within the session, request wallet authorization for the dApp. 
- Save the results of authorization in the parent component's state.

After successful authorization, the dApp receives an `AuthorizationResult` object from `authorize`. It contains a list of `accounts` and an `authToken`.
Each account object contains useful information like the account's address (or `publicKey`) and account label. The `authToken` will be used for `reauthorization` in future `transact`'s with the wallet. 

In `MainScreen.tsx`:
- Render the `ConnectButton` component below the `ScrollView`.
- Create `authorization` state and `setAuthorization` within `onConnect`.

<Tabs>
<TabItem value="ConnectButton" label="ConnectButton">

```tsx
export default function ConnectButton({onConnect}: ConnectButtonProps) {
  const onPress = async () => {
    await transact(async wallet => {
      // Transact starts a session with the wallet app during which our app
      // can send actions (like `authorize`) to the wallet.
      const authResult: AuthorizationResult = await wallet.authorize({
        cluster: 'devnet',
        identity: APP_IDENTITY,
      });
      const {accounts, auth_token} = authResult;

      // After authorizing, store the authResult with the onConnect callback we pass into the button
      onConnect({
        address: accounts[0].address,
        label: accounts[0].label,
        authToken: auth_token,
        publicKey: getPublicKeyFromAddress(accounts[0].address),
      });
    });
  };
  return (
    <TouchableOpacity style={styles.button} onPress={onPress}>
      <Text style={styles.buttonText}>Connect Wallet</Text>
    </TouchableOpacity>
  );
}
```

</TabItem>
<TabItem value="MainScreen" label="MainScreen">

```tsx
export default function MainScreen() {
  const {connection} = useConnection();
  const [message, setMessage] = useState<string>('');
  const [authorization, setAuthorization] = useState<Authorization | null>(
    null,
  );
  return (
    <>
      <View style={styles.mainContainer}>
        <ScrollView contentContainerStyle={styles.scrollContainer}>
          <View style={styles.header}>
            <Text style={styles.headerText}>Hello Solana!</Text>
          </View>

          {/* Text Input */}
          <View>
            <Text style={styles.inputHeader}>What's on your mind?</Text>
            <TextInput
              style={styles.input}
              numberOfLines={1}
              onChangeText={text => setMessage(text)}
              placeholder="Write your message here"
            />
          </View>
        </ScrollView>
        <ConnectButton
          onConnect={async (authorization: Authorization) => {
            setAuthorization(authorization);
          }}
        />
      </View>
    </>
  );
}
```

</TabItem>
</Tabs>

### Build a disconnect button

We also want to give the users the option to disconnect their wallet from the app. We'll use a `deauthorize` request to invalidate the provided `authToken`.

In `DisconnectButton.tsx`:
- Pass in the stored `authorization` and, within a `transact` session, send a `deauthorize` request to the wallet for the stored `authToken`.

In `MainScreen.tsx`:
- Conditionally render the `ConnectButton` or `DisconnectButton`.
- Pass in `onDisconnect` as props, setting the stored `authorization` state to null.

<Tabs>
<TabItem value="DisconnectButton" label="DisconnectButton">

```tsx
export default function DisconnectButton({
  onDisconnect,
  authorization,
}: DisconnectButtonProps) {
  const onPress = async () => {
    await transact(async wallet => {
      // The deauthorize request will invalidate the authToken.
      await wallet.deauthorize({
        auth_token: authorization.authToken,
      });
      // Set stored authorization state to null through onDisconnect callback
      onDisconnect();
    });
  };
  return (
    <TouchableOpacity style={styles.button} onPress={onPress}>
      <Text style={styles.buttonText}>Disconnect Wallet</Text>
    </TouchableOpacity>
  );
}

```

</TabItem>
<TabItem value="MainScreen" label="MainScreen">

```tsx
export default function MainScreen() {
  const {connection} = useConnection();
  const [message, setMessage] = useState<string>('');
  const [authorization, setAuthorization] = useState<Authorization | null>(
    null,
  );
  return (
    <>
      <View style={styles.mainContainer}>
        <ScrollView contentContainerStyle={styles.scrollContainer}>
          <View style={styles.header}>
            <Text style={styles.headerText}>Hello Solana!</Text>
          </View>

          {/* Text Input */}
          <View>
            <Text style={styles.inputHeader}>What's on your mind?</Text>
            <TextInput
              style={styles.input}
              numberOfLines={1}
              onChangeText={text => setMessage(text)}
              placeholder="Write your message here"
            />
          </View>
        </ScrollView>

        {/* Conditionally render connect or disconnect, 
            depending on if a wallet is connected */}
        {authorization === null ? (
          <ConnectButton
            onConnect={async (authorization: Authorization) => {
              setAuthorization(authorization);
            }}
          />
        ) : (
          <DisconnectButton
            authorization={authorization}
            onDisconnect={() => {
              setAuthorization(null);
            }}
          />
        )}
      </View>
    </>
  );
}

```

</TabItem>
</Tabs>

## View wallet account balance
After connecting to a wallet, we'll use the `connection` class from `useConnection` to view your wallet account's SOL balance on devnet. 

In `MainScreen.tsx`:
- Create a `balance` state and call `connection.getBalance(authorization.publicKey)` to fetch the wallet balance from devnet.
- Create a `useEffect` hook, that calls the fetches and updates the balance once connected to a wallet.
- Conditionally render the `AccountInfo` component if connected to a wallet.

In `AccountInfoSection.tsx`:
- The starter code includes function `convertLamportToSOL` because the number returned from `getBalance` is in units of [lamport](https://docs.solana.com/terminology#lamport).

:::note
The starter code handles wrapping the app with a `ConnectionProvider` in `App.tsx`, enabling the `useConnection` hook.
:::

<Tabs>
<TabItem value="AccountInfoSection" label="AccountInfoSection">

```tsx
export default function AccountInfoSection({
  authorization,
  balance,
}: AccountInfoProps) {
  return (
    <View style={styles.container}>
      <View style={styles.textContainer}>
        <Text style={styles.walletBalance}>
          {balance !== null
            ? `Balance: ${convertLamportsToSOL(balance)} SOL`
            : 'Loading balance...'}
        </Text>
        <Text style={styles.walletName}>
          {authorization.label ?? 'Wallet name not found'}
        </Text>
        <Text style={styles.walletNameSubtitle}>{authorization.address}</Text>
      </View>
    </View>
  );
}

```
</TabItem>
<TabItem value="MainScreen" label="MainScreen">

```tsx
export default function MainScreen() {
  const {connection} = useConnection();
  const [message, setMessage] = useState<string>('');
  const [authorization, setAuthorization] = useState<Authorization | null>(
    null,
  );
  const [balance, setBalance] = useState<number | null>(null);

  const fetchAndUpdateBalance = async (authorization: Authorization) => {
    // The ConnectionProvider (in App.tsx) is set to the devnet endpoint.
    const balance = await connection.getBalance(authorization.publicKey);
    console.log('Balance fetched: ' + balance);
    setBalance(balance);
  };

  useEffect(() => {
    // Fetch and update balance, if connected to a wallet.
    if (!authorization) {
      return;
    }
    fetchAndUpdateBalance(authorization);
  }, [authorization]);

  return (
    <>
      <View style={styles.mainContainer}>
        <ScrollView contentContainerStyle={styles.scrollContainer}>
          <View style={styles.header}>
            <Text style={styles.headerText}>Hello Solana!</Text>
          </View>

          {/* Text Input */}
          <View>
            <Text style={styles.inputHeader}>What's on your mind?</Text>
            <TextInput
              style={styles.input}
              numberOfLines={1}
              onChangeText={text => setMessage(text)}
              placeholder="Write your message here"
            />
          </View>

          {/* Conditionally render if connected to a wallet. */}
          {authorization !== null ? (
            <AccountInfo authorization={authorization} balance={balance} />
          ) : null}
        </ScrollView>
        {authorization === null ? (
          <ConnectButton
            onConnect={async (authorization: Authorization) => {
              setAuthorization(authorization);
            }}
          />
        ) : (
          <DisconnectButton
            authorization={authorization}
            onDisconnect={() => {
              setAuthorization(null);
            }}
          />
        )}
      </View>
    </>
  );
}
```
</TabItem>
</Tabs>

## Request a SOL airdrop
If you try connecting to the wallet at this point, you'll notice that you have 0 SOL tokens in your balance. 
In order to send transactions to devnet, we'll need to fund the account by requesting an airdrop. 

With `RequestAirdropButton`, use `connection.requestAirdrop(...)` to request an airdrop to your wallet's address (public key).

Then in `MainScreen`, render the new button and pass in `fetchAndUpdateBalance(authorization)` to the `onAirdropComplete` prop.

:::note
Unfortunately, airdropping on devnet is prone to flakiness and a request can often fail. If you are seeing a transaction confirmation error
at this step, it's most likely because due to this instability.
:::

<Tabs>
<TabItem value="RequestAirdropButton" label="RequestAirdropButton">

```tsx
export default function RequestAirdropButton({
  authorization,
  onAirdropComplete,
}: AccountInfoProps) {
  const {connection} = useConnection();

  const requestAirdrop = async () => {
    // SOL/Lamports will be airdropped to the wallet's address (public key).
    // Use Promise.all to also fetch the latest block hash in parallel.
    const [signature, latestBlockhash] = await Promise.all([
      connection.requestAirdrop(authorization.publicKey, LAMPORTS_PER_AIRDROP),
      connection.getLatestBlockhash(),
    ]);

    // Confirm that the airdrop was successful.
    return await connection.confirmTransaction({
      signature: signature,
      ...latestBlockhash,
    });
  };

  return (
    <TouchableOpacity
      style={styles.button}
      onPress={async () => {
        const result = await requestAirdrop();
        const error = result?.value?.err;
        if (error) {
          console.log(
            'Failed to fund account: ' +
              (error instanceof Error ? error.message : error),
          );
        } else {
          // Fetch and update balance if airdrop is successful
          onAirdropComplete(authorization);
        }
      }}>
      <Text style={styles.buttonText}>Request airdrop</Text>
    </TouchableOpacity>
  );
}
```
</TabItem>
<TabItem value="MainScreen" label="MainScreen">

```tsx
export default function MainScreen() {
  const {connection} = useConnection();
  const [message, setMessage] = useState<string>('');
  const [authorization, setAuthorization] = useState<Authorization | null>(
    null,
  );
  const [balance, setBalance] = useState<number | null>(null);

  const fetchAndUpdateBalance = async (authorization: Authorization) => {
    // The ConnectionProvider (in App.tsx) is set to the devnet endpoint.
    const balance = await connection.getBalance(authorization.publicKey);
    console.log('Balance fetched: ' + balance);
    setBalance(balance);
  };

  useEffect(() => {
    // Fetch and update balance, after connecting to a wallet.
    if (!authorization) {
      return;
    }
    fetchAndUpdateBalance(authorization);
  }, [authorization]);

  return (
    <>
      <View style={styles.mainContainer}>
        <ScrollView contentContainerStyle={styles.scrollContainer}>
          <View style={styles.header}>
            <Text style={styles.headerText}>Hello Solana!</Text>
          </View>

          {/* Text Input */}
          <View>
            <Text style={styles.inputHeader}>What's on your mind?</Text>
            <TextInput
              style={styles.input}
              numberOfLines={1}
              onChangeText={text => setMessage(text)}
              placeholder="Write your message here"
            />
          </View>

          {/* Conditionally render if connected to a wallet. */}
          {authorization !== null ? (
            <AccountInfo authorization={authorization} balance={balance} />
          ) : null}
          <View style={styles.buttonGroup}>
            {authorization !== null ? (
              <RequestAirdropButton
                authorization={authorization}
                onAirdropComplete={(authorization: Authorization) => {
                  fetchAndUpdateBalance(authorization);
                }}
              />
            ) : null}
          </View>
        </ScrollView>
        {authorization === null ? (
          <ConnectButton
            onConnect={async (authorization: Authorization) => {
              setAuthorization(authorization);
            }}
          />
        ) : (
          <DisconnectButton
            authorization={authorization}
            onDisconnect={() => {
              setAuthorization(null);
            }}
          />
        )}
      </View>
    </>
  );
}
```
</TabItem>
</Tabs>

## Record a message to the blockchain
### Construct and send a transaction
After receiving an airdrop successfully, you should see your SOL balance update to `0.1`. You can now pay the fee to send a transaction to record the message on the network. To do so, we'll be invoking an [on-chain program](https://docs.solana.com/developing/intro/programs#on-chain-programs) called the [`MemoProgram`](https://spl.solana.com/memo).

In `RecordMessageButton.tsx`, create a function `recordMessage` that:
- Constructs the `MemoProgram` Transaction.
- Sends a `signAndSendTransaction` request to the wallet.
- The wallet then signs the transaction with the private key and sends it to devnet.

<Tabs>
<TabItem value="recordMessage" label="recordMessage">

```tsx
// Takes in a `Buffer` type that represents the message string.
async function recordMessage(
  connection: Connection,
  authorization: Authorization,
  messageBuffer: Buffer,
): Promise<[string, RpcResponseAndContext<SignatureResult>]> {
  const [signature] = await transact(async wallet => {
    // Start a wallet session with `transact` and `reauthorize` our dApp by passing in the `authToken`.
    // Use Promise.all to also fetch the latest block hash in parallel.
    const [authResult, latestBlockhash] = await Promise.all([
      wallet.reauthorize({
        auth_token: authorization.authToken,
        identity: APP_IDENTITY,
      }),
      connection.getLatestBlockhash(),
    ]);

    // Construct a `Transaction` with an instruction to invoke the `MemoProgram`.
    const memoProgramTransaction = new Transaction({
      ...latestBlockhash,
      feePayer: authorization.publicKey,
    }).add(
      new TransactionInstruction({
        data: messageBuffer,
        keys: [],
        programId: new PublicKey(
          'MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr', // Memo Program address
        ),
      }),
    );

    // Send a `signAndSendTransactions` request to the wallet. The wallet will sign the transaction with the private key and send it to devnet.
    return await wallet.signAndSendTransactions({
      transactions: [memoProgramTransaction],
    });
  });

  const latestBlockhash = await connection.getLatestBlockhash();
  return [
    signature,
    await connection.confirmTransaction({
      signature: signature,
      ...latestBlockhash,
    }),
  ];
}
```

</TabItem>
<TabItem value="RecordMessageButton" label="RecordMessageButton">

```tsx
export default function RecordMessageButton({
  authorization,
  message,
}: RecordMessageButtonProps) {
  const {connection} = useConnection();
  const buttonDisabled = message === null || message.length === 0;
  const buttonStyle = buttonDisabled ? styles.disabled : styles.enabled;
  return (
    <TouchableOpacity
      disabled={buttonDisabled}
      style={[styles.button, buttonStyle]}
      onPress={async () => {
        const result = await recordMessage(
          connection,
          authorization,
          new TextEncoder().encode(message) as Buffer,
        );
        if (result) {
          const [signature, response] = result;
          const err = response.value.err;
          if (err) {
            console.log(
              'Failed to record message:' +
                (err instanceof Error ? err.message : err),
            );
          } else {
            const explorerUrl =
              'https://explorer.solana.com/tx/' +
              signature +
              '?cluster=' +
              WalletAdapterNetwork.Devnet;
            console.log(
              'Successfully recorded a message. View your message at: ' +
                explorerUrl,
            );
            // TODO: Add an alert to give the user an option to click the link.
          }
        }
      }}>
      <Text style={styles.buttonText}>Record message</Text>
    </TouchableOpacity>
  );
}
```

</TabItem>
</Tabs>

### View your message on explorer
If this transaction is successful, you can use the [Solana Explorer](https://explorer.solana.com/) to see your message on the blockchain itself.

On the success case, add an `Alert` to give the user the option to click a link and navigate to the `explorerUrl`.

```tsx
Alert.alert(
       'Success!',
       'Your message was successfully recorded. View your message on Solana Explorer:',
    [
       { text: 'View', onPress: () => Linking.openURL(explorerUrl) },
       { text: 'Cancel', style: 'cancel' },
    ]
);
```

You should now be seeing the alert after clicking the `RecordMessageButton`.

***Congratulations!***

You've successfully recorded your message onto the Solana blockchain and created a functioning Solana Mobile dApp!  🎉


## Next steps

Explore guides and SDK references to learn more and create more advanced applications. Here are some links to explore:

### Sample App Collection
- If you want to see more examples of dApps, then check out this [curated list](../sample-apps/sample_app_overview) of Solana mobile sample apps. It also includes a more [robust version of the app](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/examples/example-react-native-app) built in this tutorial.

### Guides/References
- [web3.js Javascript SDK reference](https://solana-labs.github.io/solana-web3.js/)
- [Writing your own Solana programs](https://docs.solana.com/developing/on-chain-programs/overview)




---

## Original File: mobile_nft_minter_tutorial.md
---
# Mobile NFT Minter Tutorial

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CTAButton from "../../src/components/CTAButton";

In this tutorial, you will learn how to build a React Native dApp that can mint NFTs from your phone photos.

<p float="left">
  <img src="/tutorial_imgs/nftminter1.png" alt="Saga Photo 3" width="33%" style={{width: '33%', padding: '1%'}} />
  <img src="/tutorial_imgs/nftminter2.gif" alt="Saga Photo 3" width="33%" style={{width: '33%', padding: '1%'}} />
  <img src="/tutorial_imgs/nftminter3.png" alt="Saga Photo 3" width="33%" style={{width: '33%', padding: '1%'}} />
</p>

View or download the complete open-source program on Github, or follow this tutorial to build the application from scratch.

<CTAButton label="View on Github" to="https://github.com/solana-mobile/tutorial-apps/tree/main/MobileNFTMinter" />

## What you will learn  
- Integrate the Metaplex JS in a React Native project.
- Use an MWA Identity Signer with Metaplex.
- How to read image files from the OS file system.
- How to upload image and metadata files to IPFS, through NFT.storage.
- Create and mint a Metaplex NFT on-chain.

## Prerequisites
- Complete [prerequisite setup](../developers/development-setup) for a ready dev environment.
- An Android device/emulator to build and launch a React Native app
- An MWA-compatible wallet installed on the same device/emulator.
- Basic understanding of MWA.

## Project Setup

### 1. Initialize Scaffold

This tutorial builds off the [dApp Scaffold template](https://github.com/solana-mobile/solana-mobile-dapp-scaffold). This template
comes setup with the essential packages like MWA and web3.js.

Initialize the scaffold using the following command:

```shell
npx react-native init MobileNFTMinter --template @solana-mobile/solana-mobile-dapp-scaffold --npm
cd MobileNFTMinter
```

:::note
The `--npm` flag is only necessary if you're using newer versions of Yarn (Yarn 2 or Yarn 3).
After running the command, you can simply delete the `package-lock.json` generated and continue using
Yarn.
:::

Next, install the dependencies:

<Tabs>
<TabItem value="yarn" label="yarn">

```shell
rm package-lock.json
yarn install
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
npm install
```

</TabItem>
</Tabs>

### 2. Install Metaplex JS SDK

The next step is to install the `@metaplex-foundation/js` package. This is the Metaplex JS SDK that provides
a developer friendly API to interact with onchain programs.

Carefully follow the [Metaplex installation](../react-native/metaplex_integration#installation) steps
here to make sure you install the package along with all the necessary polyfill libraries.

### 3. Install dependencies

In addition to the Metaplex JS SDK, the app will use several other libraries that handle other usecases, like file reading,
IPFS, and others. Some of these are opinionated, so feel free to swap out a library with one of your choice.

<details>
<summary>A brief overview of each dependency:</summary>

- [`rn-fetch-blob`](https://github.com/joltup/rn-fetch-blob): A React Native file system reader for converting photos to bytes.
- [`react-native-image-picker`](https://github.com/react-native-image-picker/react-native-image-picker): Provides a convenient Photo selector UI.
- [`react-native-config`](https://github.com/luggit/react-native-config):Used to store and expose the NFT.storage API key to Javascript.
- [`multiformats`](https://github.com/multiformats/js-multiformats): A low-level JS library that provides an API/interface to compute CIDs in IPFS format.

</details>

<Tabs>
<TabItem value="yarn" label="yarn">

```shell
yarn install \
    rn-fetch-blob \
    react-native-image-picker \
    react-native-config \
    multiformats
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
npm install \
    rn-fetch-blob \
    react-native-image-picker \
    react-native-config \
    multiformats
```

</TabItem>
</Tabs>



### 4. Launch the app

```shell
npx react-native run-android
```

At this point, your app should build, install into your device, and launch automatically. If you are seeing errors
about missing/undefined methods, double check you installed the polyfills correctly.

## How does minting work?

The end to end procedure of minting a photo NFT roughly follows these steps:
1. Select a photo and upload it to a storage provider.
2. Upload a JSON object containing metadata that conforms to the [Metaplex NFT Standard](https://docs.metaplex.com/programs/token-metadata/token-standard#the-non-fungible-standard), to a storage provider.
3. Submit a transaction to the network that creates your NFT on chain.

## Uploading to IPFS with NFT.storage

In this tutorial, we choose [IPFS](https://ipfs.tech/), a decentralized storage provider, to host the selected photo and the metadata object. 

We'll also be using [NFT.storage](https://nft.storage/docs/) to help upload directly to IPFS, through their [HTTP API](https://nft.storage/docs/client/http/).
You can sign up for a free API key on their website.


### Selecting the photo

You need to select an existing photo from our gallery. To present a picker UI and retrieve the file path, use `launchImageLibrary` from the `react-native-image-picker` library.

:::info
In the example app, this is done within the `NftMinter` component where `handleSelectImage` is called on a [button press](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/components/NftMinter.tsx#L44).

We save the image path as state, to be used in the next step.
:::

```tsx
import {launchImageLibrary} from 'react-native-image-picker';

const photo = await launchImageLibrary({
    selectionLimit: 1,
    mediaType: 'photo',
});
const selectedPhoto = photo?.assets?.[0];
if (!selectedPhoto?.uri) {
    console.warn('Selected photo not found');
    return;
}
const imagePath = selectedPhoto.uri;
```

### Upload the photo

Now that we have the image path, we need to upload the raw bytes of the file to IPFS, using the NFT.storage `/upload` endpoint.

The steps:
1. Use the `rn-fetch-blob` library to read the image file into a Base 64 string.
2. Convert to raw bytes by decoding the Base64 string with `Buffer`.
3. Use `fetch` to send a request containing the image bytes to the upload endpoint.

:::info
In the example app, this is handled in a separate helper function [`uploadToIPFS`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/ipfs/uploadToIPFS.ts#L7), which is called
later within the larger the [`mintNft`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/components/NftMinter.tsx#L61) function. 
:::
:::caution
During this step, you'll need to provide your own API key from NFT.storage. In the example app, the `NFT_STORAGE_API_KEY` value is set through
an environment variable config, using the `react-native-config` library.
:::

```tsx
// Read the image file and get the base64 string.
const imageBytesInBase64: string = await RNFetchBlob.fs.readFile(
    imagePath,
    'base64',
);

// Convert base64 into raw bytes.
const bytes = Buffer.from(imageBytesInBase64, 'base64');

// Upload the image to IPFS by sending a POST request to the NFT.storage upload endpoint.
const headers = {
    Accept: 'application/json',
    Authorization: `Bearer ${Config.NFT_STORAGE_API_KEY}`,
};
const imageUpload = await fetch('https://api.nft.storage/upload', {
    method: 'POST',
    headers: {
        ...headers,
        'Content-Type': 'image/jpg',
    },
    body: bytes,
});

const imageData = await imageUpload.json();
console.log(imageData.value.cid);
```

If successful, the `imageData.value.cid` will contain a valid [CID (Content Identifier)](https://docs.ipfs.tech/concepts/content-addressing/). This is a string that
uniquely identifies your uploaded asset. 

You can view your uploaded asset on an [IPFS gateway](https://docs.ipfs.tech/concepts/ipfs-gateway/) by passing in the CID in the URL (e.g: `https://ipfs.io/ipfs/<cid>`). 
View an [example](https://ipfs.io/ipfs/bafkreicdv4jt7oaah73kvjfnm4f2yd5klbnyehlkpi33kxjakdo6encepe) of an uploaded photo on ipfs.io.

### Uploading the metadata 
Next, we need to construct a metadata object that conforms to the [Metaplex NFT Standard](https://docs.metaplex.com/programs/token-metadata/token-standard#the-non-fungible-standard), then
upload it to the same `/upload` endpoint.

Metadata fields:
- Name: The name of the NFT.
- Description: A description of the NFT.
- Image: A URL that hosts the photo. In this case, we use an `ipfs.io` URL with the CID of the uploaded photo.

:::info
In the example app, the metadata upload step is also handled within the [`uploadToIPFS`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/ipfs/uploadToIPFS.ts#L37) function.

There is a slight difference that should be noted. The `image` field uses a precomputed CID for the photo, rather than waiting for the photo upload to finish. 
This is an optimization that is explained in the next section.
:::

```tsx
// Construct the metadata fields.
const metadata = JSON.stringify({ 
    name,
    description,
    image: `https://ipfs.io/ipfs/${imageData.value.cid}`,
});
// Upload to IPFS
const metadataUpload = await fetch('https://api.nft.storage/upload', {
    method: 'POST',
    headers: {
        ...headers,
        'Content-Type': 'application/json',
    },
    body: metadata,
});

const metadataData = await metadataUpload.json();
console.log(metadataData.value.cid);
```

If successful, `metadataData.value.cid` will now contain a CID that points to a JSON object representing the NFT metadata. View an [example](https://ipfs.io/ipfs/bafkreidbymwcjxntxak7wkxvblzgtaivg2ktef47i3nfcqtbw4but5ufhe) of an uploaded metadata object.

To recap, we now have two CIDs that are viewable on IPFS. First, the CID of our uploaded photo, and second, the CID of
JSON Metadata (which has a reference to the photo CID in the `image` field).

### Precomputing the CID

You may notice in the example app, that during the upload step in [`uploadToIPFS`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/ipfs/uploadToIPFS.ts#L50)
we're able to precompute the CID of the photo asset before actually uploading it to IPFS. This is an optimization that allows us to
construct and upload the metadata object, without waiting for the photo upload to complete and return the CID.

We take advantage of this by uploading both the photo and metadata asynchronously.
```tsx
// Fire off both uploads aysnc
return Promise.all([
    imageUpload.then(response => response.json()),
    metadataUpload.then(response => response.json()),
]);
```

:::tip
This is made possible because CIDs are generated deterministically from the binary data of any given asset. This mean we can compute the CID of an asset before
uploading it to IPFS.
:::

To compute the CID from the bytes of a given asset, see the [`getCid`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/ipfs/getCid.ts) function.

```tsx
import {CID, hasher} from 'multiformats';
const crypto = require('crypto-browserify');

const SHA_256_CODE = 0x12;
const IPLD_RAW_BINARY_CODE = 0x55;

const getCid = async (bytes: Buffer) => {
  const sha256 = hasher.from({
    // As per multiformats table
    // https://github.com/multiformats/multicodec/blob/master/table.csv#L9
    name: 'sha2-256',
    code: SHA_256_CODE,
    encode: input =>
      new Uint8Array(crypto.createHash('sha256').update(input).digest()),
  });
  const hash = await sha256.digest(bytes);
  const cid = await CID.create(1, IPLD_RAW_BINARY_CODE, hash);

  return cid;
};
```

## Minting the NFT

At this point we have completed the IPFS uploading steps and all that is left is to mint the NFT on chain. To do so,
we'll use the Metaplex JS SDK.

### Create a Metaplex Instance

To interact with Metaplex onchain programs, instantiate a `Metaplex` instance provided by the SDK.

Follow this [section](../react-native/metaplex_integration#using-mwa-as-an-identity-driver) in the Metaplex guide, to create an MWA Identity Signer plugin. We'll need
this so that the `Metaplex` instance will be able to request wallet signing through MWA.

:::info
In the example app, this is handled in two files: 
- [`mwaPlugin`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/metaplex-util/mwaPlugin.ts): A helper file that installs a `MetaplexPlugin` using MWA as an identity signer.
- [`useMetaplex`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/metaplex-util/useMetaplex.tsx): A React hook that vends a `Metaplex` instance with
the `mobileWalletAdapterIdentity` installed.
:::

Like in the example app, create the `Metaplex` instance with the `useMetaplex` hook.
```tsx
import useMetaplex from '../metaplex-util/useMetaplex';

const {metaplex} = useMetaplex(connection, selectedAccount, authorizeSession);
```

### Create the NFT

With the `metaplex` instance, we can now access the `nfts()` module that provides [a collection of functions](https://docs.metaplex.com/programs/token-metadata/getting-started#javascript-sdk) that
make it simple to interact with on chain programs and submit transactions.

To mint an NFT, call the `create` function which takes in a JSON object corresponding to the [Token Metadata Standard](https://docs.metaplex.com/programs/token-metadata/changelog/v1.0#token-metadata-program).

This will prompt the user to sign a transaction using MWA, then submit the transaction to the specified RPC.

:::info
In the example app, this step happens at the end of [`mintNft`](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/components/NftMinter.tsx#L84C1-L90C59). 
We return the NFT address and present a [clickable explorer link](https://github.com/solana-mobile/tutorial-apps/blob/main/MobileNFTMinter/components/NftMinter.tsx#L159).
:::

```tsx
const {nft, response} = await metaplex.nfts().create({
    name: nftName,
    uri: `https://ipfs.io/ipfs/${metadataUploadData.value.cid}`,
    sellerFeeBasisPoints: 0,
    tokenOwner: selectedAccount?.publicKey,
});

console.log(nft.address.toBase58())
console.log(response.signature)
```

**Congrats!** 

Your NFT should now be minted and viewable on chain! You can view it on a block explorer by pasting in the String from `nft.address.toBase58()`.