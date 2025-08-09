# Solana Mobile Dev Quickstart

## Prerequisites
- Install Node.js, Yarn, Expo CLI (`npm install -g expo-cli`), Solana CLI, and Android SDK.
- Configure Solana CLI: `solana config set --url devnet` (use `mainnet-beta` for production).
- Set up Android emulator or device with USB debugging.

## Quick Start
npx @solana/create-solana-dapp
yarn add @solana-mobile/mobile-wallet-adapter-protocol
yarn add @solana-mobile/mobile-wallet-adapter-protocol-web3js
yarn add @solana/web3.js
npx expo run:android

## Deployment
eas build --profile production --platform android
solana-keygen new --outfile publisher-keypair.json
dapp-store create-app --keypair publisher-keypair.json
dapp-store create-release --keypair publisher-keypair.json
dapp-store publish --keypair publisher-keypair.json --requestor-is-authorized
