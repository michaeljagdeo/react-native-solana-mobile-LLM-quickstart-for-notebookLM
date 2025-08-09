# 19 Dapp Publishing Submission

## Original File: submit.md
---
# Submit your dApp release

After minting an App NFT, you will need to mint a **Release NFT** in order to submit for app review.

A Release NFT:

- Contains metadata that represents a specific release version of an app.
- Should be newly created each time an updated app version is submitted.

## Mint a release NFT

Using the same wallet that contains your App NFT, run this command.

```shell
npx dapp-store create release -k <path_to_your_keypair> -b <path_to_your_android_sdk_build_tools> [-u <mainnet_beta_rpc_url>]
```

**You will repeat this step for each new version of your dApp you want to release**. The mint address of the latest release is recorded in your configuration file.

:::tip
When using CLI version >= `0.8.0`, the following commands will submit a Solana transaction that includes a default priority fee of `500000` lamports.

To customize this value, use param `-p` or `--priority-fee-lamports <priority-fee-lamports>`
:::

:::caution
Please make sure your network connection is reliable and has a minimum upload speed of 0.25 megabytes per second.
:::

## Submit your app

After minting a complete set of on-chain NFTs (App NFT and Release NFT), you may choose to submit them to the Solana dApp Publisher Portal, as a candidate for inclusion in the Solana dApp Store catalog:

```shell
npx dapp-store publish submit -k <path_to_your_keypair> -u <mainnet_beta_rpc_url> --requestor-is-authorized --complies-with-solana-dapp-store-policies
```

The two flags for this command (`--requestor-is-authorized` and `--complies-with-solana-dapp-store-policies`) are attestations from the requestor that this dApp is compliant with Solana dApp Store policies, and that they are authorized to submit this request to the Solana dApp Publisher Portal.

## Get in contact for App Review

Congrats! You've successfully submitted your app. Now, we'll review your app based on our policies.

 1. Join the Solana Mobile Discord.
 2. In the `#developer` channel, get the developer role.
 3. Leave a message in the `#dapp-store` channel that you've completed app review.
 4. We'll be in contact through the channel shortly after.




---

## Original File: building-expo-apk.md
---
# Building a release APK with Expo

If you have a React Native app built with Expo, just a few steps are needed to publish it on the Solana Mobile dApp Store.

This guide assumes minimal experience with typical Android development tools.

## Step 1. Build an APK

By default, when building with EAS, the platform compiles an Android App Bundle (.aab) file. Solana Mobile dApp Store requires a different binary format, APK, so we'll create a new EAS profile by adding the following lines in `eas.json`:

```
"dapp-store": {
    "channel": "production",
    "android": {
        "buildType": "apk"
    }
}
```

Then build:

```
npx eas build -p android --profile dapp-store
```

Meanwhile, in your project directory, create a new folder `dapp-store-build` (if you're using Git you may want to add this folder to `.gitignore` to avoid uploading large binary files).

Once the EAS build has finished, download the APK file, and name it `myapp-0.0.1-unsigned.apk`. Replace `myapp` and `0.0.1` with your app name and release version, but keep file naming consistent between updates.

## Step 2. Set up Java and Android tooling

You will need to download and install:

- JDK: https://www.oracle.com/java/technologies/downloads/
- Android Studio: https://developer.android.com/studio

Once Android Studio is installed, you need to create an empty project. Then, open Settings > Languages & Frameworks > Android SDK.

Switch to SDK Tools tab and check if you have Android SDK Command-line tools installed.

A tool named AAPT2 (Android Asset Packaging Tool) may be missing from Android Studio, and you may have to install it separately:

```
sdkmanager "build-tools;build-tools-version"
```

`build-tools-version` should be replaced with the latest version from the release page: https://developer.android.com/tools/releases/build-tools

For example:

```
sdkmanager "build-tools;34.0.0"
```

Alternatively, use a full path on Mac:

```
~/Library/Android/sdk/cmdline-tools/latest/bin/sdkmanager "build-tools;34.0.0"
```

## Step 3. Sign the APK

In your project directory, create a new folder `dapp-store-signing-keys` (if you're using Git you may want to add this folder to `.gitignore` to avoid exposing signing keys).

Open the folder `dapp-store-signing-keys` and run the following command (edit as needed) to create a keystore used for signing the app.

```
keytool -genkey -v -keystore release-key.keystore -alias myapp -keyalg RSA -keysize 2048 -validity 50000
```

In this command you need to change the values:

- `myapp`: unique name (alias) that identifies your app or project
- `50000`: key validity in days; you may want to reduce it

Open your project directory, and run the following command:

```
~/Library/Android/sdk/build-tools/35.0.0/apksigner sign \
    --ks ./dapp-store-signing-keys/release-key.keystore \
    --ks-key-alias myapp \
    --out ./dapp-store-build/myapp-v0.0.1-signed.apk \
    ./dapp-store-build/myapp-v0.0.1-unsigned.apk
```

The signed binary will be at `dapp-store-build/myapp-v0.0.1-signed.apk`. This is the file you'll need to reference in `config.yaml` at the publishing stage.

## Publishing to dApp Store

Now that you have signed APK, you are ready to follow the [dApp publishing guide](/dapp-publishing/overview) to submit your app to Solana Mobile dApp Store.