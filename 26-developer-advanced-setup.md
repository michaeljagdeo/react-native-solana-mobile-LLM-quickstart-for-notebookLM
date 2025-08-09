# 26 Developer Advanced Setup

## Original File: development-setup.md
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import Card from "../../src/components/Card"
import CardLayout from "../../src/layouts/CardLayout"

# Development Setup

This section covers the steps to:
- Set up your local environment for Android development.
- Configure a device or emulator for building apps for the dApp Store.
- Install an MWA-compliant wallet app for development purposes.


## Set up Android development environment

To build apps for the dApp Store, you need to setup your environment for Android development.

:::info Supported Frameworks

<Tabs>
<TabItem value="Expo / React Native" label="Expo / React Native">

For both Expo or React Native Android development:
- View the Expo [**setup documentation**](https://docs.expo.dev/get-started/set-up-your-environment/?platform=android&device=physical&mode=development-build&buildEnv=local) (Choose custom development build instead of Expo Go).


</TabItem>
<TabItem value="Kotlin (Native)" label="Kotlin (Native)">

For Kotlin Android development:
- Install [Android Studio](https://developer.android.com/studio/install).

</TabItem>
<TabItem value="Flutter" label="Flutter">

For Flutter Android development:
- View the official Flutter [**setup documentation**](https://docs.flutter.dev/get-started/install/macos/mobile-android#configure-android-development).

</TabItem>
</Tabs>

:::

## Setup Device/Emulator

You can test your app during development on any Android device or emulator. A Solana Mobile device is **not** required for dApp Store development—testing on a standard Android device is typically sufficient.

For detailed device setup instructions, refer to Android's official documentation:

- [Setting up a physical device](https://developer.android.com/studio/run/device)
- [Configuring the Android Emulator](https://developer.android.com/studio/run/emulator)

## Install a development wallet app 

The [Mobile Wallet Adapter](/mobile-wallet-adapter/overview) (MWA) library allows your app to connect and interact with compatible wallet apps on your device. 

Install an MWA-compatible wallet app to test your app's Mobile Wallet Adapter integration.

### Mock MWA Wallet

Solana Mobile provides a mock development wallet for you to test your app with and get a feel of what Seed Vault Wallet interactions on the Seeker will look like.

The mock wallet features:
- Mobile Wallet Adapter support
- Simulate connection or signing errors
- QR Code Scanning for MWA Remote Connections

It is intended for development purposes only, so it does not store a persistent keypair and the wallet is reset each time the app is exited.

<details>
<summary>Installation:</summary>

1. Clone the Mock MWA Wallet repo from the [github repository](https://github.com/solana-mobile/mock-mwa-wallet)

```
git clone https://github.com/solana-mobile/mock-mwa-wallet.git
```

2. In Android Studio, `Open project > Navigate to the directory > Select mock-mwa-wallet/android/build.gradle`

3. After Android Studio finishes loading the project, select `fakewallet` in the build/run configuration dropdown in the top right

![Mock MWA Wallet build](/img/mock-mwa-wallet-install.png)

4. After it builds successfully, you should see the app on your connected Android device or emulator.

</details>

### Other MWA-compliant wallet apps

You can also install and test with these popular MWA-compatible wallet apps:
- [Phantom](https://phantom.com/)
- [Solflare](https://www.solflare.com/)

## Next steps

You have a development environment and testing device, and are now ready to start developing. 

Check out these links to quickly get an app running:
- [Quickstart](/react-native/quickstart)
- [Sample Apps](/sample-apps/sample_app_overview)






---

## Original File: mobile-development-frameworks.md
---
# Mobile Development Frameworks

import Card from "../../src/components/Card"
import CardLayout from "../../src/layouts/CardLayout"


## Choose a development framework

Although, the Solana Mobile Stack is primarily written in native Android, we also have support for popular mobile
development frameworks.


### SDKs

These are SDKs that are maintained by the Solana Mobile team.


<CardLayout autoFitEnabled={true}>
    <Card
        to="/react-native/overview"
        header={{
            label: "React Native",
            translateId: "react-native",
        }}
        body={{
            label: "Quickly start building your mobile dApp with React Native, with access to a selection of familiar web libraries.",
            translateId: "react-native-body",
        }}
        iconPath="img/react-native-96.svg"
    />
    <Card
        to="/android-native/overview"
        header={{
            label: "Kotlin",
            translateId: "android-native",
        }}
        body={{
            label: "Develop a Kotlin Android app to utilize the full capabilities of the Android OS and build a performant native experience.",
            translateId: "android-native-body",
        }}
        iconPath="img/kotlin-icon-32.svg"
    />
</CardLayout>

### Community SDKs

These SDKs are actively maintained, supported, and used by members and developers of our community. Click to learn more about
the capabilities supported by each, the state of development, and how to get started.

<CardLayout autoFitEnabled={true}>
    <Card
        to="/flutter/overview"
        header={{
            label: "Flutter",
            translateId: "flutter-headr",
        }}
        body={{
            label: "An actively maintained SDK for building Solana mobile dApps with Flutter.",
            translateId: "learn-programs",
        }}
        iconPath="img/flutter-icon.svg"
    />
    <Card
        to="/unity/unity_sdk"
        header={{
            label: "Unity",
            translateId: "unity-header",
        }}
        body={{
            label: "Integrate the Solana Mobile stack into your Unity mobile game.",
            translateId: "unity-setup-body",
        }}
        iconPath="img/unity-logo.png"
    />
    <Card
        to="/unreal/unreal_sdk"
        header={{
            label: "Unreal Engine",
            translateId: "unreal-header",
        }}
        body={{
            label: "An early stage SDK for integrating Solana Mobile into an Unreal Engine mobile game.",
            translateId: "unreal-header-body",
        }}
        iconPath="img/unreal-logo.png"
    />
</CardLayout>
