# 21 Dapp Publishing Guidelines

## Original File: listing-page-guidelines.md
---
---
hide_table_of_contents: true
---

# dApp Listing Page Guidelines

import ListingPageSection from '@site/src/components/ListingPageSection';

Your dApp's listing page is often the first impression users will have of your application.
These guidelines will help you create a compelling and informative listing that showcases your dApp.

<ListingPageSection
title="App Icon"
imageSrc="/listing_page_imgs/appicon.png">

The app icon is often a user's first point of contact with your app on the dApp Store, so it is crucial that it is thoughtfully crafted to convey your dApp's brand and value.

**Requirements**

- Icon must be 512px by 512px dimensions.

</ListingPageSection>

<ListingPageSection
title="App Name"
imageSrc="/listing_page_imgs/appname.png">

Your app name should be unique, memorable, and suggest your app's core functionality. Set yourself apart by choosing a name that isn't generic or too similar to existing apps.

</ListingPageSection>

<ListingPageSection
title="Description"
imageSrc="/listing_page_imgs/subtitle.png">

**Short Description**

Your app’s subtitle, or short description, should concisely communicate the purpose of your app. Use this space to showcase your app's "elevator pitch" and capture a user's attention.

##### **Requirements**

- Short description cannot exceed 30 characters.

**Long Description**

Your app's long description should be a well-written, concise overview of your app' features and functionality. Use this space to give more context to users about what your app is and how they will use it. Users should be able to read it and quickly understand your app's core purpose.

</ListingPageSection>

<ListingPageSection
title="Preview"
imageSrc="/listing_page_imgs/apppreview.gif"
isLast={true}>

### Videos

An app preview video should effectively showcase your app's main features, UI flow, and core user experience.

##### **Requirements**

- All videos must be at least 720px in width and height. 1080p (1920px by 1080px) is strongly recommended.
- All videos must be `.mp4` video file format.

### Screenshots

An app preview screenshot should highlight a core feature or screen of your app. The preview gallery should tell a compelling visual story of your app's core user experience.

##### **Requirements**

- All images must be at least 1080px in width and height.
- All images must have consistent orientation (landscape or portrait).
- All images must have equal aspect ratio.

</ListingPageSection>


---

## Original File: link-to-dapp-listing-page.md
---
# Linking to your dApp listing page

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

The dApp Store provides a deep-linking scheme that you can use to directly link users to your app's listing page, where users can see the description, preview media, and more, and then install the app.

## Deep-link scheme

To create the link, you need to know the app's fully qualified _package name_, which is declared in the app's manifest file (e.g `com.solanamobile.mintyfresh`). For Expo apps, it can be found in the `android` field of your `app.json`.

```
solanadappstore://details?id=<package_name>
```

An example:

```
solanadappstore://details?id=com.solanamobile.mintyfresh
```

## Linking from an Android app

You can also link to your dApp Store listing page from an Android app.

This can be useful, for example, when your user's app is out-of-date and you want to link them to the listing page to update the app.

<Tabs>
<TabItem value="React Native" label="React Native">

```ts
import { Linking } from "react-native";

// Use the React Native `Linking` library to open the URL
const linkToListing = () => {
  const url = "solanadappstore://details?id=com.solanamobile.mintyfresh";
  Linking.canOpenURL(url)
    .then((supported) => {
      if (supported) {
        Linking.openURL(url);
      } else {
        console.error("Unable to link to dApp Store");
      }
    })
    .catch((err) => console.error("An error occurred", err));
};
```

</TabItem>
<TabItem value="Kotlin" label="Kotlin">

Update the `AndroidManifest.xml` to include `queries`. 
```xml
<manifest
    <queries>
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data
                android:host="details"
                android:scheme="solanadappstore" />
        </intent>
    </queries>
...
</manifest>
```

```kotlin
// Create an Android intent to navigate to the listing page
val intent = Intent(Intent.ACTION_VIEW).apply {
    data = Uri.parse("solanadappstore://details?id=com.solanamobile.mintyfresh")
    // Make sure there's an activity that can handle this intent
    resolveActivity(packageManager)?.let {
        startActivity(this)
    }
}
```

</TabItem>
</Tabs>
