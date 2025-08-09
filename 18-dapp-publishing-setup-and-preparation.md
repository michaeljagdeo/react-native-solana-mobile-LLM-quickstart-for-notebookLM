# 18 Dapp Publishing Setup And Preparation

## Original File: setup.md
---
# Publishing Tools Setup

## Prerequisites

### Node.js

Please run all CLI tooling with Node version 18 to 21. Latest known compatibility is Node `v21.7.3`.

```shell
corepack enable
corepack prepare pnpm@`npm info pnpm --json | jq -r .version` --activate
```

If you don't have [jq](https://stedolan.github.io/jq/) installed, you can [install it](https://formulae.brew.sh/formula/jq), or manually get the current version of pnpm with `npm info pnpm` and use like this:

```shell
corepack prepare pnpm@7.13.4 --activate
```

### Android SDK Tools

You must have the Android SDK build tools available for use by the `dapp-store` CLI. If you have Android Studio, these tools are available as part of that installation (for e.g., on MacOS, they can be found in ~/Library/Android/sdk/build-tools/\{version\}). If you do not have Android Studio installed, or wish to use a standalone version of the Android SDK build tools, please follow the instructions [here](https://developer.android.com/studio/intro/update#sdk-manager).

The path to the SDK build tools can be provided either directly to subcommands that require it with the `-b` option, or indirectly via a `.env` file. Please provide a path to a specific, recent version of the SDK tools (e.g., `~/Library/Android/sdk/build-tools/33.0.0`) as older versions do not have the requisite dependency:

```shell
echo "ANDROID_TOOLS_DIR=\"<path_to_android_sdk_version_build_tools_dir>\"" > .env
```

### Java Environment Variable

Some utilities in the Android tools directory require you to configure and make a `JAVA_HOME` environment variable available to your terminal app. This variable points to the root directory of your installed Java Development Kit, or JDK. If you have Android studio installed, there is already a JDK included (in recent versions of Android Studio this is called the "JBR").

To find the location of the JDK/JBR installed with Android Studio, follow [these instructions in the Android Studio documentation.](https://developer.android.com/build/jdks#set-jdk-in-studio). While discussing the mthod for _*setting*_ the JDK location, it also provides the correct method for obtaining the current installation directory.

If you do not have Android Studio installed, you can download a copy of the the OpenJDK [here](https://openjdk.org/projects/jdk/17/). We recommend OpenJDK 17.

Once you have downloaded and installed the JDK, you will need to set the `JAVA_HOME` environment variable per the appropriate method for your operating system. [This page](https://www3.ntu.edu.sg/home/ehchua/programming/howto/Environment_Variables.html) has a good overview for all major operating systems.

### ffmpeg

If you plan on uploading video preview assets, make sure you have the [ffmpeg](https://www.ffmpeg.org/download.html) utility library installed.

Follow this [guide](https://support.audacityteam.org/basics/installing-ffmpeg) to install `ffmpeg` for your operating system.

## Getting Started

In your application folder (e.g., `android-app`):

:::caution

If you're seeing an error, ensure that your `node -v` is returning a version `>= 18` and `<= 21`.
Latest tested compatibile version is Node `v21.7.3`.

:::

```shell
mkdir publishing
cd publishing

pnpm init
pnpm install --save-dev @solana-mobile/dapp-store-cli
npx dapp-store init
npx dapp-store --help
```

## CLI Updates

The CLI will automatically check for updated versions on npm and restrict operations on a periodic basis. If your CI/CD deployments fail, be sure to check if there is a required update.

To update the CLI run:

```bash
pnpm install --save-dev @solana-mobile/dapp-store-cli@latest
```


---

## Original File: prepare.md
---
# Prepare your dApp for publishing

## Configure the publishing details for your dApp

### 1. Collect your publishing assets

:::tip Reference
View the [dApp Publishing Example repo](https://github.com/solana-mobile/dapp-publishing/tree/main/example) for a reference of the assets and configuration file required for publishing.
:::

Once everything is setup, collect the file paths for all your publishing assets (e.g., APK file, icons, screenshot images, videos) relative to the directory that was just created.

#### Requirements

Your submission must meet the following requirements to pass app review:

- Icon(s) must be 512px by 512px dimensions.
  - Follow the [Google Play icon design spec](https://developer.android.com/distribute/google-play/resources/icon-design-specifications) for best practices.
- Banner Graphic image of size 1200px by 600px is required.
- (optional) Feature Graphic image of size 1200x1200px is required to be featured in Editor's choice carousel
- Provide a minimum of 4 screenshot images or videos of your app.
  - We recommend preview images and videos to be 1080p resolution (1920px by 1080px).
- Image Requirements
  - All images provided must be at least 1080px in width and height.
  - All images provided must have consistent orientation (landscape or portrait).
  - All images provided must have equal aspect ratio.
- Videos Requirements
  - All videos provided must be at least 720px in width and height.
  - All videos provided must be `.mp4` video file format.

    :::info
    If you have `.mp4` video assets in your publishing, make sure you have `ffmpeg` utility library [installed](/dapp-publishing/setup#ffmpeg).
    :::
- A release build of your Android APK signed with a unique signing key.

:::warning Important
It is very important that you publish a release build of your Android APK signed with a unique signing key. This key will be used for all releases that you submit to the dApp store, and cannot be shared with releases on a different Android app store. You can learn more about this process [here](https://developer.android.com/studio/publish/app-signing#opt-out).

Apps submitted as debug builds or release builds signed by anything other than a unique key cannot be accepted.
:::

#### Recommendations

Some best practices for the assets & files you'll be providing:

- Make sure your dApp APK is localized properly, and that your build.gradle file identifies the languages & locales that your dApp supports. See [the Android developer documentation](https://developer.android.com/guide/topics/resources/multilingual-support#specify-the-languages-your-app-supports) for more details.

:::tip
It is recommended that you put your dApp publishing files next to your dApp, and source control them together.
:::

### 2. Populate the configuration file

Populate the initial contents of the configuration file created during setup. By default, the file name is `config.yaml`. Replace all fields in `<< >>` with details for your dApp. Remove any fields that don't apply (for e.g., `saga_features`, `google_store_package`, etc).

There are 3 sections to fill out: `publisher`, `app`, and `release`:

- The `publisher` section describes you, the app developer.
- The `app` section represents a single logical app produced by a publisher. A single publisher will always have at least one app, but if you publish multiple different apps under a single identity, there will be one for each of your apps.
- The `release` section is the most important, and describes all the metadata for a single release of an app, including its display name, description, icons, screenshots, etc. The text you enter in the `catalog` subsection, along with the icon and screenshots in the `media` subsections, are what application stores will use to display details about your app to the end user, so be as descriptive as you can.

:::tip Reference
View the [dApp Publishing Example repo](https://github.com/solana-mobile/dapp-publishing/blob/main/example/config.yaml) for a reference of a completed configuration file.
:::

:::info
Each of the above regions of the configuration file have an `address` field. You do not need to modify this field nor should you remove it from the configuration file.
:::

:::tip
You may need to provide details in the `testing_instructions` field of the configuration file that you would not want published on-chain. Rest assured, this data is not published as part of the on-chain metadata.
:::

### 3. Localization of store details (Optional)

The configuration file allows for localization of the details/copy you provide that describes your app. To be clear, this localized text is different from the strings localized _within your app itself_. Localized store details will be presented to users browsing dApp stores based on the locale they have chosen on their device. If a user's device is set to a locale you have provided, they will be presented that localized text.

As a tangible example, here's how you would localize details strings for French (France). This would be placed at the same hierarchical level alongside the default `en-US` locale text:

```yaml
release:
    catalog:
        en-US:
          name: Name of app in English
          ...
        fr-FR:
            name: >-
              Name of app in French (France)
            short_description: >-
              Short app description in French (France)
            long_description: |
              Long app description in French (France)
            new_in_version: >-
              New version features in French (France)
            saga_features: >-
              Saga features in French (France)
```


---

## Original File: checklist.md
---
# Publishing Journey Checklist

import Diagram from '@site/src/components/Diagram';

This checklist aims to provide you a high level overview of the dApp publishing process. You can get
an idea of the resources you'll need to prepare before submitting your app.

## Publishing Journey

At a high level, the publishing process can be simplified into these steps:

<Diagram src="/diagrams/publishingflow.svg" alt="Publishing Flow Diagram" width={900} height={500} />

### Build an Android APK

The dApp Publishing tool requires you to submit an Android APK of your app.

- If you already have an Android app (e.g Published to Google Play Store), you can generate a release build APK.
- If you have a web app, follow [this guide](/dapp-publishing/publishing-a-pwa) to convert it to an Android app and generate an APK for submission.

:::important

Ensure you are submitting a release build of your app that is [signed](https://developer.android.com/studio/publish/app-signing#opt-out). Debug builds will not be accepted.

:::

### Test on an Android device

To ensure a great user experience, we recommend developers to thoroughly test their app to catch any bugs/crashes/issues.

You do not need a Solana Mobile device to test your app, you can just test against a comparable Android device or emulator.

- If using Mobile Wallet Adapter, test your app's connect and signing flows with popular MWA wallets like Phantom and Solflare.
- If your app content is gated (e.g beta access, NFT-gated), prepare a test account with full access for App Review.

### Prepare your App Listing Page

Your app's listing page is what gives users the first impression of your app. You'll want to prepare ahead of time, assets like
your app icon, screenshots/videos, and text content.

See the [Listing Page guidelines](/dapp-publishing/listing-page-guidelines) to visualize your app's listing page and learn best practices.

### Proceed to App Submission!

Once you are ready for publishing, you can follow the step-by-step [App Submission guide](/dapp-publishing/overview) to submit your app. The publishing process
is completely self service, so it can be started whenever you are ready!

After finished, your app will be in queue for App Review and, using your provided contact details, we will reach out to you if any questions are needed.

### App Promotion

For questions regarding promoting your app after launching, see our [Marketing & Partnership documentation](/marketing/overview).
