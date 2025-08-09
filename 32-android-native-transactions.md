# 32 Android Native Transactions

## Original File: building_transactions.md
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import versions from '@site/sms-version-constants';

# Building Solana transactions

A client interacts with the Solana network by submitting a _transaction_ to the cluster. Transactions
allow a client to invoke instructions of on-chain [_Programs_](https://docs.solana.com/developing/intro/programs).

For a full explanation, see the core docs overview of a [_transaction_](https://docs.solana.com/developing/programming-model/transactions).

## Add dependencies

The [`web3-solana`](https://github.com/solana-mobile/web3-core) library provides the abstraction classes like `Transaction` and `AccountMeta` to simplify building Solana transactions.

<Tabs>
<TabItem value="build.gradle.kts" label="build.gradle.kts">

<pre><code language="groovy">

{
`dependencies {
    implementation("com.solanamobile:web3-solana:${versions.KOTLIN_WEB3_SOLANA_VERSION}")
}`
}

</code></pre>

</TabItem>
</Tabs>

## Example: Memo Program Transaction

In the following example, we are creating a `Transaction` that invokes the [Memo Program](https://spl.solana.com/memo) to publish the message "Hello Solana" on-chain.

### Create an instruction

A transaction instruction is comprised of a program id, a list of accounts, and instruction data specific to the program.

To create an instruction, define a list of `AccountMeta` that represent the accounts required by the instruction.
Then pass the encoded message as `data` into the `TransactionInstruction` constructor.

```kotlin
import com.solana.publickey.*
import com.solana.transaction.*

// Solana Memo Program
val memoProgramId = "MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr"
val memoProgramIdKey = SolanaPublicKey.from(memoProgramId)

// Construct the instruction
val message = "Hello Solana!"
val memoInstruction = TransactionInstruction(
    memoProgramIdKey,
    // Define the accounts in instruction
    listOf(AccountMeta(address, true, true)),
    // Pass in the instruction data as ByteArray
    message.encodeToByteArray()
)
```

### Create the Memo transaction

After creating the instructions, use `Message.Builder()` to assemble the instructions and a _blockhash_ to construct the a _Transaction message_. Then
pass the transaction message into the `Transaction` constructor.

See the previous _Making RPC Requests_ guide for an example of how to fetch a blockhash.

```kotlin
import com.solana.rpc.SolanaRpcClient
import com.solana.networking.KtorNetworkDriver

// Fetch blockhash from RPC
val rpcClient = SolanaRpcClient("https://api.devnet.solana.com", KtorNetworkDriver())
val blockhasResponse = rpcClient.getLatestBlockhash()

if (response.error) {
    println("Failed to fetch latest blockhash: ${response.error.message}")
    return;
}

// Build transaction message
val memoTxMessage = Message.Builder()
    // highlight-next-line
    .addInstruction(memoInstruction) // Pass in instruction from previous step
    .setRecentBlockhash(blockhasResponse.result!!.blockhash)
    .build()

// Construct the Transaction object from the message
val unsignedTx = Transaction(memoTxMessage)
```

## Next steps

Read the following _Using Mobile Wallet Adapter_ guide to learn how to sign these transactions and submit them to the Solana network.


---

## Original File: sending-sol.md
---
# Example: Sign and send a SOL transfer

This example will walkthrough the steps to build a transaction that sends SOL from a user's wallet to another wallet.

To achieve this, you will write code that:

1. Builds a transaction that invokes the System Program's `transfer` instruction
2. Connects to the user's wallet to retrieve their wallet address
3. Prompt the user to sign the transaction with Mobile Wallet Adapter

## Build a transfer transaction

Write a helper method `buildTransferTransaction` that handles creating the transfer instruction and assembling it into a `Transaction`.

You can use the `SystemProgram.transfer` util method provided by `web3-solana` to conveniently generate a serialized transfer instruction.

```kotlin
import com.solana.publickey.SolanaPublicKey
import com.solana.transaction.*

fun buildTransferTransaction(
    blockhash: String,
    fromPublicKey: SolanaPublicKey,
    toPublicKey: SolanaPublicKey,
    lamports: Long
): Transaction {
    val transferTxMessage = Message.Builder()
        .addInstruction(
            SystemProgram.transfer(
                fromPublicKey,
                toPublicKey,
                lamports
            )
        )
        .setRecentBlockhash(blockhash)
        .build()

    return Transaction(transferTxMessage)
}
```

:::tip

## Fetching the latest blockhash

In this method, we use a `blockhash` parameter. See this [RPC requests guide](/android-native/rpc-requests#example-fetching-latest-blockhash) for an example.

:::tip

## Instantiate Mobile Wallet Adapter client

In your app, instantiate a `MobileWalletAdapter` client instance, that will be used to establish a session with the user's mobile wallet app.

```kotlin
import com.solana.mobilewalletadapter.clientlib.*

// Define dApp's identity metadata
val solanaUri = Uri.parse("https://yourdapp.com")
val iconUri = Uri.parse("favicon.ico") // resolves to https://yourdapp.com/favicon.ico
val identityName = "Solana Kotlin Transfer Example"

// Construct the client
val walletAdapter = MobileWalletAdapter(connectionIdentity = ConnectionIdentity(
    identityUri = solanaUri,
    iconUri = iconUri,
    identityName = identityName
))
```

## Connect to the user's wallet

Assuming you do not currently have the user's wallet address available, you can use Mobile Wallet Adapter
to connect to their mobile wallet app, and learn what their wallet address is.

```kotlin
import com.funkatronics.encoders.Base58
import com.solana.publickey.SolanaPublicKey
import com.solana.mobilewalletadapter.clientlib.*
import com.solana.rpc.SolanaRpcClient
import com.solana.networking.KtorNetworkDriver

 // `this` is the current Android activity
val sender = ActivityResultSender(this)

// Instantiate the MWA client object
val walletAdapter = MobileWalletAdapter(/* ... */)

val lamportAmount = 1000000
val result = walletAdapter.transact(sender) { authResult ->
    // Retrieve the user wallet address from the MWA authResult
    val userAccountAddress = SolanaPublicKey(authResult.accounts.first().publicKey)

    // Fetch latest blockhash
    val rpcClient = SolanaRpcClient("https://api.devnet.solana.com", KtorNetworkDriver())
    val blockhashResponse = rpcClient.getLatestBlockhash()

    // Use the wallet address to build the transfer transaction
    val transferTx = buildTransferTransaction(
        blockhashResponse.result!!.blockhash,
        userAccountAddress,
        SolanaPublicKey("<address_of_recipient>"),
        lamportAmount
    );

    // ...
}
```

## Sign and send the transaction

Finally, issue a `signAndSendTransactions` request, prompting the user to sign the transfer transaction
and submit it to the network.

```kotlin
import com.funkatronics.encoders.Base58
import com.solana.publickey.SolanaPublicKey
import com.solana.mobilewalletadapter.clientlib.*

 // `this` is the current Android activity
val sender = ActivityResultSender(this)

// Instantiate the MWA client object
val walletAdapter = MobileWalletAdapter(/* ... */)

val result = walletAdapter.transact(sender) { authResult ->
    // Build a transaction using web3-solana classes
    val userAccountAddress = SolanaPublicKey(authResult.accounts.first().publicKey)

    // Fetch latest blockhash
    val rpcClient = SolanaRpcClient("https://api.devnet.solana.com", KtorNetworkDriver())
    val blockhashResponse = rpcClient.getLatestBlockhash()

    // Use the wallet address to build the transfer transaction
    val transferTx = buildTransferTransaction(
        blockhashResponse.result!!.blockhash,
        userAccountAddress,
        SolanaPublicKey("<address_of_recipient>"),
        lamportAmount
    );

    // Issue a 'signTransactions' request
    signAndSendTransactions(arrayOf(transferTx.serialize()));
}

// Read the results!
when (result) {
    is TransactionResult.Success -> {
        val txSignatureBytes = result.successPayload?.signatures?.first()
        txSignatureBytes?.let {
            println("Transaction signature: " + Base58.encodeToString(signedTxBytes))
        }
    }
    is TransactionResult.NoWalletFound -> {
        println("No MWA compatible wallet app found on device.")
    }
    is TransactionResult.Failure -> {
        println("Error during signing and sending transactions: " + result.e.message)
    }
}
```

If signing is successful, you can check the returned `result` for the transaction signature, or handle failure cases.

## Next steps

Check out the [Kotlin Compose Scaffold](https://github.com/solana-mobile/solana-kotlin-compose-scaffold) for a code examples of what is discussed in this guide, and an easy launching point to getting started with Solana Kotlin development!
