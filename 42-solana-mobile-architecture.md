# 42 Solana Mobile Architecture

## Original File: mobile-dapp-architecture.md
---
# Mobile dApp Architecture Overview

This reference aims to give a conceptual overview of the architecture of a mobile dApp using the Solana Mobile Stack.

## High Level

The following diagram shows a bird's eye view of a mobile dApp's interactions with mobile wallets and the Solana network.

  <img src="/diagrams/dapp_architecture_full.svg" alt="Full Architecture Diagram" className="diagram-image"/>

## Mobile Wallet Adapter Protocol

The **Mobile Wallet Adapter (MWA) ** protocol is what defines the communication exchange between a dApp and a mobile wallet.
In the protocol, the dApp sends requests (i.e: authorization or signing), while the wallet is responsible for displaying
these requests to the user and responding back to the dApp if approved.

For an extensive, deep dive into the specifics of the protocol and MWA methods, refer to the [MWA spec](https://solana-mobile.github.io/mobile-wallet-adapter/spec/spec.html).

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

## Original File: web3-and-mobile.md
---
# Web3 and Mobile

Before development, its important to understand the current landscape of Web3 mobile development to foresee potential blockers and opportunities.

## Mobile Platforms 📱

**Android** 🤖

Android is currently the best supported platform for Solana Mobile development. The Solana Mobile SDKs (namely Mobile Wallet Adapter) are all natively implemented and available in Android. The majority of the resources in this website are for Android development.

In addition to this, the Google Play Store has less restrictive policies with regards to Crypto/Web3 dApps compared to the Apple App Store.

For Android development, Solana Mobile SDKs available for **Kotlin, React Native, and Flutter**. See our _SDK_ section for more details on each.

**iOS** 🍎

There are several blockers for developing Solana mobile apps on iOS. First, the Mobile Wallet Adapter protocol is not supported on iOS due to technical limitations of the operating system. In addition to this, the Apple App Store has a more restrictive policy against web3/crypto apps.

For now, the current recommendation for iOS is to create a mobile web app, that is — a web app designed/optimized for mobile browsers. More on this below.

## **Mobile Web** 🌐

Although our recommendation is to always create a native mobile app for the best user experience, this is not always possible like on iOS. An alternative option to developing a native mobile app is to create a web app optimized for a mobile browser experience.

**Browser Compatibility**

- Android Chrome ✅
  - Supports MWA automatically if using the [`wallet-adapter-react`](https://github.com/solana-labs/wallet-adapter) library.
- iOS Safari 🟨
  - Does not support MWA due to iOS limitations.
  - Wallet signing only possible if user has a Safari Extension wallet, like [Glow Wallet](https://glow.app/).
- Firefox, Opera, Brave, Other ❌
  - Does not support MWA on either iOS or Android.
- Wallet In-App-Browsers
  - Mobile wallets like Phantom, Solflare, and Ultimate, also provide an option to load a web dApp through an in-app-browser.
  - In-App-Browsers have wallet signing capabilities at the cost of being restricted within the wallet app.
