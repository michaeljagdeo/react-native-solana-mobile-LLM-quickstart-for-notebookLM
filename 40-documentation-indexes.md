# 40 Documentation Indexes

## Original File: README.md
---
# Solana Mobile React Native Documentation

This directory contains the complete offline documentation for developing Solana mobile applications using React Native.

## 📁 Directory Structure

```
react-native-docs/
├── README.md                          # This file - your starting point
├── overview.md                        # High-level overview of React Native development
├── getting-started/                   # General Solana mobile concepts
├── mobile-wallet-adapter/             # Mobile Wallet Adapter documentation
├── reference/                         # TypeScript API references
├── polyfill-guides/                   # Platform-specific polyfills
├── images/                            # Documentation images and assets
└── [Core Documentation Files]         # Main React Native development guides
```

## 🚀 Quick Start Path

### 1. **Start Here**
- `overview.md` - Understanding React Native on Solana Mobile
- `quickstart.md` - Create your first project

### 2. **Environment Setup**
- `setup.md` - Development environment configuration
- `test-with-any-android-device.md` - Testing setup

### 3. **Core Development**
- `making_rpc_requests.md` - Connecting to Solana network
- `building_transactions.md` - Creating and submitting transactions
- `using_mobile_wallet_adapter.md` - Wallet integration
- `storing_mwa_auth.md` - Persistent wallet connections

## 📚 Learning Path by Experience Level

### Beginner
1. `overview.md` - Understand the ecosystem
2. `quickstart.md` - Set up your first project
3. `first_app_tutorial.md` - Build a complete app
4. `hello_world_tutorial.md` - Simple transaction app

### Intermediate
1. `anchor_integration.md` - Using Anchor framework
2. `metaplex_integration.md` - NFT and token standards
3. `mobile_nft_minter_tutorial.md` - NFT creation tutorial
4. `polyfill-guides/` - Platform compatibility

### Advanced
1. `reference/typescript/` - Complete API documentation
2. `mobile-wallet-adapter/` - Deep wallet integration
3. Custom dApp development patterns

## 🛠 Key Components

### Core SDKs
- **@solana/web3.js** - Solana blockchain interaction
- **Mobile Wallet Adapter** - Wallet connection and signing
- **Anchor Integration** - Smart contract interaction
- **Metaplex Integration** - NFT and token standards

### Platform Support
- **React Native** - Cross-platform mobile development
- **Expo** - Simplified React Native development
- **Android** - Primary platform support
- **iOS** - Limited support (web3 features only)

## 📖 Essential Files for Mobile App Development

### Must-Read Foundation
1. `overview.md` - Core concepts
2. `setup.md` - Development environment
3. `quickstart.md` - Project creation

### Wallet Integration (Critical)
1. `using_mobile_wallet_adapter.md` - Connect to wallets
2. `storing_mwa_auth.md` - Persistent sessions
3. `mobile-wallet-adapter/overview.md` - Deep dive

### Blockchain Interaction
1. `making_rpc_requests.md` - Network communication
2. `building_transactions.md` - Transaction creation
3. `anchor_integration.md` - Smart contracts

### Complete Tutorials
1. `first_app_tutorial.md` - End-to-end app building
2. `mobile_nft_minter_tutorial.md` - NFT functionality

## 🔧 Development Tools & Resources

### Scaffold Projects
- `react-native-scaffold.md` - Pre-built templates
- `expo-dapp-template.md` - Expo starter projects

### Platform Compatibility
- `polyfill-guides/` - Cross-platform support
- `test-with-any-android-device.md` - Testing strategies

### Reference Documentation
- `reference/typescript/` - Complete API docs
- TypeScript interfaces and method signatures

## 🎯 Common Development Patterns

### 1. **Wallet Connection Flow**
```
App Launch → Check Stored Auth → Connect Wallet → Request Permissions → Store Session
```

### 2. **Transaction Flow**
```
Build Transaction → Sign with Wallet → Submit to Network → Monitor Status
```

### 3. **NFT Operations**
```
Connect Wallet → Fetch Metadata → Display Assets → Execute Operations
```

## 📱 Platform Considerations

### Android (Full Support)
- Complete Solana Mobile Stack
- Hardware wallet integration
- Seed Vault access

### iOS (Limited Support)
- Web3.js functionality only
- No Mobile Wallet Adapter
- Browser-based wallet connections

## 🔗 External Resources

