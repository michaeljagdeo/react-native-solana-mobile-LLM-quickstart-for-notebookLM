# 44 Saga Token Info

## Original File: saga-genesis-token.md
---
# Saga Genesis Token

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Introduction

The Saga Genesis Token is a unique, non-transferable NFT that represents a verified owner of a Saga device.
It is minted only once per device through the dApp Store after initial device setup.

## NFT Details

The Saga Genesis Token NFT adheres to the Metaplex [Token Standard](https://developers.metaplex.com/token-metadata/token-standard) and is part of a [Verified Collection](https://developers.metaplex.com/token-metadata/collections).

### Soulbound NFT

Every Saga Genesis Token is a Soulbound NFT that is non-transferable from the wallet that it was minted to.

Although likely, this does not mean that every Genesis Token user is interacting from a Saga device. This is because the user's wallet (more specifically, secret key) can be exported/imported from/into the device.

### Collection NFT address

The Saga Genesis Token Collection NFT address is `46pcSL5gmjBrPqGKFaLbbCmR6iVuLJbnQy13hAe7s6CC`. Each Saga Genesis Token NFT metadata will
have a `collection` field with this address, along with the `verified` flag set to `true`.

To better understand how Collection NFTs verify a normal NFT, read the official Metaplex [documentation](https://docs.metaplex.com/programs/token-metadata/certified-collections#collection-nfts).

### View on an explorer

You can view a real Saga Genesis Token on-chain with an explorer by querying an [individual NFT mint address](https://solscan.io/token/DMcJLbYGT9UAiYXMoHMjsoLCW1MRJ12YDnU967pAvByg) or filtering by
[collection NFT address](https://solscan.io/collection/4a2d96b22ab0c8f01cb5ce5bc960b627c2a8271529ae5132d5352b7c86b3b54d).

## Verify a Saga Genesis Token holder

For certain use cases, like rewards claiming or token gated content for Saga users, you will need to verify ownership of the Saga Genesis Token NFT.

### Verifying individual ownership

For token gated content and rewards, the user can explicitly claim their Saga Genesis Token reward. The user should prove
ownership of a Saga Genesis Token and only then should they receive the rewards/content.

#### Sign in with Solana

To receive token gated content and rewards, a user first needs to prove that they own a Saga Genesis Token.
You should use _Sign in with Solana_ to verify that the connecting user truly owns the wallet that contains the Saga Genesis Token.

If you are implementing this flow on web, you can use the standard Solana web libraries like `wallet-adapter-react`, following
this [integration guide](https://github.com/phantom/sign-in-with-solana?tab=readme-ov-file#dapp-integration).

If you are implementing this flow within a mobile app, you will need to manually implement SIWS using [Mobile Wallet
Adapter](/react-native/quickstart#signing-messages) (`authorize` + `signMessage`).

#### Verify the Saga Genesis Token

After proving that the user owns the wallet, you need to verify that the user's wallet actually contains a Saga Genesis Token. There are different ways to do this, but the simplest is to query an RPC provider that supports the [_DAS (Digital Asset Standards)_ API](https://github.com/metaplex-foundation/digital-asset-standard-api).

Given the user's wallet address you can use the _searchAssets_ DAS API method to check ownership of a Saga Genesis Token NFT.

<Tabs>
<TabItem value="Javascript" label="Javascript">

```javascript
const url = `https://your.rpc.com/?api-key=<api_key>`;

const searchAssets = async () => {
  const response = await fetch(url, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      jsonrpc: "2.0",
      id: "my-id",
      method: "searchAssets",
      params: {
        ownerAddress: "<user-wallet-address>", // user's wallet address
        grouping: [
          "collection",
          "46pcSL5gmjBrPqGKFaLbbCmR6iVuLJbnQy13hAe7s6CC", // Genesis Token Collection NFT Address
        ],
        page: 1, // Starts at 1
        limit: 1000,
      },
    }),
  });
  const { result } = await response.json();
  if (result?.total === 1) {
    console.log("Wallet contains a Saga Genesis Token!");
  } else {
    console.log("Wallet does not contain a Saga Genesis Token.");
  }
};
searchAssets();
```

</TabItem>
</Tabs>

The above is a modification of the example from the [Helius DAS API documentation](https://docs.helius.dev/compression-and-das-api/digital-asset-standard-das-api/search-assets).

## Fetching a mint list of holders

For use cases like snapshots and airdropping, you can query for the entire list of current holders.

To fetch the entire collection list of Saga Genesis Token holders, you can use the `getAssetsByGroup` DAS API method. In the following example,
the paginated response is a list of Saga Genesis Token Assets and metadata.

<Tabs>
<TabItem value="Javascript" label="Javascript">

```javascript
const url = "https://your.rpc.com/?api-key=<api-key>";

const getAssetsByGroup = async () => {
  const response = await fetch(url, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      jsonrpc: "2.0",
      id: "my-id",
      method: "getAssetsByGroup",
      params: {
        groupKey: "collection",
        groupValue: "46pcSL5gmjBrPqGKFaLbbCmR6iVuLJbnQy13hAe7s6CC", // Genesis Token Collection NFT Address
        page: 1, // Starts at 1
        limit: 1000,
      },
    }),
  });
  const { result } = await response.json();
  console.log("Page 1 of Saga Genesis Token Assets: ", result.items);
};
getAssetsByGroup();
```

</TabItem>
</Tabs>

The above is a modification of the example from the [Helius DAS API documentation](https://docs.helius.dev/compression-and-das-api/digital-asset-standard-das-api/get-assets-by-group).

Alternatively, there are many [third-party/community created tools](https://docs.metaplex.com/guides/mint-lists) to generate a mint list.


---

## Original File: chapter2-preorder-tokens.md
---
---
title: Chapter 2 Preorder Token NFTs
description: An article explaning the distribution and technical details of the Chapter 2 Preorder Token NFTs.
slug: chapter2-preorder-token
authors:
  - name: Mike Sulistio
    title: Developer Relations @ Solana Mobile
    url: https://twitter.com/MikeSulistio
    image_url: /blog_profile_pics/mikesulistio.png
tags: [saga, chapter-2, NFT]
hide_table_of_contents: false
---

import BlogImageRow from "../../src/components/BlogImageRow/"

# Chapter 2 Preorder Tokens NFTs

<BlogImageRow>
  <img src="/blog_imgs/chapter2-preorder-token.jpeg" alt="Preorder Token" width="300" />
</BlogImageRow>

## Introduction

This article provides information about the Solana Mobile Chapter 2 Preorder Tokens NFTs.The non-transferrable Chapter 2 Preorder Tokens allow for the identification of wallets that have participated in supporting Chapter 2.

Solana Mobile does not currently have a rewards program in connection with the Chapter 2 Preorder Token. Ecosystem teams are entirely free to decide if (and if so, how) they wish to interact with the holders of these non-transferable tokens.

## Chapter 2 Preorder Token holders

The Chapter 2 Preorder Token NFT is a non-transferable NFT distributed to the wallets of anyone who has preordered Chapter 2.

As of the time this article was written, there are 140,000+ holders of the Preorder Token. The preorder phase is still active and any new Tokens will be issued in waves.

## NFT Details

The Chapter 2 Preorder Tokens are non-transferable NFTs enabled by the _[NonTransferable](https://solana.com/developers/guides/token-extensions/non-transferable)_ extension.

### Mint Address

The Chapter 2 Preorder Token mint address is:

- `2DMMamkkxQ6zDMBtkFp8KH7FoWzBMBA1CGTYwom4QH6Z`

This mint address is shared by all token accounts of the Chapter 2 Preorder Token.

### Metadata

Each Chapter 2 Preorder Token NFT utilizes the [Metadata and Metadata Pointer](https://solana.com/developers/guides/token-extensions/metadata-pointer) extension to define
its NFT metadata.

```
Metadata
    Uri: https://arweave.net/WHyy5Fo8vUC7FqFfzqkuYAmDi-BfOMBjZSXIwwO7P6g
    Mint: 2DMMamkkxQ6zDMBtkFp8KH7FoWzBMBA1CGTYwom4QH6Z
    Name: Chapter 2 Preorder Token
    Symbol: CHAPTER2
    Update Authority: GRR6BquJZYWgUqWpNJekBZHDHgxVQ56iZ2P2nWjDFRSn
    Additional Metadata
