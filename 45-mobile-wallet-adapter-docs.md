# 45 Mobile Wallet Adapter Docs

## Original File: overview.md
---
# Mobile Wallet Adapter

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import { FeatureCards, FeatureCard } from '@site/src/components/FeatureCard';
import { Plug, Code, Users, Smartphone, Globe, ArrowDownToLine, QrCode, List } from 'lucide-react';
import { SDKSelectionContainer, SDKSelectionButton } from '@site/src/components/SDKSelectionContainer';



**Mobile Wallet Adapter (MWA)** is a generic protocol specification that enables dApps to connect with mobile wallet apps for Solana transaction and message signing.

<div style={{ display: 'flex', justifyContent: 'center' }}>
  <img src="/diagrams/mwa_hero_diagram.svg" alt="Mobile Wallet Adapter" width="auto" height="auto" />
</div>

## Features

<FeatureCards>
  <FeatureCard 
    icon={<Plug />}
    title="Unified Wallet Integration" 
    description="Integrate the SDK once and your dApp is compatible with all MWA-compliant wallets." 
  />
  <FeatureCard 
    icon={<Code />}
    title="Simple User Onboarding" 
    description="Users connect and choose a familiar wallet with no additional sign-up steps required." 
  />
  <FeatureCard 
    icon={<Users />}
    title="User Wallet Choice" 
    description="Give users the freedom to connect with their preferred mobile wallet app." 
  />
</FeatureCards>


## Client SDKs

<div>
  <SDKSelectionContainer title="Mobile" icon={<Smartphone size={16} />}>
    <SDKSelectionButton 
      title="Overview" 
      icon={<List size={20} />}
      emphasized={true}
      link="/mobile-wallet-adapter/mobile-apps" 
    />
    <SDKSelectionButton 
      title="React Native" 
      icon={
        <img
          src={"/img/react-native-96.svg"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/react-native/using_mobile_wallet_adapter" 
    />
    <SDKSelectionButton 
      title="Kotlin" 
      icon={
        <img
          src={"/img/kotlin-icon-32.svg"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/android-native/using_mobile_wallet_adapter" 
    />
    <SDKSelectionButton 
      title="Flutter" 
      icon={
        <img
          src={"/img/flutter-icon.svg"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/flutter/overview" 
    />
  </SDKSelectionContainer>

  <SDKSelectionContainer title="Web" icon={<Globe size={16} />}>
    <SDKSelectionButton 
      title="Overview" 
      icon={<List size={20} />}
      link="/mobile-wallet-adapter/web-apps" 
    />
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
  </SDKSelectionContainer>
</div>

---

## Original File: mobile-apps.md
---
---
hide_table_of_contents: true
---

# MWA for Mobile Apps

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import MediaCarousel from '@site/src/components/MediaCarousel';
import SideBySideLayout from '@site/src/layouts/SideBySideLayout';
import { SDKSelectionContainer, SDKSelectionButton } from '@site/src/components/SDKSelectionContainer';
import {Code, ArrowDownToLine, Globe } from 'lucide-react';

<SideBySideLayout
  rightContent={
    <MediaCarousel 
      videos={[
        {
          src: "/videos/svw_mwa.mp4",
          label: "SV Wallet"
        },
        {
          src: "/videos/solflare_mwa.mp4",
          label: "Solflare"
        },
        {
          src: "/videos/phantom_mwa.mp4",
          label: "Phantom"
        },
      ]}
      autoPlay={true}
      muted={true}
    />
  }
  gap="4rem"
  verticalAlignment="center"
>

## Overview

Solana Mobile maintains an open-source **Android** SDK for Mobile Wallet Adapter that enables mobile apps to connect to wallet apps on the same device.

  <SDKSelectionContainer title="Installation" icon={<ArrowDownToLine size={20} />}>
    <SDKSelectionButton 
      title="React Native" 
      icon={
        <img
          src={"/img/react-native-96.svg"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/react-native/using_mobile_wallet_adapter" 
    />
    <SDKSelectionButton 
      title="Kotlin" 
      icon={
        <img
          src={"/img/kotlin-icon-32.svg"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/android-native/using_mobile_wallet_adapter" 
    />
    <SDKSelectionButton 
      title="Mobile Web" 
      icon={<Globe size={20} />}
      link="/mobile-wallet-adapter/web-installation" 
    />
    <SDKSelectionButton 
      title="Flutter" 
      icon={
        <img
          src={"/img/flutter-icon.svg"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/flutter/overview" 
    />
    <SDKSelectionButton 
      title="Unity" 
      icon={
        <img
          src={"/img/unity-logo.png"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/unity/unity_sdk" 
    />
    <SDKSelectionButton 
      title="Unreal" 
      icon={
        <img
          src={"/img/unreal-logo.png"}
          alt=""
          width={20}
          height={20}
        />
      }
      link="/unreal/unreal_sdk" 
    />
  </SDKSelectionContainer>


:::note
These SDKs are only supported on Android because MWA is incompatible with iOS. View this [blog post](/blog/ios-wallet-signing) to learn why.
:::

### SDK Compatibility

| Mobile Platform                            | Is MWA Supported? | Notes                                                                 |
| ------------------------------------------ | ----------------- | --------------------------------------------------------------------- |
| Android                                    | ✅                | Full support for dApps and Wallet apps.                               |
| Mobile Web - Chrome (Android)              | ✅                | Automatic integration if using `@solana/wallet-adapter-react`.        |
| iOS                                        | ❌                | MWA is not currently available for any iOS platform (app or browser). |
| Mobile Web - Safari, Firefox, Opera, Brave | ❌                | These browsers currently do not support MWA on Android or iOS.      |


</SideBySideLayout>

## Supported Wallets

These wallets support MWA and are compatible on Android with any dApp using the Mobile Wallet Adapter SDK.

- Seed Vault Wallet (Coming soon!)
- Solflare
- Phantom