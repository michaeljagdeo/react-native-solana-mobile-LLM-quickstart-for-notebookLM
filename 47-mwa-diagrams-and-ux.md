# 47 Mwa Diagrams And Ux

## Original File: diagrams.md
---
# Diagrams

This reference aims to give a conceptual overview of the architecture of the **Mobile Wallet Adapter (MWA) protocol**.

## Mobile Wallet Adapter Protocol

The Mobile Wallet Adapter protocol is what defines the communication exchange between a dApp and a mobile wallet app.

In the protocol, the dApp sends requests (e.g. `authorize`, `sign_transactions`), while the wallet is responsible for displaying
these requests to the user and responding back to the dApp if approved.

For an extensive, deep dive into the specifics of the protocol and MWA methods, refer to the [MWA spec](https://solana-mobile.github.io/mobile-wallet-adapter/spec/spec.html).

### High Level

The following diagram shows a bird's eye view of a mobile dApp's interactions with mobile wallets and the Solana network.

  <img src="/diagrams/dapp_architecture_full.svg" alt="Full Architecture Diagram" className="diagram-image"/>

### Session Establishment

To begin the protocol, a dApp initiates first contact with a mobile wallet and establishes an **MWA session**.
With the current SDKs, the MWA session is initiated through Android intents, with the dApp broadcasting an intent
with the `solana-wallet://` scheme.

  <img src="/diagrams/session_establishment.svg" alt="Session Establishment Diagram" className="diagram-image"/>
  <br /><br />

A wallet then receives the intent and starts a websocket connection, thus establishing a channel for commmunication.

### Example: Authorize and Sign Transaction

Once a session is established, the dApp can now begin sending MWA Requests to receive signed transactions from the wallet.
This example case outlines an MWA session where the dApp:

1. Establishes a session with a wallet.
2. Requests authorization, elevating the session to an "authorized state" and receiving a list of authorized accounts and an authToken.
3. Requests transaction signing, receiving a transaction signed by the authorized accounts.

<img src="/diagrams/authorize_and_sign.svg" alt="Authorize and Sign Diagram" className="diagram-image"/>

In future sessions, the dApp can initiate with the valid authToken to immediately elevate to an "authorizd state", skipping the "connect" step.

While the protocol technically supports multiple accounts, most wallet apps only implement a single account authorization per session.

For a more detailed diagram that shows the full communication exchange, refer to this [section in the spec](https://solana-mobile.github.io/mobile-wallet-adapter/spec/spec.html#authorize-and-sign-transaction).

## Submitting to the Solana network

Just like web dApps, the process for a mobile dApp submitting transactions to the blockchain network is the same. The dApp specifies
a cluster and an RPC endpoint then sends the transaction payload, following the [JSON RPC API](https://docs.solana.com/api).

For certain usecases, the dApp may choose to communicate with the RPC through the [Websocket API](https://docs.solana.com/api/websocket)

  <img src="/diagrams/submit_rpc.svg" alt="Submit to RPC Diagram" className="diagram-image"/>

### Sign and Send Transaction

For submitting transactions, it is encouraged for the dApp to send a `sign_and_send_transaction` MWA request to the wallet. This request
type sends a unsigned transaction to the wallet. If authorized, the wallet will then sign the transaction and send it to the network with its
own implementation. Depending on the wallet app, this can include applying its own priority fee.

Relying on the wallet reduces the risk of replay attacks with [durable nonce based transactions](https://docs.solana.com/implemented-proposals/durable-tx-nonces), which can be abused with `sign_transactions`.

  <img src="/diagrams/sign_and_send.svg" alt="Sign and Send Diagram" className="diagram-image"/>


---

## Original File: ux-guidelines.md
---
# Mobile Wallet Adapter UX Guidelines

This guide will cover the best practices for using Mobile Wallet Adapter (MWA) in your web app.

This guide assumes:
- You are using `@solana/wallet-adapter-react`
- The user is browsing on an **Android Web environment**, where MWA is usually the only available wallet.

## Call `connect()` directly

You should explicitly handle two scenarios:

1. If MWA is already selected, you should always directly call `connect`.

2. If it is not selected, but available, `select` it as early as possible in your UI flow. 

This will also fix connection related issues with Mobile Wallet Adapter.

### Example: Connect Button

```typescript
import { useWallet } from '@solana/wallet-adapter-react'
import { useWalletModal } from '@solana/wallet-adapter-react-ui';
import { SolanaMobileWalletAdapterWalletName } from '@solana-mobile/wallet-standard-mobile'

export default function ConnectButton() {
    const { connected, wallet, wallets } = useWallet();
    const { setVisible: showWalletSelectionModal } = useWalletModal();

    // MWA is only available when user is on Android Web environments (e.g Android Chrome).
    const mobileWalletAdapter = wallets.find((wallet) => wallet.adapter.name === SolanaMobileWalletAdapterWalletName)

    const handleConnectClick = () => {
        if (wallet?.adapter?.name === SolanaMobileWalletAdapterWalletName) {
            // If already selected, immediately connect.
            await wallet?.adapter.connect();
        } else if (mobileWalletAdapter) {
            // If MWA is not selected, but available, select it instead of showing modal.
            select(SolanaMobileWalletAdapterWalletName)
        } else {
            // Else, show modal as usual.
            showWalletSelectionModal(true)
        }
    }
    return <Button disabled={connected} onClick={handleConnectClick} />;
}
```

## Change the displayed name

Throughout your UI, use the text `Use Installed Wallet` as the displayed name for the MWA option. 

This descriptive text helps your users understand that this option will allow them to connect to an installed mobile wallet app (via MWA).

### Example: Wallet List Item Component

```typescript
import { SolanaMobileWalletAdapterWalletName } from '@solana-mobile/wallet-standard-mobile'

export default function WalletListItem({ wallet, onPress }){
    // If we are showing MWA, use a descriptive display name.
    const displayName = (wallet.adapter.name === SolanaMobileWalletAdapterWalletName) 
                        ? `Use Installed Wallet` : wallet.adapter.name
    return (
    <Button onClick={onPress}>
        {displayName}
    </Button>
    );
};
```


---

## Original File: web-installation.md
---
# Installing Mobile Wallet Standard 


Use the **Mobile Wallet Standard** library to register Mobile Wallet Adapter as a wallet option into your web application.

## Installation

### 1. Install the package

You can add Mobile Wallet Adapter to your web application by installing:

```shell    
npm install @solana-mobile/wallet-standard-mobile
```

### 2. Register the wallet

In the root of your web application, invoke the `registerMwa` function.

:::caution
Ensure `registerMwa` is invoked in a non-SSR context. if you're using a framework with Server Side Rendering (e.g Next.js), 
:::

```typescript
// 'use client' - If using Next.js, ensure it is registered in a non-SSR context.

import { 
    createDefaultAuthorizationCache, 
    createDefaultChainSelector, 
    createDefaultWalletNotFoundHandler,
    registerMwa, 
} from '@solana-mobile/wallet-standard-mobile';

registerMwa({
    appIdentity: {
      name: 'My app',
      uri: 'https://myapp.io',
      icon: 'relative/path/to/icon.png', // resolves to https://myapp.io/relative/path/to/icon.png
    },    
    authorizationCache: createDefaultAuthorizationCache(),
    chains: ['solana:devnet', 'solana:mainnet'],
    chainSelector: createDefaultChainSelector(),
    onWalletNotFound: createDefaultWalletNotFoundHandler(),
    remoteHostAuthority: '<REPLACE_WITH_URL_>', // Include to enable remote connection option.
})
```

Once registered, the wallet behavior automatically adapts to the user's device:

- **Desktop**: If `remoteHostAuthority` is provided, remote connection via QR Code.
- **Mobile**: Local connection via Android Intents (same as native Android apps).


## Enable remote connection

To enable the remote connection for desktop viewers, you need to configure the `remoteHostAuthority` parameter in the register function. If not provided, the wallet will only register on mobile environments.

:::info
The `remoteHostAuthority` is a URL that points to a [*reflector WebSocket server*](https://solana-mobile.github.io/mobile-wallet-adapter/spec/spec.html#reflector-protocol) endpoint that reflects all communication between the web app and the wallet app. 

For more information, view the Reflector protocol documentation.
:::



### 1. Get the endpoint URL

Solana Mobile maintains a public endpoint for a reflector server (endpoint details to be announced).

If needed, dApps can implement and self-host their own reflector server. More information on this to come.
<!-- For more information, view the Hosting a Reflector server documentation. TODO -->

### 2. Configure your application

Once you've accepted the terms, add the URL in your register function with the URL.

Example with a configured endpoint:

```typescript
registerMwa({
  // ...other configuration options
  remoteHostAuthority: "<REPLACE_WITH_ENDPOINT>",
  // ...remaining options
})
```

After configured, MWA Remote connection will now appear as a wallet option on desktop environments.
