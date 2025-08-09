# 17 Dapp Publishing Overview

## Original File: overview.md
---
# Publishing to the Solana dApp Store

import LiteYouTubeEmbed from 'react-lite-youtube-embed';
import 'react-lite-youtube-embed/dist/LiteYouTubeEmbed.css';

## Overview

Publishing a dApp to the Solana dApp Store involves the following:

1. Create a set of NFTs describing the dApp, publisher, and release on-chain.
2. Submit a request to the Solana dApp Store publisher portal requesting that Solana Mobile team review the dApp's release NFT.

The publishing tool is designed for CI/CD usage - all steps, including submitting publish portal requests, can be integrated into your dApp release workflows. All files used during the NFT creation and publishing request submission steps can be committed to source control.

## Agreement & Policies

Before you get started with the publishing process, you should read the following documents:

- [Publisher Policy](publisher-policy)
- [Developer Agreement](agreement)

### Publisher Policy

We’ve created the Solana dApp Store to be the best home for Web3 and crypto apps. Our policies exist to protect users from illegal, harmful, and misleading content, without restricting the abilities of apps to use crypto features, like trading NFTs or other digital goods.

All apps submitted to the Solana dApp Store must comply with these policies. If you have concerns about an app in the Solana dApp Store catalog, please report it to concerns@dappstore.solanamobile.com.

### Solana dApp Store Developer Agreement

Make sure to read and understand the Solana dApp Store Developer Agreement. These terms apply to all submissions made to the Solana dApp Store publisher portal.

## Next steps

To get started with dApp publishing, you can begin following the [step-by-step guide](/dapp-publishing/setup) or watch this video tutorial:

<div className="video-container">
<LiteYouTubeEmbed
    id="IgeE1mg1aYk"
    params="autoplay=1&autohide=1&showinfo=0&rel=0"
    title="How to submit an app to the Solana dApp Store"
    poster="hqdefault"
    webp
/>
</div>


---

## Original File: intro.md
---
---
displayed_sidebar: dappPublishingSidebar
---

# Solana dApp Store

import Card from "../../src/components/Card"
import CardLayout from "../../src/layouts/CardLayout"
import { FAQ, FAQSection } from '@site/src/components/FAQ';


The Solana dApp Store is the crypto-friendly app store for Solana Mobile devices.

# Quickstart

Wherever you are in your publishing journey, you can find resources here to get started.

<FAQ>
  <FAQSection title="What apps can be published to the dApp Store?" expanded={true}>

    Both Android apps and web apps!

    If you:
    - Have an existing Android app, you're ready to start publishing now!
    - Have a web app, you can convert a PWA into an Android app and generate an APK for app submission!

    Before publishing, ensure your dApp complies with the Solana dApp Store [publisher policy](/dapp-publishing/publisher-policy).

    <CardLayout autoFitEnabled={true}>

        <Card
            to="/dapp-publishing/overview"
            header={{
                label: "Publish your dApp",
                translateId: "publish-card-header",
            }}
            body={{
                label: "Follow the step-by-step guide to publish your app to the Solana dApp Store.",
                translateId: "publish-card-body",
            }}
            iconPath="img/rocket-icon2.png"
        />

        <Card
            to="/dapp-publishing/publishing-a-pwa"
            header={{
                label: "Convert a PWA to an APK",
                translateId: "pwa-card-header",
            }}
            body={{
                label: "Learn how to convert a PWA to an Android app and publish on the dApp Store.",
                translateId: "pwa-card-body",
            }}
            iconPath="img/pwa-icon.png"
        />

    </CardLayout>

  </FAQSection>
  <FAQSection title="Do I need a Solana Mobile device to build an app for the dApp Store?">
    You do not need a Solana Mobile device to develop Android apps for the dApp Store.

    Developers **already** have all the tools necessary to start building apps for the dApp Store today!


    <CardLayout autoFitEnabled={true}>
        <Card
            to="/developers/development-setup"
            header={{
                label: "Development Setup",
                translateId: "develop-card-header",
            }}
            body={{
                label: "A guide to setup an Android device or emulator and start building for the dApp Store.",
                translateId: "develop-card-body",
            }}
        iconPath="img/solana-mobile-stack-logo.png"
        />
    </CardLayout>

  </FAQSection>
  <FAQSection title="I've published my app! How can I promote it?">
    You can find resources related to App Promotion by viewing our Marketing & Partnerships documentation

    <CardLayout autoFitEnabled={true}>
        <Card
            to="/marketing/overview"
            header={{
                label: "Marketing & Promotion Resources",
                translateId: "marketing-card-header",
            }}
            body={{
                label: "See the Marketing & Partnership documentation for resources around launching on the dApp Store.",
                translateId: "marketing-card-body",
            }}
            emoji="📣"
        />
    </CardLayout>

  </FAQSection>
</FAQ>

## More resources

<CardLayout autoFitEnabled={true}>
    <Card
        to="/dapp-publishing/overview"
        header={{
            label: "Publish a dApp",
            translateId: "publish-card-header",
        }}
        body={{
            label: "Follow the step-by-step guide to publish your app to the Solana dApp Store.",
            translateId: "publish-card-body",
        }}
        emoji="🚀"
    />
    <Card
        to="/dapp-publishing/publishing-a-pwa"
        header={{
            label: "Progressive Web Apps (PWAs)",
            translateId: "pwa-card-header",
        }}
        body={{
            label: "Learn how to convert a PWA to an Android app and publish on the dApp Store.",
            translateId: "pwa-card-body)",
        }}
        iconPath="img/pwa-icon.png"
    />
</CardLayout>

<CardLayout autoFitEnabled={true}>
    <Card
        to="/dapp-publishing/qanda"
        header={{
            label: "Frequently Asked Questions",
            translateId: "qanda-card-header",
        }}
        body={{
            label: "See the list of frequently asked questions about the Solana dApp Store and its publishing process.",
            translateId: "qanda-card-body",
        }}
        emoji="❓"
    />
    <Card
        to="/dapp-publishing/publisher-policy"
        header={{
            label: "Publisher Policy",
            translateId: "policy-card-header",
        }}
        body={{
            label: "Learn about the dApp Store mission statement and publisher policy.",
            translateId: "policy-card-body",
        }}
        emoji="📜"
    />
</CardLayout>

<br/>
