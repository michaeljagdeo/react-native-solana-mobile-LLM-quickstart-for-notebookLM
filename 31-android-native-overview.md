# 31 Android Native Overview

## Original File: overview.md
---
---
displayed_sidebar: kotlinSidebar
---

import Card from "../../src/components/Card"
import CardLayout from "../../src/layouts/CardLayout"

# Kotlin Android Development

Kotlin is an [officially supported](https://developer.android.com/kotlin) programming language for Android development and is used to build native Android apps.

This section of the site provides a developer guide for building Kotlin Android apps for Solana.

## Quickstart

<CardLayout autoFitEnabled={true}>
    <Card
        to="/android-native/setup"
        header={{
            label: "Project Setup",
            translateId: "setup",
        }}
        body={{
            label: "Setup your Android project with the core Kotlin SDKs for Solana development.",
            translateId: "setup-body",
        }}
        iconPath="img/android_icon.svg"
    />
    <Card
        to="/android-native/quickstart"
        header={{
            label: "Jetpack Compose Starter App",
            translateId: "compose-scaffold-body",
        }}
        body={{
            label: "Clone the Jetpack Compose Scaffold app to quickly start developing.",
            translateId: "compose-scaffold",
        }}
        iconPath="img/jetpack-compose-icon.png"
    />
</CardLayout>

## Core Kotlin SDKs

Read these guides for a collection of code snippets and examples for basic use cases commonly used in Solana Kotlin dApps.

<CardLayout autoFitEnabled={true}>
    <Card
        to="/android-native/rpc-requests"
        header={{
            label: "JSON RPC Requests",
            translateId: "rpc-requests",
        }}
        body={{
            label: "Learn the rpc-core library to create and send Solana RPC Requests.",
            translateId: "rpc-requests-body",
        }}
        emoji={"🌐"}
    />
    <Card
        to="/android-native/building_transactions"
        header={{
            label: "Transaction building",
            translateId: "transaction-building",
        }}
        body={{
            label: "Use the web3-core library to construct Solana transactions and Program instructions.",
            translateId: "transaction-building-body",
        }}
        emoji={"🔧"}
    />
    <Card
        to="/android-native/using_mobile_wallet_adapter"
        header={{
            label: "Mobile Wallet Adapter",
            translateId: "mobile-wallet-adapter",
        }}
        body={{
            label: "Learn how to connect to mobile wallets and request signing services.",
            translateId: "mobile-wallet-adapter-body",
        }}
        emoji={"📱"}
    />
</CardLayout>

## Benefits of native Android development

### Direct access to Android OS features

Developing on Android enables convenient and full access to the Android platform's capabilities. Developers can access OS-specific functionalities like Camera SDK, ARKit, Touch ID, hardware sensors, etc without needing any bridging.

### Optimized performance

Native Android apps, as a result of full utilization of the system capabilities, generally have better performance and efficiency. Native apps provide the highest attainable frame rates, computing power, graphics support, etc.

### Jetpack Compose

Kotlin enables the building of Android apps using [Jetpack Compose](https://developer.android.com/jetpack/compose/why-adopt), a modern toolkit for creating native Android UIs. The Kotlin sample apps
on this documentation site are all built with Jetpack Compose.


---

## Original File: setup.md
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import CTAButton from "../../src/components/CTAButton";
import versions from '@site/sms-version-constants';

# Kotlin Project Setup

### Prerequisites

- Download [Android Studio](https://developer.android.com/studio) for development and device management.

- Follow the [prerequisite setup](../developers/development-setup#prerequisite-setup) guide to set up your [Android Device/Emulator](../developers/development-setup#2-setup-deviceemulator) and install a MWA-compatible wallet, like [fakewallet](../developers/development-setup#3-install-a-wallet-app).

## Solana Mobile Kotlin Compose Scaffold

The quickest way to start developing on Kotlin is to build off of the [Solana Jetpack Compose Scaffold](https://github.com/solana-mobile/solana-kotlin-compose-scaffold) example.

The scaffold is a basic Solana Android app built with [Jetpack Compose](https://developer.android.com/jetpack/compose) and Material 3 components.

Follow the quickstart guide to install and run the scaffold app.

<CTAButton label="Quickstart" to="/android-native/quickstart" />

## Android Project Setup

### Setting up a fresh Android Project

Follow these steps to setup a fresh Android project with the recommended libraries for Solana development.

#### Step 1: Navigate to your Android project's build.gradle file

In Android Studio, navigate to your Android project's module `build.gradle.kts` file.

#### Step 2. Add Solana dependencies

Include the following dependencies to your Android project's `build.gradle.kts` file. These
are the recommended core Kotlin libraries for Solana transaction building, RPC requests, and wallet signing.

<Tabs>
<TabItem value="build.gradle.kts" label="build.gradle.kts">

<pre><code language="groovy">

{
`dependencies {
    implementation("com.solanamobile:mobile-wallet-adapter-clientlib-ktx:${versions.KOTLIN_MWA_CLIENTLIB_KTX_VERSION}")
    implementation("com.solanamobile:web3-solana:${versions.KOTLIN_WEB3_SOLANA_VERSION}")
    implementation("com.solanamobile:rpc-core:${versions.KOTLIN_RPC_CORE_VERSION}")
    implementation("io.github.funkatronics:multimult:${versions.KOTLIN_MULTIMULT_VERSION}")
}`
}

</code></pre>

</TabItem>
</Tabs>

<details>
<summary>Overview of each dependency:</summary>

- `com.solanamobile:mobile-wallet-adapter-clientlib-ktx`: Mobile Wallet Adapter client library for interacting with MWA-compatible wallets.
- `com.solanamobile:web3-solana`: Solana Kotlin library providing core Solana primitives like transaction building and public key class.
- `com.solanamobile:rpc-core`: A Kotlin library providing a generic interface and abstractions for building Solana RPC requests.
- `io.github.funkatronics:multimult`: Lightweight utility library for Base58 conversions.

</details>

#### Step 3. Build and run your app

Your project's dependencies should be set up and you can try building and run the app!

## Next Steps

Congrats! At this point, you have installed the necessary libraries for your project and are ready to start building an app that interacts with the Solana network.

Check out the other resources on this site like guides, SDK references, and sample apps to learn more about what you can do.


---

## Original File: quickstart.md
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import Card from "../../src/components/Card"
import CardLayout from "../../src/layouts/CardLayout"
import ImageRow from "../../src/components/ImageRow"

import BuildRunImage from "../../static/kotlin_images/compose-build-run.png";

# Kotlin Quickstart

The quickest way to start building Solana Kotlin dApps is to build off the [Solana Jetpack Compose Scaffold](https://github.com/solana-mobile/solana-kotlin-compose-scaffold).

## Solana Jetpack Compose Scaffold

The scaffold app serves as both a starting implementation and an example reference of how to use the core Kotlin SDKs, like `web3-core`, `rpc-core`,
and Mobile Wallet Adapter.

It includes:

- Core Solana kotlin libraries
- Pre-built Compose UI components,
- Code examples of transaction building and RPC requests.

<ImageRow>
    <img src="/kotlin_images/scaffoldScreenshot1.png" alt="Scaffold dApp Screenshot 1" width="300" />
    <img src="/kotlin_images/scaffoldScreenshot2.png" alt="Scaffold dApp Screenshot 2" width="300" />
    <img src="/kotlin_images/scaffoldScreenshot3.png" alt="Scaffold dApp Screenshot 3" width="300" />
</ImageRow>

## Prerequisites

Follow the [prerequisite setup](../developers/development-setup#prerequisite-setup) guide to set up Android Studio, your [Android Device/Emulator](../developers/development-setup#2-setup-deviceemulator) and install a MWA-compatible wallet, like [fakewallet](../developers/development-setup#3-install-a-wallet-app).

## Install the Jetpack Compose Scaffold

**Clone the repo**

The scaffold app is open source and can be fetched from [Github](https://github.com/solana-mobile/solana-kotlin-compose-scaffold).

```shell
git clone https://github.com/solana-mobile/solana-kotlin-compose-scaffold.git
```

**Open the project in Android Studio**

In Android Studio, open the project with `File > Open > SolanaKotlinComposeScaffold/build.gradle.kts`

**Build and run**

Ensure you have connected an Android emulator or device and it is detected by Android Studio. If not, follow
this [guide](../developers/development-setup#2-setup-deviceemulator) to setup your emulator/device.

In the top bar of Android Studio, select `"app"` as the configuration and your emulator/device, then click run.

<img
src={BuildRunImage}
alt="Build and run the app"
/>

<br />
<br />

If successful, the scaffold app will launch on your emulator/device.

Connect with a locally installed wallet app to start interacting with the Solana network! 🎉

## Further learning

To learn how to better use the core Solana Kotlin SDKs, check out these developer guides.

<CardLayout autoFitEnabled={true}>
    <Card
        to="/android-native/rpc-requests"
        header={{
            label: "JSON RPC Requests",
            translateId: "rpc-requests",
        }}
        body={{
            label: "Learn the rpc-core library to create and send Solana RPC Requests.",
            translateId: "rpc-requests-body",
        }}
        emoji={"🌐"}
    />
    <Card
        to="/android-native/building_transactions"
        header={{
            label: "Transaction building",
            translateId: "transaction-building",
        }}
        body={{
            label: "Use the web3-core library to construct Solana transactions and Program instructions.",
            translateId: "transaction-building-body",
        }}
        emoji={"🔧"}
    />
    <Card
        to="/android-native/using_mobile_wallet_adapter"
        header={{
            label: "Mobile Wallet Adapter",
            translateId: "mobile-wallet-adapter",
        }}
        body={{
            label: "Learn how to connect to mobile wallets and request signing services.",
            translateId: "mobile-wallet-adapter-body",
        }}
        emoji={"📱"}
    />
</CardLayout>
