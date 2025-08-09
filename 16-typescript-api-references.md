# 16 Typescript Api References

## Original File: mobile-wallet-adapter.md
---
---
title: Mobile Wallet Adapter Typescript Reference
hide_table_of_contents: true
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CardLayout from "../../../src/layouts/CardLayout";
import CTAButton from "../../../src/components/CTAButton";

Connect to wallet apps and sign transactions and messages with the Mobile Wallet Adapter API.

<CTAButton label="API Reference" to="#mobile-wallet-api-methods" />

<br/>

:::tip
Mobile Wallet Adapter 2.0 is the newest and current version of the Mobile Wallet Adapter protocol.

The complete 2.0 spec is viewable [here](https://solana-mobile.github.io/mobile-wallet-adapter/spec/spec.html) and the legacy API is viewable [here](/reference/typescript/mobile-wallet-adapter-legacy).
:::

### Install dependencies

- [**`@solana-mobile/mobile-wallet-adapter-protocol`**](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages/mobile-wallet-adapter-protocol)

  - Base library that implements the MWA client. Include this, but only import `transact` from the wrapper library.

- [**`@solana-mobile/mobile-wallet-adapter-protocol-web3js`**](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages/mobile-wallet-adapter-protocol-web3js)
  - A convenience wrapper for the base library that enables with `web3.js` primitives like `Transaction`.

<Tabs>
<TabItem value="yarn" label="yarn">

```shell
yarn add \
  @solana-mobile/mobile-wallet-adapter-protocol-web3js \
  @solana-mobile/mobile-wallet-adapter-protocol \
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
npm install \
  @solana-mobile/mobile-wallet-adapter-protocol-web3js \
  @solana-mobile/mobile-wallet-adapter-protocol \
```

</TabItem>
</Tabs>

### Import into a file

```tsx
import {
  transact,
  Web3MobileWallet,
} from "@solana-mobile/mobile-wallet-adapter-protocol-web3js";
```

<br/><br/>

## Mobile Wallet API Methods

Reference for the Mobile Wallet API for dApps to connect to wallets and receive signing services.

import Transact from "./mobile-wallet-methods/\_transact.mdx"

<Transact />

import WalletAuthorize from "./mobile-wallet-methods/\_walletAuthorize.mdx"

<WalletAuthorize />

import WalletDeauthorize from "./mobile-wallet-methods/\_walletDeauthorize.mdx"

<WalletDeauthorize />

import WalletGetCapabilities from "./mobile-wallet-methods/\_walletGetCapabilities.mdx"

<WalletGetCapabilities />

import WalletSignAndSendTransactions from "./mobile-wallet-methods/\_walletSignAndSendTransactions.mdx"

<WalletSignAndSendTransactions />

import WalletSignTransactions from "./mobile-wallet-methods/\_walletSignTransactions.mdx"

<WalletSignTransactions />

import WalletSignMessages from "./mobile-wallet-methods/\_walletSignMessages.mdx"

<WalletSignMessages />


---

## Original File: mobile-wallet-adapter-legacy.md
---
---
title: MWA Typescript Reference (Legacy)
hide_table_of_contents: true
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CardLayout from "../../../src/layouts/CardLayout";
import CTAButton from "../../../src/components/CTAButton";

Connect to wallet apps and sign transactions and messages with the Mobile Wallet Adapter API.

<CTAButton label="API Reference" to="#mobile-wallet-api-methods" />

<br/>

:::caution
This document references than older version of the Mobile Wallet Adapter protocol. Although the SDK and wallet apps are backwards compatible, it is
recommended for dApps to use the [new API](/reference/typescript/mobile-wallet-adapter).
:::

### Install dependencies

- [**`@solana-mobile/mobile-wallet-adapter-protocol`**](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages/mobile-wallet-adapter-protocol)

  - Base library that implements the MWA client. Include this, but only import `transact` from the wrapper library.

- [**`@solana-mobile/mobile-wallet-adapter-protocol-web3js`**](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages/mobile-wallet-adapter-protocol-web3js)
  - A convenience wrapper for the base library that enables with `web3.js` primitives like `Transaction`.

<Tabs>
<TabItem value="yarn" label="yarn">

```shell
yarn add \
  @solana-mobile/mobile-wallet-adapter-protocol-web3js \
  @solana-mobile/mobile-wallet-adapter-protocol \
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
npm install \
  @solana-mobile/mobile-wallet-adapter-protocol-web3js \
  @solana-mobile/mobile-wallet-adapter-protocol \
```

</TabItem>
</Tabs>

### Import into a file

```tsx
import {
  transact,
  Web3MobileWallet,
} from "@solana-mobile/mobile-wallet-adapter-protocol-web3js";
```

<br/><br/>

## Mobile Wallet API Methods

Reference for the Mobile Wallet API for dApps to connect to wallets and receive signing services.

import Transact from "./mobile-wallet-methods-legacy/\_transact.mdx"

<Transact />

import WalletAuthorize from "./mobile-wallet-methods-legacy/\_walletAuthorize.mdx"

<WalletAuthorize />

import WalletReauthorize from "./mobile-wallet-methods-legacy/\_walletReauthorize.mdx"

<WalletReauthorize />

import WalletDeauthorize from "./mobile-wallet-methods-legacy/\_walletDeauthorize.mdx"

<WalletDeauthorize />

import WalletGetCapabilities from "./mobile-wallet-methods-legacy/\_walletGetCapabilities.mdx"

<WalletGetCapabilities />

import WalletSignTransactions from "./mobile-wallet-methods-legacy/\_walletSignTransactions.mdx"

<WalletSignTransactions />

import WalletSignAndSendTransactions from "./mobile-wallet-methods-legacy/\_walletSignAndSendTransactions.mdx"

<WalletSignAndSendTransactions />

import WalletSignMessages from "./mobile-wallet-methods-legacy/\_walletSignMessages.mdx"

<WalletSignMessages />


---

## Original File: web3js.md
---
---
title: Web3.js Reference
hide_table_of_contents: true
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CardLayout from "../../../src/layouts/CardLayout";
import CTAButton from "../../../src/components/CTAButton";

Interact with accounts and programs on the 
Solana network through the Solana [JSON RPC API](https://docs.solana.com/api/http).

<CTAButton label="API Reference" to="https://solana-labs.github.io/solana-web3.js/" />

### Install dependencies

<Tabs>
<TabItem value="yarn" label="yarn">

```shell
yarn add \
  @solana/web3.js \
  react-native-get-random-values \
  react-native-url-polyfill \
  @craftzdog/react-native-buffer
```

</TabItem>
<TabItem value="npm" label="npm">

```shell
npm install \
  @solana/web3.js \
  react-native-get-random-values \
  react-native-url-polyfill \
  @craftzdog/react-native-buffer
```

</TabItem>
</Tabs>

### Polyfills

Enable all of them in `index.js`

```typescript
import 'react-native-get-random-values';
import 'react-native-url-polyfill/auto';
import {Buffer} from '@craftzdog/react-native-buffer';
global.Buffer = Buffer;
```