- [Official Solana Mobile Documentation](https://docs.solanamobile.com/)
- [GitHub Repository](https://github.com/solana-mobile/solana-mobile-doc-site)
- [Solana Mobile Stack](https://docs.solanamobile.com/solana-mobile-stack/overview)

## 💡 Next Steps

1. Read `overview.md` to understand the ecosystem
2. Follow `quickstart.md` to create your first project
3. Complete `first_app_tutorial.md` for hands-on experience
4. Explore specific integration guides based on your app needs

---

**Note**: This documentation was downloaded from the official Solana Mobile documentation site and is current as of the download date. For the latest updates, refer to the online documentation.


---

## Original File: DOCUMENTATION_INDEX.md
---
# Solana Mobile React Native Documentation Index

**Total Files Downloaded: 58 documentation files**

## 📋 Complete File Inventory

### Core React Native Documentation (21 files)
- `overview.md` - Platform overview
- `quickstart.md` - Quick project setup
- `quickstart_legacy.md` - Legacy setup guide
- `setup.md` - Development environment
- `test-with-any-android-device.md` - Testing setup
- `making_rpc_requests.md` - Solana network communication
- `building_transactions.md` - Transaction construction
- `using_mobile_wallet_adapter.md` - Wallet integration
- `storing_mwa_auth.md` - Auth persistence
- `anchor_integration.md` - Anchor framework
- `metaplex_integration.md` - NFT/Token standards
- `first_app_tutorial.md` - Complete app tutorial
- `hello_world_tutorial.md` - Basic tutorial
- `mobile_nft_minter_tutorial.md` - NFT tutorial
- `expo.md` - Expo development
- `expo-dapp-template.md` - Expo templates
- `react-native-scaffold.md` - Scaffold projects

### Polyfill Guides (4 files)
- `polyfill-guides/polyfills.md` - Overview
- `polyfill-guides/web3-js.md` - web3.js compatibility
- `polyfill-guides/spl-token.md` - SPL token support
- `polyfill-guides/anchor.md` - Anchor compatibility

### Mobile Wallet Adapter (7 files)
- `mobile-wallet-adapter/overview.md` - MWA overview
- `mobile-wallet-adapter/mobile-apps.md` - Mobile integration
- `mobile-wallet-adapter/web-apps.md` - Web integration
- `mobile-wallet-adapter/web-installation.md` - Web setup
- `mobile-wallet-adapter/diagrams.md` - Architecture diagrams
- `mobile-wallet-adapter/ux-guidelines.md` - UX best practices
- `mobile-wallet-adapter/migrating-to-wallet-standard.md` - Migration guide

### Getting Started (8 files)
- `getting-started/overview.md` - General overview
- `getting-started/intro.md` - Introduction
- `getting-started/introduction-to-solana.md` - Solana basics
- `getting-started/mobile-dapp-architecture.md` - Mobile architecture
- `getting-started/web3-and-mobile.md` - Web3 mobile concepts
- `getting-started/development-setup.md` - Dev environment
- `getting-started/saga-genesis-token.md` - Saga token info
- `getting-started/chapter2-preorder-tokens.md` - Token preorders

### TypeScript Reference (18 files)
- `reference/typescript/mobile-wallet-adapter.md` - MWA types
- `reference/typescript/mobile-wallet-adapter-legacy.md` - Legacy MWA types
- `reference/typescript/web3js.md` - web3.js types
- `reference/typescript/mobile-wallet-methods/` (8 method files)
- `reference/typescript/mobile-wallet-methods-legacy/` (8 legacy method files)

### Snippets & Templates (2 files)
- `_snippets/expo-dapp-template-snippet.mdx` - Expo template code
- `_snippets/react-native-scaffold-snippet.mdx` - Scaffold template code

### Images & Assets (5 files)
- React Native logo variants (SVG)
- Sample app screenshots (PNG)

## 🎯 Recommended Reading Order

### For Complete Beginners
1. `getting-started/introduction-to-solana.md`
2. `getting-started/web3-and-mobile.md`
3. `getting-started/mobile-dapp-architecture.md`
4. `overview.md`
5. `setup.md`
6. `first_app_tutorial.md`

### For React Native Developers New to Solana
1. `overview.md`
2. `quickstart.md`
3. `making_rpc_requests.md`
4. `building_transactions.md`
5. `using_mobile_wallet_adapter.md`
6. `anchor_integration.md`

### For Solana Developers New to Mobile
1. `getting-started/mobile-dapp-architecture.md`
2. `mobile-wallet-adapter/overview.md`
3. `polyfill-guides/polyfills.md`
4. `test-with-any-android-device.md`
5. `expo.md` or `react-native-scaffold.md`

## 🔧 Development Workflow

### Phase 1: Setup (Read First)
- `setup.md` - Environment configuration
- `quickstart.md` - Project creation
- `test-with-any-android-device.md` - Testing setup

### Phase 2: Core Development
- `making_rpc_requests.md` - Network layer
- `using_mobile_wallet_adapter.md` - Wallet integration
- `building_transactions.md` - Transaction handling

### Phase 3: Advanced Features
- `anchor_integration.md` - Smart contracts
- `metaplex_integration.md` - NFTs and tokens
- `storing_mwa_auth.md` - Session management

### Phase 4: Polish & Deploy
- `mobile-wallet-adapter/ux-guidelines.md` - UX best practices
- Platform-specific polyfills from `polyfill-guides/`

## 📚 Reference Materials

### API Documentation
- `reference/typescript/` - Complete TypeScript definitions
- Method-specific documentation in `mobile-wallet-methods/`

### Code Examples
- `_snippets/` - Reusable code templates
- Tutorial files contain working examples
- Scaffold projects provide full app templates

## 🚀 Quick Start Commands

After reading the documentation, you can start development with:

```bash
# Install Solana Mobile CLI
npm install -g @solana-mobile/cli

# Create new React Native project
npx @solana-mobile/dapp-scaffold create MyApp

# Or use Expo template
npx create-expo-app MyApp --template @solana-mobile/expo-dapp-template
```

Refer to `quickstart.md` for detailed setup instructions.
