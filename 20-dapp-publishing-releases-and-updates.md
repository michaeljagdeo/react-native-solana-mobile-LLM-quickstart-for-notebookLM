# 20 Dapp Publishing Releases And Updates

## Original File: publishing_releases.md
---
# Publishing subsequent dApp releases

When you're ready to publish additional releases of your dApp, follow these steps.

## 1. Create an updated APK file

Each release of your dApp will require an updated release APK file, signed with the same signing key you used for your initial release.

It is **very** important that each new APK file include the following updates:

- The `versionName` value in `build.gradle` must be updated from the previous release. This field can be set to arbitrary values.
- The `versionCode` value in `build.gradle` must be incremented by one monotonically between each update.

:::tip
You can learn more about APK versioning in the [Android developer docs](https://developer.android.com/studio/publish/versioning).
:::

## 2. Update your configuration file

Edit the `release` and `solana_mobile_dapp_publisher_portal` sections of your configuration file to reflect any changes.

:::tip
Be sure to include `new_in_version` details so users can know what to expect with the update!
:::

## 3. Mint a new release NFT

The Solana dApp store requires each new release of your dApp to be minted as a release NFT with all the changes discussed in this section.

Run the same CLI command as the "Create the release NFT" step from the [Mint a release NFT](submit#mint-a-release-nft) section in these docs.

## 4. Submit an update to the Publisher Portal

Submit the update to the Solana dApp Publisher Portal, where the new release will enter a review queue for inclusion in the dApp store catalog:

```
npx dapp-store publish update -k <path_to_your_keypair> --requestor-is-authorized --complies-with-solana-dapp-store-policies
```


---

## Original File: publishing-updates.md
---
# Publish a dApp update

When you're ready to publish an updated release of your dApp, follow these steps.

## 1. Create an updated APK file

Each release of your dApp will require an updated release APK file, signed with the same signing key you used for your initial release.

It is **very** important that each new APK file include the following updates:

- The `versionName` value in `build.gradle` must be updated from the previous release. This field can be set to arbitrary values.
- The `versionCode` value in `build.gradle` must be incremented by one monotonically between each update.

:::tip
You can learn more about APK versioning in the [Android developer docs](https://developer.android.com/studio/publish/versioning).
:::

## 2. Update your configuration file

Edit the `release` and `solana_mobile_dapp_publisher_portal` sections of your configuration file to reflect any changes.

:::tip
Be sure to include `new_in_version` details so users can know what to expect with the update!
:::

## 3. Mint a new release NFT

The Solana dApp store requires each new release of your dApp to be minted as a release NFT with all the changes discussed in this section.

Run the same CLI command as the "Create the release NFT" step from the [Mint a release NFT](submit#mint-a-release-nft) section in these docs.

## 4. Submit an update to the Publisher Portal

Submit the update to the Solana dApp Publisher Portal, where the new release will enter a review queue for inclusion in the dApp store catalog:

```
npx dapp-store publish update -k <path_to_your_keypair> -u <mainnet_beta_rpc_url> --requestor-is-authorized --complies-with-solana-dapp-store-policies
```


---

## Original File: publishing-a-pwa.md
---
# Publishing a Progressive Web App (PWA)

## PWAs on the dApp Store

[Progressive Web Apps (PWAs)](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) are web applications that use modern web capabilities to deliver an app-like experience to users.

PWAs can be published on the dApp Store, as an Android app wrapped within a **Trusted Web Activity (TWA)**.

## Trusted Web Activities (TWAs)

[Trusted Web Activities (TWAs)](https://developer.chrome.com/docs/android/trusted-web-activity) allow you to package your PWA into an Android app. TWAs use Chrome to render the web app, providing a full screen, native-like experience without any browser UI.

Once you create a TWA, you will have an Android APK file and you can follow the dApp publishing guide to submit the app.

Follow the guide below to get your PWA ready for the dApp Store.

## Tutorial: Converting a PWA to an Android App

This guide shows you how to:

- Install the Bubblewrap CLI tool
- Build the TWA and output an APK
- Generate the Digital Asset Link for the APK

By the end, you will have a functional, signed release APK that can be published on the dApp Store!

### Prerequisite

- A PWA [web manifest](https://developer.mozilla.org/en-US/docs/Web/Manifest) hosted at `https://your-pwa-url.com/manifest.json`

### Template: Web Manifest 

At minimum, a typical manifest file includes:

- The app's name
- The icons the app should use
- The URL that should be opened when the app launches

You can use this template to quickly stand up a `manifest.json` on your website.

```json manifest.json
{
    "name": "APP_NAME",
    "short_name": "APP_NAME",
    "scope": "/",
    "start_url": "/",
    "icons": [
        {
            "src": "/android-chrome-192x192.png",
            "sizes": "192x192",
            "type": "image/png"
        },
        {
            "src": "/android-chrome-512x512.png",
            "sizes": "512x512",
            "type": "image/png"
        }
    ],
    "theme_color": "#ffffff",
    "background_color": "#ffffff",
    "display": "standalone"
}
```

For more information on web manifests, view this [documentation](https://web.dev/articles/add-manifest).


### 1. Installing Bubblewrap CLI

[Bubblewrap CLI](https://github.com/GoogleChromeLabs/bubblewrap/tree/main/packages/cli) is a tool that simplifies the process of converting your PWA into an Android app using TWA.

Requires Node 14.15.0 and above

```bash
npm i -g @bubblewrap/cli
```

### 2. Initializing Your Project

In a new directory, run the `init` command and supply the URL to your web manifest.
Bubblewrap will download the existing manifest and use it to help fill in metadata for the TWA's manifest.

- See the Bubblewrap official [documentation](https://github.com/GoogleChromeLabs/bubblewrap/tree/main/packages/cli#init).

```bash
bubblewrap init --manifest https://your-pwa-url.com/manifest.json
```

This command will guide you through the process of creating the TWA manifest for the project.

- If it asks to install additional tooling (e.g Android SDK, JDK, or build tools), it is necessary and you should allow it to install.

#### Manifest Configuration

It will also prompt a series of questions regarding configurations for:

- **Domain and URL path**
  - Ensure this points directly to where your PWA is hosted.
- **Display mode and Status Bar settings**
  - See the [Android documentation](https://material.io/design/platform-guidance/android-bars.html) to see how Status Bar and Navigation Bars appear and decide what configuration is appropriate for your app.
- **Splash Screen and Icons**
  - Create a cohesive splash screen by providing a splash screen color and an icon that displays over it.
- **Keystore location and Password**
  - See _Caution_ below.

:::caution

The `init` command will ask you to generate an **Android Keystore and password**.

The [Android Keystore](https://developer.android.com/privacy-and-security/keystore) is a security tool that:

- Contains a private key used to digitally sign your app. This signature is used to verify the app's authenticity and integrity.
- The same key must be used to sign all future updates of your app. This ensures that only you can make updates to your app.

Keep the Keystore file and password secure – losing them can prevent future app updates. Consider following
Google's [official guide for best practices around Keystore management](https://developer.android.com/studio/publish/app-signing#secure_key).

:::

After completion, Bubblewrap will create in your directory:

- A `twa-manifest.json` configured with the options from above.
- TWA Android project files generated from `twa-manifest.json`.

:::note

The Android project is entirely generated from the `twa-manifest.json`, so you only need to include
`twa-manifest.json` in source control. The Android project files are unncessary to track as they are generated.

Any changes to the Android project will be deleted or overwritten by the `update` command (explained in the _Updating your TWA_ section).

:::

### 3. Building the Android APK

#### Add supported langauges

Before running the build command, you need to specify the languages your app supports. 

:::warning
Do not skip this step!

By default, Bubblewrap CLI incorrectly declares that your app supports all locales. This inaccuracy will be displayed on your dApp Store listing page.
:::

In the generated Android project, edit the `build.gradle` and add:

```kt build.gradle
android {
    defaultConfig {
        ...
        resConfigs "en", "es" // Add any locales your app supports
    }
}
```

See the [Android documentation](https://developer.android.com/guide/topics/resources/multilingual-support#specify-the-languages-your-app-supports) for more details.

#### Build the APK

The next step is to build initialized Android project and output a _signed release APK_. This APK is what you will submit for publishing on the dApp Store.


In the same directory, run:

```bash
bubblewrap build
```

This command will

- If it asks to install additional tooling (e.g Android SDK, JDK, or build tools), you should allow it to install.

### 4. Publish Digital Asset Links

The last step is to declare your app's [Digital Asset Links (DAL)](https://developers.google.com/digital-asset-links/v1/getting-started). DALs establish a secure connection between your website and the Android app.

:::tip

This step is **required** for the PWA to display in a full screen, native-like experience. If missing, the PWA will display
Chrome browser UI (e.g the URL bar).

:::

Declare the connection by adding the SHA256 fingerprints of your app’s signing certificate at `https://your-domain.com/.well-known/assetlinks.json`.

1. Generate the SHA256 fingerprint from the Keystore:

```bash
keytool -list -v -keystore android.keystore
```

2. Add the fingerprint to your TWA manifest:

```bash
bubblewrap fingerprint add <SHA256_fingerprint>
```

3. Generate the DAL `assetlinks.json` file:

```bash
bubblewrap fingerprint generateAssetLinks
```

It should generate a file named `assetlinks.json` configured with the SHA256 fingerprint.

4. Publish the generated `assetlinks.json` file at:
   `https://your-domain.com/.well-known/assetlinks.json`

Congrats! You have successfully converted your PWA into a working Android app.

## Testing Your App

If you have an emulator or testing device ready, you can install the APK with:

```bash
bubblewrap install app-release-signed.apk
```

Ensure the app is working as expected before submitting to the dApp Store.

:::tip

If you are seeing the browser navigation bar at the top of the app, your app's Digital Asset Links might not be configured
correctly.

Double check that you followed Step 4 and have correctly published your app's SHA256 fingerprint.

:::

## Updating your TWA

If you want to release a new version of the app with changes to the TWA manifest (e.g Updating the icon), you can
make edits to `twa-manifest.json` and run the command:

```bash
bubblewrap update --manifest=./path/to/twa-manifest.json
```

This command regenerates the entire Android project from the `twa-manifest.json` and bumps the app version. The manifest is preserved, while any manual changes to the previous Android project are deleted or overwritten.

After this you can run `bubblewrap build` again to generate the signed release APK.

## Publishing to dApp Store

Once you have a signed APK, you can proceed with publishing as if you were publishing a normal
Android app.

**Follow the step by step [dApp publishing guide](/dapp-publishing/overview) to submit your signed release APK.**