Metadata Pointer
    Authority: GRR6BquJZYWgUqWpNJekBZHDHgxVQ56iZ2P2nWjDFRSn
    Metadata Address: 2DMMamkkxQ6zDMBtkFp8KH7FoWzBMBA1CGTYwom4QH6Z
```

## Querying for Chapter 2 Preorder Token holders manually

:::tip
This script can return duplicates of the same owner's wallet address.
This is because it is possible for a single wallet address to own more than one Preorder Token, if they preordered multiple Chapter 2 products.
:::

You can follow these steps to manually query the Chapter 2 Preorder Token list and save it into a JSON file:

### 1. Access your RPC provider.

Any RPC provider with the [DAS (Digital Asset Standard)](https://github.com/metaplex-foundation/digital-asset-standard-api) APIs should work. If you don't have one, the Helius Free plan (helius.dev/pricing) should be sufficient for this query.

### 2. Add your API Key to the following query:

```js
import { writeFileSync } from "fs";

const DAS_API_URL = "https://mainnet.helius-rpc.com/?api-key=<API_KEY>";
const TOKEN_MINT_ADDRESS = "2DMMamkkxQ6zDMBtkFp8KH7FoWzBMBA1CGTYwom4QH6Z";
const OUTPUT_FILE = `out.json`;

let tokens = [];

let page = 0;
while (true) {
  page++;
  console.log(`Fetching page ${page}`);
  const response = await fetch(DAS_API_URL, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      jsonrpc: "2.0",
      id: "my-id",
      method: "getTokenAccounts",
      params: {
        mint: `${TOKEN_MINT_ADDRESS}`,
        page: page, // Starts at 1
        limit: 1000,
      },
    }),
  });
  const { result } = await response.json();
  if (result.total === 0) {
    console.log("Done!");
    break;
  }
  for (const item of result.token_accounts) {
    tokens.push({ owner: item.owner, count: item.amount });
  }
}

console.log(`Writing to ${OUTPUT_FILE}`);
writeFileSync(OUTPUT_FILE, JSON.stringify(tokens, null, 2));
```

### 3. Perform the Query

Run the script to perform the query and you can find the results in the output file `out.json`.

### 4. Further querying (optional)

In order to query a subset of the Preorder Token Holders, you may write a script to fetch wallet activity data using blockchain explorers and analytics tools based on the subset criteria. You can use their APIs to retrieve transaction history, token balances, and other relevant information, such as:

- Length of their engagement with the applicable protocol (i.e. The date of the first transaction)
- Participation in applications within the ecosystem that are adjacent or complementary to your project
