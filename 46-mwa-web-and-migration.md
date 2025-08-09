# 46 Mwa Web And Migration

## Original File: web-apps.md
---
# MWA for Web Apps

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import MediaCarousel from '@site/src/components/MediaCarousel';
import SideBySideLayout from '@site/src/layouts/SideBySideLayout';
import { SDKSelectionContainer, SDKSelectionButton } from '@site/src/components/SDKSelectionContainer';
import { Smartphone, Code, Github, BookOpen, QrCode, ArrowDownToLine } from 'lucide-react';


<MediaCarousel 
  media={[
    {
      src: "/videos/svw_mwa.mp4",
      label: "Mobile Web"
    },
    {
      src: "/img/RemoteModal.png",
      label: "Desktop Web"
    },
  ]}
  autoPlay={true}
  muted={true}
/>

## Overview

Mobile Wallet Adapter works on both Desktop Web and Mobile Web applications through two different connection methods:

- **Desktop Web Apps** connect remotely to wallet apps via QR code scanning.
- **Mobile Web Apps**, including PWAs, connect locally to wallet apps on the same device.

## SDK

Use the **Mobile Wallet Standard** library to register MWA as a wallet option in your web app for both desktop and mobile users.

The library handles both connection types automatically and presents the correct user experience based on the user's platform. It is compatible with any web app using the `@anza/wallet-adapter` libraries.

  <SDKSelectionContainer title="Reference" icon={<BookOpen size={20} />}>
    <SDKSelectionButton 
      title="Installation" 
      icon={<ArrowDownToLine size={20} />}
      link="/mobile-wallet-adapter/web-installation" 
    />
    <SDKSelectionButton 
      title="Demo" 
      icon={<QrCode size={20} />}
      link="https://solana-mobile.github.io/mobile-wallet-adapter/example-web-app/" 
    />
    <SDKSelectionButton 
      title="Github" 
      icon={<Github size={20} />}
      link="https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages/wallet-standard-mobile" 
    />
  </SDKSelectionContainer>

## Wallet Compatibility 

| Wallet (Android only) | QR Code (Remote) | Mobile Web (Local) |
| ------ | ---------------- | ------------------ |
| Seed Vault Wallet | ✅ | ✅ |
| Solflare | ✅ (Planned) | ✅ |
| Phantom | ❌ | ✅ |

## Browser Compatibility

| Platform                                  | QR Code (Remote) | Mobile Web (Local) | Notes                                                          |
| ----------------------------------------- | ---------------- | ------------------ | -------------------------------------------------------------- |
| Desktop - All Browsers                    | ✅               | N/A                | QR Code display works on all desktop browsers.                 |
| Android - Chrome                          | N/A              | ✅                 | Primary Android browser; also works with Chrome PWAs.          |
| Android - Other Browsers                  | N/A              | ❌                 | Firefox, Opera, Brave, etc. do not support MWA.                |
| iOS - All Browsers                        | N/A              | ❌                 | MWA is not available on any iOS browser.                       |



---

## Original File: migrating-to-wallet-standard.md
---
# Migrate to Wallet Standard

On the web, Mobile Wallet Adapter (MWA) is available as a *standard wallet* with the package:

- [`@solana-mobile/wallet-standard-mobile`](https://github.com/solana-mobile/mobile-wallet-adapter/tree/main/js/packages/wallet-standard-mobile)

This is the recommended library for MWA on web and this guide explains why and how to migrate your web app.

## How to upgrade

### 1. Install Mobile Wallet Standard

Installing the standard wallet takes two steps:

#### Step 1

Add the library `@solana-mobile/wallet-standard-mobile`.

```bash
npm install @solana-mobile/wallet-standard-mobile
```

#### Step 2

Call the `registerMwa` function. 

:::caution
Ensure `registerMwa` is invoked in a non-SSR context. if you're using a framework with Server Side Rendering (e.g Next.js), 
:::

```ts
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
    // remoteHostAuthority: '<REPLACE_WITH_URL_>',  Include to enable remote connection option.
})
```

Now, `Mobile Wallet Adapter` will appear as a wallet option.

For more context, view the **full [installation guide](/mobile-wallet-adapter/web-installation).**

### 2. Update to wallet-adapter >= 0.15.36

If your web app is using `@solana/wallet-adapter-react` update to a version `>= 0.15.36`. 
Updating fixes common MWA Web issues like:
- the [*No connect after selecting MWA*](https://github.com/solana-mobile/mobile-wallet-adapter/issues/1086) bug.

## Why switch?

There are several reasons to upgrade to using MWA via Wallet Standard.

### Wallet Adapter no longer includes MWA as a default 

The `@solana/wallet-adapter-react` library will [not include MWA as a default option](https://github.com/anza-xyz/wallet-adapter/pull/1097) starting from versions `>= 1.0.0`. 

This means any web app using `@solana/wallet-adapter-react` will no longer display `Mobile Wallet Adapter` as an option for users
browsing on Android Chrome, unless explicitly added.

### Enable remote connection

The `@solana-mobile/wallet-standard-mobile` library includes a remote connection option that allows users to connect their mobile wallet app to a desktop web page.

View the [MWA Remote documentation](/mobile-wallet-adapter/web-installation#enable-remote-connection) for more information.

### Bug fixes and stability

The `@solana-mobile/wallet-standard-mobile` is the recommended web library for MWA going forward. This means it will receive all the latest feature additions and updates. 

The legacy `@solana-mobile/wallet-adapter-mobile` library will be deprecated and enter maintenance mode, and only receive updates for bug fixes.