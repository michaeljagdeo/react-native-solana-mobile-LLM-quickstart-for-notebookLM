# 33 Android Native Rpc Communication

## Original File: building-json-rpc-requests.md
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import versions from '@site/sms-version-constants';

# Deep dive: Building JSON RPC request methods

This deep dive explains how to use underlying primitives in the `rpc-core` library to construct RPC requests for any methods that aren't provided by `SolanaRpcClient`.

:::tip
If you only need to use the common RPC methods already supported by `SolanaRpcClient`, read the [_RPC Client usage guide_](/android-native/rpc-requests).
:::

## Add dependencies

The [`rpc-core`](https://github.com/solana-mobile/rpc-core) library provides core classes and abstractions to build and submit requests according to the JSON-RPC 2.0 specification.

<Tabs>
<TabItem value="build.gradle.kts" label="build.gradle.kts">

<pre><code language="groovy">

{
`dependencies {
    implementation("com.solanamobile:rpc-core:${versions.KOTLIN_RPC_CORE_VERSION}")
}`
}

</code></pre>

</TabItem>
</Tabs>

## Creating a JSON RPC Request

The `rpc-core` library defines a `JsonRpc20Request` constructor to conveniently construct a Solana JSON RPC request.

Populate the JSON object with the method name and JSON serialized parameters of a [Solana RPC method](https://docs.solana.com/api/http). The
constructor also includes a `requestId` parameter, as per JSON-RPC spec.

### Example: `getLatestBlockhash` RPC request

```kotlin
fun createBlockhashRequest(commitment: String = "confirmed", requestId: String = "1") =
    JsonRpc20Request(
        // JSON RPC Method (ie: `getLatestBlockhash`, `getSignatureForAddresses`)
        method = "getLatestBlockhash",
        // Populate with JSON parameters
        params = buildJsonArray {
            addJsonObject {
                put("commitment", commitment)
            }
        },
        requestId
    )
```

## Defining the JSON RPC Response

After creating the request, create [Kotlin serializable classes](https://kotlinlang.org/docs/serialization.html#libraries) that define the expected response payload for that request.

In the following example, we are defining the expected response of the `getLatestBlockhash` request using the `kotlinx.serialization` library.

### Example: `getLatestBlockhash` RPC response

```kotlin
import kotlinx.serialization.Serializable
import kotlinx.serialization.json.*

@Serializable
class BlockhashResponse(val value: BlockhashInfo)

@Serializable
class BlockhashInfo(
    val blockhash: String,
    val lastValidBlockHeight: Long
)

// Additionally, define an exception in case of failure during request
class BlockhashException(message: String? = null, cause: Throwable? = null) : RuntimeException(message, cause)
```

## Implement `HttpNetworkDriver`

The `rpc-core` library defines a `HttpNetworkDriver` interface that is used to make network requests.

```kotlin
interface HttpRequest {
    val url: String
    val method: String
    val properties: Map<String, String>
    val body: String?
}

interface HttpNetworkDriver {
    suspend fun makeHttpRequest(request: HttpRequest): String
}
```

You can use a common networking package like the Ktor library to implement the `makeHttpRequest` method. The following
is an example from the [Kotlin Jetpack Compose Scaffold sample app](https://github.com/solana-mobile/solana-kotlin-compose-scaffold/blob/main/app/src/main/java/com/example/solanakotlincomposescaffold/networking/HttpDriver.kt).

```kotlin
import com.solana.networking.HttpNetworkDriver
import com.solana.networking.HttpRequest
import io.ktor.client.request.*
import io.ktor.client.HttpClient
import io.ktor.client.engine.android.Android
import io.ktor.client.statement.bodyAsText
import io.ktor.http.HttpMethod

class KtorHttpDriver : HttpNetworkDriver {
    override suspend fun makeHttpRequest(request: HttpRequest): String =
        HttpClient(Android).use { client ->
            client.request(request.url) {
                method = HttpMethod.parse(request.method)
                request.properties.forEach { (k, v) ->
                    header(k, v)
                }
                setBody(request.body)
            }.bodyAsText()
        }
}
```

## Sending RPC requests

After putting these parts together, use the `Rpc20Driver` class to point to an RPC uri, send
the request, and receive a response.

```kotlin
// import com.example.solanakotlincomposescaffold.networking.KtorHttpDriver
import com.solana.networking.Rpc20Driver
import com.solana.rpccore.JsonRpc20Request
import com.solana.transaction.Blockhash
import java.util.UUID

fun getLatestBlockhash(): Blockhash {
    // Create the Rpc20Driver and specify the RPC uri and network driver
    val rpc = Rpc20Driver("https://api.devnet.solana.com", KtorHttpDriver())

    // Construct the RPC request
    val requestId = UUID.randomUUID().toString()
    val request = createBlockhashRequest(commitment, requestId)

    // Send the request and provide the serializer for the expected response
    val response = rpc.makeRequest(request, BlockhashResponse.serializer())

    response.error?.let { error ->
        throw BlockhashException("Could not fetch latest blockhash: ${error.code}, ${error.message}")
    }

    // Unwrap the response to receive the base58 blockhash string
    val base58Blockhash = response.result?.value?.blockhash

    // Return a `Blockhash` object from the web3-solana library
    Blockhash.from(base58Blockhash
        ?: throw BlockhashException("Could not fetch latest blockhash: UnknownError"))
}
```

## Next steps

- Browse the [full list](https://docs.solana.com/api/http) of Solana RPC HTTP Methods


---

## Original File: rpc-requests.md
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# RPC Client Usage guide

To interface with the Solana network, a client needs to construct and send [_JSON RPC requests_](https://docs.solana.com/api/http) to an [_RPC endpoint_](https://docs.solana.com/cluster/rpc-endpoints).

This guide will teach you how to use the `SolanaRpcClient` and send these RPC requests.

## Add dependencies

The [`rpc-core`](https://github.com/solana-mobile/rpc-core) library provides a convenient `SolanaRpcClient` that implements an API to call these RPC methods and return responses.

<Tabs>
<TabItem value="build.gradle.kts" label="build.gradle.kts">

```groovy
dependencies {
    implementation("com.solanamobile:rpc-core:0.2.6")
}
```

</TabItem>
</Tabs>

## Create an RPC Client

To create an instance of a `SolanaRpcClient`, pass in:

- an RPC `url` that the client will send requests.
- a `networkDriver` used to send HTTP requests.

In this example, we construct an RPC client pointed at devnet and Ktor as a network driver:

```kotlin
import com.solana.rpc.SolanaRpcClient
import com.solana.networking.KtorNetworkDriver

val rpcClient = SolanaRpcClient("https://api.devnet.solana.com", KtorNetworkDriver())
```

## Example: Fetching latest blockhash

Calling the `getLatestBlockhash` method returns an [`RpcResponse`](https://github.com/solana-mobile/rpc-core/blob/a6ea1466fb1d79113ca77f2d073d33a85fb5ad5e/rpccore/src/commonMain/kotlin/com/solana/rpccore/RpcResponse.kt#L6).

- If successful, the response result will contain a [`BlockhashResult`](https://github.com/solana-mobile/rpc-core/blob/a6ea1466fb1d79113ca77f2d073d33a85fb5ad5e/solanaclient/src/commonMain/kotlin/com/solana/rpc/SolanaRpcClient.kt#L232).

- If an error occured, the response will contain an [`RpcError`](https://github.com/solana-mobile/rpc-core/blob/a6ea1466fb1d79113ca77f2d073d33a85fb5ad5e/rpccore/src/commonMain/kotlin/com/solana/rpccore/RpcResponse.kt#L16).

```kotlin
import com.solana.rpc.SolanaRpcClient
import com.solana.networking.KtorNetworkDriver

val rpcClient = SolanaRpcClient("https://api.devnet.solana.com", KtorNetworkDriver())

val response = rpcClient.getLatestBlockhash()

if (response.result) {
    println("Latest blockhash: ${response.result.blockhash}")
} else if (response.error) {
    println("Failed to fetch latest blockhash: ${response.error.message}")
}
```

## Example: Sending a transaction

To submit a transaction to the RPC, use the `sendTransaction` method.

- If successful, the response result will contain a transaction signature string.

- If an error occured, the response will contain an [`RpcError`](https://github.com/solana-mobile/rpc-core/blob/a6ea1466fb1d79113ca77f2d073d33a85fb5ad5e/rpccore/src/commonMain/kotlin/com/solana/rpccore/RpcResponse.kt#L16).

```kotlin
import com.solana.rpc.SolanaRpcClient
import com.solana.networking.KtorNetworkDriver

val rpcClient = SolanaRpcClient("https://api.devnet.solana.com", KtorNetworkDriver())

val transaction = Transaction(/* ... */)

/* ...sign the transaction... */

val response = rpc.sendTransaction(transaction)

if (response.result) {
    println("Transaction signature: ${response.result}")
} else if (response.error) {
    println("Failed to send transaction: ${response.error.message}")
}

```

## Next steps

These examples are just some of the methods supported by `SolanaRpcClient`. Here are suggestions to continue learning:

- Read the following guide to learn how to build Solana program instructions and transactions.
- For a complete reference of the RPC methods supported, view the `SolanaRpcClient` [source code](https://github.com/solana-mobile/rpc-core/blob/main/solanaclient/src/commonMain/kotlin/com/solana/rpc/SolanaRpcClient.kt) and [unit tests](https://github.com/solana-mobile/rpc-core/blob/main/solanaclient/src/commonTest/kotlin/com/solana/rpc/RpcClientTests.kt).
- Read the [_Building JSON RPC requests_ deep dive](/android-native/building-json-rpc-requests) to learn how to create requests for RPC methods that aren't immediately supported by `SolanaRpcClient`.
