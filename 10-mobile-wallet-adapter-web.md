# 10 Mobile Wallet Adapter Web

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
