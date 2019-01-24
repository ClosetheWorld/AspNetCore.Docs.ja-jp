---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core SignalR アプリケーションを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/06/2018
uid: signalr/configuration
ms.openlocfilehash: bb18ba242584afa7181dcc19a5295f86996aeaa3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837521"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="858aa-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="858aa-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="858aa-104">JSON/MessagePack シリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="858aa-105">ASP.NET Core SignalR には、メッセージをエンコードするための 2 つのプロトコルがサポートされています。[JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)します。</span><span class="sxs-lookup"><span data-stu-id="858aa-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="858aa-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="858aa-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="858aa-107">JSON のシリアル化を使用して、サーバーで構成できます、 [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドは後に、追加する[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)で、`Startup.ConfigureServices`メソッド。</span><span class="sxs-lookup"><span data-stu-id="858aa-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="858aa-108">`AddJsonProtocol`メソッドが受信するデリゲートを受け取り、`options`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="858aa-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="858aa-109">[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)そのオブジェクトのプロパティは、JSON.NET`JsonSerializerSettings`引数のシリアル化の構成し、戻り値を使用できるオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="858aa-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="858aa-110">参照してください、 [JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)の詳細。</span><span class="sxs-lookup"><span data-stu-id="858aa-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="858aa-111">たとえば、「キャメル ケース」の既定名ではなく"PascalCase"プロパティの名前を使用するシリアライザーを構成する次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="858aa-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
        new DefaultContractResolver();
    });
```

<span data-ttu-id="858aa-112">.NET クライアントで同じ`AddJsonProtocol`に拡張メソッドが存在する[HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)します。</span><span class="sxs-lookup"><span data-stu-id="858aa-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="858aa-113">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するのには名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="858aa-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="858aa-114">この時点で、JavaScript クライアント内で JSON のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="858aa-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="858aa-115">MessagePack シリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-115">MessagePack serialization options</span></span>

<span data-ttu-id="858aa-116">デリゲートを提供することで MessagePack シリアル化を構成することができます、 [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出します。</span><span class="sxs-lookup"><span data-stu-id="858aa-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="858aa-117">参照してください[SignalR で MessagePack](xref:signalr/messagepackhubprotocol)の詳細。</span><span class="sxs-lookup"><span data-stu-id="858aa-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="858aa-118">この時点で、JavaScript クライアント内で MessagePack シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="858aa-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="858aa-119">サーバー オプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-119">Configure server options</span></span>

<span data-ttu-id="858aa-120">次の表では、SignalR ハブを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="858aa-120">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="858aa-121">オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-121">Option</span></span> | <span data-ttu-id="858aa-122">既定値</span><span class="sxs-lookup"><span data-stu-id="858aa-122">Default Value</span></span> | <span data-ttu-id="858aa-123">説明</span><span class="sxs-lookup"><span data-stu-id="858aa-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="858aa-124">15 秒</span><span class="sxs-lookup"><span data-stu-id="858aa-124">15 seconds</span></span> | <span data-ttu-id="858aa-125">クライアントがこの時間間隔内で最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="858aa-125">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="858aa-126">これは、重大なネットワーク待機時間が原因のハンドシェイクのタイムアウト エラーが発生している場合にのみ変更する高度な設定です。</span><span class="sxs-lookup"><span data-stu-id="858aa-126">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="858aa-127">ハンドシェイク プロセスの詳細については、次を参照してください。、 [SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)します。</span><span class="sxs-lookup"><span data-stu-id="858aa-127">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="858aa-128">15 秒</span><span class="sxs-lookup"><span data-stu-id="858aa-128">15 seconds</span></span> | <span data-ttu-id="858aa-129">サーバーがこの間隔内でメッセージを送信していない場合、接続を開いたままに ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="858aa-129">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="858aa-130">変更するときに`KeepAliveInterval`、変更、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントに設定します。</span><span class="sxs-lookup"><span data-stu-id="858aa-130">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="858aa-131">推奨される`ServerTimeout` / `serverTimeoutInMilliseconds`値が double、`KeepAliveInterval`値。</span><span class="sxs-lookup"><span data-stu-id="858aa-131">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="858aa-132">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="858aa-132">All installed protocols</span></span> | <span data-ttu-id="858aa-133">このハブでサポートされるプロトコル。</span><span class="sxs-lookup"><span data-stu-id="858aa-133">Protocols supported by this hub.</span></span> <span data-ttu-id="858aa-134">既定では、サーバーに登録されているプロトコルをすべて許可されますが、プロトコルは、個々 のハブに対する特定のプロトコルを無効にするには、この一覧から削除できます。</span><span class="sxs-lookup"><span data-stu-id="858aa-134">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="858aa-135">場合`true`詳細のハブ メソッドで例外がスローされたときに、例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="858aa-135">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="858aa-136">既定値は`false`、これらの例外メッセージは、機密情報を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="858aa-136">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="858aa-137">オプションのデリゲートを提供することですべてのハブのオプションを構成でき、`AddSignalR`呼び出す`Startup.ConfigureServices`します。</span><span class="sxs-lookup"><span data-stu-id="858aa-137">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="858aa-138">1 つのハブのオプションで提供されるグローバル オプションを上書き`AddSignalR`を使用して構成できると[AddHubOptions\<T >](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span><span class="sxs-lookup"><span data-stu-id="858aa-138">Options for a single hub override the global options provided in `AddSignalR` and can be configured using [AddHubOptions\<T>](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

<span data-ttu-id="858aa-139">使用`HttpConnectionDispatcherOptions`トランスポートおよびメモリ バッファーの管理に関連する高度な設定を構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-139">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="858aa-140">これらのオプションを構成するデリゲートを渡すことによって[MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)します。</span><span class="sxs-lookup"><span data-stu-id="858aa-140">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub).</span></span>

| <span data-ttu-id="858aa-141">オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-141">Option</span></span> | <span data-ttu-id="858aa-142">既定値</span><span class="sxs-lookup"><span data-stu-id="858aa-142">Default Value</span></span> | <span data-ttu-id="858aa-143">説明</span><span class="sxs-lookup"><span data-stu-id="858aa-143">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="858aa-144">32 KB</span><span class="sxs-lookup"><span data-stu-id="858aa-144">32 KB</span></span> | <span data-ttu-id="858aa-145">クライアントから受信したバイトの最大数をサーバーのバッファー。</span><span class="sxs-lookup"><span data-stu-id="858aa-145">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="858aa-146">この値を増やすことによりより大きなメッセージを受信するサーバーが、メモリ消費量に悪影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="858aa-146">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="858aa-147">データが自動的に収集、`Authorize`ハブ クラスに適用される属性。</span><span class="sxs-lookup"><span data-stu-id="858aa-147">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="858aa-148">一連の[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトは、クライアントがハブへの接続を承認されているかどうかを判断するために使用します。</span><span class="sxs-lookup"><span data-stu-id="858aa-148">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="858aa-149">32 KB</span><span class="sxs-lookup"><span data-stu-id="858aa-149">32 KB</span></span> | <span data-ttu-id="858aa-150">アプリによって送信されたバイト数の最大数をサーバーのバッファー。</span><span class="sxs-lookup"><span data-stu-id="858aa-150">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="858aa-151">この値を増やすことによりより大きなメッセージを送信するサーバーが、メモリ消費量に悪影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="858aa-151">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="858aa-152">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="858aa-152">All Transports are enabled.</span></span> | <span data-ttu-id="858aa-153">ビットマスク`HttpTransportType`トランスポートを制限する値をクライアントが接続に使用できます。</span><span class="sxs-lookup"><span data-stu-id="858aa-153">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="858aa-154">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="858aa-154">See below.</span></span> | <span data-ttu-id="858aa-155">長いポーリングのトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="858aa-155">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="858aa-156">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="858aa-156">See below.</span></span> | <span data-ttu-id="858aa-157">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="858aa-157">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="858aa-158">ポーリングの長いトランスポートを使用して構成できるその他のオプションを持つ、`LongPolling`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="858aa-158">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="858aa-159">オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-159">Option</span></span> | <span data-ttu-id="858aa-160">既定値</span><span class="sxs-lookup"><span data-stu-id="858aa-160">Default Value</span></span> | <span data-ttu-id="858aa-161">説明</span><span class="sxs-lookup"><span data-stu-id="858aa-161">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="858aa-162">90 秒</span><span class="sxs-lookup"><span data-stu-id="858aa-162">90 seconds</span></span> | <span data-ttu-id="858aa-163">1 つのポーリング要求を終了する前に、クライアントに送信するメッセージのサーバーが待機する最長時間。</span><span class="sxs-lookup"><span data-stu-id="858aa-163">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="858aa-164">新しいポーリング要求をより頻繁に発行するクライアントは、この値を小さきます。</span><span class="sxs-lookup"><span data-stu-id="858aa-164">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="858aa-165">WebSocket トランスポートが追加のオプションを使用して構成できる、`WebSockets`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="858aa-165">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="858aa-166">オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-166">Option</span></span> | <span data-ttu-id="858aa-167">既定値</span><span class="sxs-lookup"><span data-stu-id="858aa-167">Default Value</span></span> | <span data-ttu-id="858aa-168">説明</span><span class="sxs-lookup"><span data-stu-id="858aa-168">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="858aa-169">5 秒</span><span class="sxs-lookup"><span data-stu-id="858aa-169">5 seconds</span></span> | <span data-ttu-id="858aa-170">サーバーが閉じ、クライアントがこの時間間隔内に閉じるが失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="858aa-170">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="858aa-171">設定するために使用するデリゲート、`Sec-WebSocket-Protocol`ヘッダーをカスタム値。</span><span class="sxs-lookup"><span data-stu-id="858aa-171">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="858aa-172">デリゲートは、入力としてクライアントによって要求された値を受け取るし、目的の値を返します。</span><span class="sxs-lookup"><span data-stu-id="858aa-172">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="858aa-173">クライアント オプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-173">Configure client options</span></span>

<span data-ttu-id="858aa-174">クライアント オプションを構成できる、 `HubConnectionBuilder` (.NET と JavaScript の両方のクライアントで使用できる)、型ではなく、`HubConnection`自体。</span><span class="sxs-lookup"><span data-stu-id="858aa-174">Client options can be configured on the `HubConnectionBuilder` type (available in both .NET and JavaScript clients), as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="858aa-175">ログを構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-175">Configure logging</span></span>

<span data-ttu-id="858aa-176">.NET を使用してクライアントでログ記録が構成されている、`ConfigureLogging`メソッド。</span><span class="sxs-lookup"><span data-stu-id="858aa-176">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="858aa-177">サーバー上にある、同じ方法でログ記録プロバイダーおよびフィルターを登録できます。</span><span class="sxs-lookup"><span data-stu-id="858aa-177">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="858aa-178">参照してください、 [ASP.NET Core でのログ記録](xref:fundamentals/logging/index)詳細についてはドキュメントです。</span><span class="sxs-lookup"><span data-stu-id="858aa-178">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="858aa-179">ログ プロバイダーを登録するために必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="858aa-179">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="858aa-180">参照してください、[組み込みのログ プロバイダー](xref:fundamentals/logging/index#built-in-logging-providers)完全な一覧については、ドキュメントのセクション。</span><span class="sxs-lookup"><span data-stu-id="858aa-180">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="858aa-181">たとえば、コンソールのログ記録を有効にするインストール、 `Microsoft.Extensions.Logging.Console` NuGet パッケージ。</span><span class="sxs-lookup"><span data-stu-id="858aa-181">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="858aa-182">呼び出す、`AddConsole`拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="858aa-182">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="858aa-183">同様に、JavaScript クライアント内で`configureLogging`メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="858aa-183">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="858aa-184">提供、`LogLevel`を生成するログ メッセージの最小レベルを示す値。</span><span class="sxs-lookup"><span data-stu-id="858aa-184">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="858aa-185">ブラウザーのコンソール ウィンドウには、ログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="858aa-185">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="858aa-186">完全ログ記録を無効にするには指定`signalR.LogLevel.None`で、`configureLogging`メソッド。</span><span class="sxs-lookup"><span data-stu-id="858aa-186">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="858aa-187">JavaScript クライアントに利用可能なログ レベルは、以下に示します。</span><span class="sxs-lookup"><span data-stu-id="858aa-187">Log levels available to the JavaScript client are listed below.</span></span> <span data-ttu-id="858aa-188">ログ レベルをこれらの値のいずれかに設定でメッセージのログ記録を有効になります**以上**レベル。</span><span class="sxs-lookup"><span data-stu-id="858aa-188">Setting the log level to one of these values enables logging of messages at **or above** that level.</span></span>

| <span data-ttu-id="858aa-189">レベル</span><span class="sxs-lookup"><span data-stu-id="858aa-189">Level</span></span> | <span data-ttu-id="858aa-190">説明</span><span class="sxs-lookup"><span data-stu-id="858aa-190">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="858aa-191">メッセージは記録されません。</span><span class="sxs-lookup"><span data-stu-id="858aa-191">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="858aa-192">アプリ全体で障害を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="858aa-192">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="858aa-193">現在の操作の失敗を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="858aa-193">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="858aa-194">致命的でない問題を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="858aa-194">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="858aa-195">情報メッセージ。</span><span class="sxs-lookup"><span data-stu-id="858aa-195">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="858aa-196">診断メッセージのデバッグに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="858aa-196">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="858aa-197">非常に詳細な診断メッセージが特定の問題を診断するために設計されています。</span><span class="sxs-lookup"><span data-stu-id="858aa-197">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

### <a name="configure-allowed-transports"></a><span data-ttu-id="858aa-198">許可されているトランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-198">Configure allowed transports</span></span>

<span data-ttu-id="858aa-199">SignalR によって使用されるトランスポートで構成できる、`WithUrl`を呼び出す (`withUrl` JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="858aa-199">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="858aa-200">値のビットごとの OR`HttpTransportType`のみ指定されたトランスポートを使用するクライアントを制限するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="858aa-200">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="858aa-201">既定では、すべてのトランスポートが有効にします。</span><span class="sxs-lookup"><span data-stu-id="858aa-201">All transports are enabled by default.</span></span>

<span data-ttu-id="858aa-202">たとえば、Server-Sent イベント転送を無効にすることを許可する WebSockets および長いポーリングの接続。</span><span class="sxs-lookup"><span data-stu-id="858aa-202">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="858aa-203">設定して、JavaScript クライアントでのトランスポートが構成されている、`transport`フィールドに指定されたオプションのオブジェクトに`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="858aa-203">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="858aa-204">ベアラー認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-204">Configure bearer authentication</span></span>

<span data-ttu-id="858aa-205">SignalR 要求と共に認証データを提供するには、使用、`AccessTokenProvider`オプション (`accessTokenFactory` JavaScript で) 必要なアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="858aa-205">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="858aa-206">.NET クライアントでこのアクセス トークンとして渡される HTTP「ベアラー認証」トークン (を使用して、`Authorization`ヘッダーの型を持つ`Bearer`)。</span><span class="sxs-lookup"><span data-stu-id="858aa-206">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="858aa-207">アクセス トークンをベアラー トークンとして使用、JavaScript クライアントで**を除く**ブラウザー Api が (具体的には、Server-Sent イベントと Websocket の要求) のヘッダーを適用する機能を制限するような場合。</span><span class="sxs-lookup"><span data-stu-id="858aa-207">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="858aa-208">このような場合は、アクセス トークンはクエリ文字列値として指定`access_token`します。</span><span class="sxs-lookup"><span data-stu-id="858aa-208">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="858aa-209">.NET クライアントで、`AccessTokenProvider`でオプションのデリゲートを使用してオプションを指定することができます`WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="858aa-209">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="858aa-210">設定して、JavaScript クライアントでアクセス トークンが構成されている、`accessTokenFactory`フィールド内のオプション オブジェクトに`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="858aa-210">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="858aa-211">タイムアウトとキープ アライブ オプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-211">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="858aa-212">タイムアウトとキープ アライブ動作を構成するための追加のオプションがあります、`HubConnection`オブジェクト自体。</span><span class="sxs-lookup"><span data-stu-id="858aa-212">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

| <span data-ttu-id="858aa-213">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-213">.NET Option</span></span> | <span data-ttu-id="858aa-214">JavaScript のオプション</span><span class="sxs-lookup"><span data-stu-id="858aa-214">JavaScript Option</span></span> | <span data-ttu-id="858aa-215">既定値</span><span class="sxs-lookup"><span data-stu-id="858aa-215">Default Value</span></span> | <span data-ttu-id="858aa-216">説明</span><span class="sxs-lookup"><span data-stu-id="858aa-216">Description</span></span> |
| ----------- | ----------------- | ------------- | ----------- |
| `ServerTimeout` | `serverTimeoutInMilliseconds` | <span data-ttu-id="858aa-217">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="858aa-217">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="858aa-218">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="858aa-218">Timeout for server activity.</span></span> <span data-ttu-id="858aa-219">サーバーは、この間隔でメッセージを送信していない、クライアントと見なされ、サーバーが切断されているトリガー、`Closed`イベント (`onclose` JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="858aa-219">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="858aa-220">この値は、サーバーから送信される ping メッセージに十分な大きさである必要があります**と**タイムアウト間隔内に、クライアントによって受信されます。</span><span class="sxs-lookup"><span data-stu-id="858aa-220">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="858aa-221">推奨値は数値、サーバーに少なくとも 2 倍の`KeepAliveInterval`の ping が到着するまでの値。</span><span class="sxs-lookup"><span data-stu-id="858aa-221">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="858aa-222">構成できません。</span><span class="sxs-lookup"><span data-stu-id="858aa-222">Not configurable</span></span> | <span data-ttu-id="858aa-223">15 秒</span><span class="sxs-lookup"><span data-stu-id="858aa-223">15 seconds</span></span> | <span data-ttu-id="858aa-224">サーバーの初期ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="858aa-224">Timeout for initial server handshake.</span></span> <span data-ttu-id="858aa-225">サーバーは、この間隔でハンドシェイクの応答を送信しない場合、は、クライアントがキャンセル ハンドシェイクとトリガー、`Closed`イベント (`onclose` JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="858aa-225">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="858aa-226">これは、重大なネットワーク待機時間が原因のハンドシェイクのタイムアウト エラーが発生している場合にのみ変更する高度な設定です。</span><span class="sxs-lookup"><span data-stu-id="858aa-226">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="858aa-227">ハンドシェイク プロセスの詳細については、次を参照してください。、 [SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)します。</span><span class="sxs-lookup"><span data-stu-id="858aa-227">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="858aa-228">として、.NET クライアントでタイムアウト値が指定された`TimeSpan`値。</span><span class="sxs-lookup"><span data-stu-id="858aa-228">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span> <span data-ttu-id="858aa-229">JavaScript クライアントでは、タイムアウト値は、期間 (ミリ秒) を示す数値として指定されます。</span><span class="sxs-lookup"><span data-stu-id="858aa-229">In the JavaScript client, timeout values are specified as a number indicating the duration in milliseconds.</span></span>

### <a name="configure-additional-options"></a><span data-ttu-id="858aa-230">追加のオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="858aa-230">Configure additional options</span></span>

<span data-ttu-id="858aa-231">追加のオプションを構成することができます、 `WithUrl` (`withUrl` JavaScript で) メソッドを`HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="858aa-231">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder`:</span></span>

| <span data-ttu-id="858aa-232">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="858aa-232">.NET Option</span></span> | <span data-ttu-id="858aa-233">JavaScript のオプション</span><span class="sxs-lookup"><span data-stu-id="858aa-233">JavaScript Option</span></span> | <span data-ttu-id="858aa-234">既定値</span><span class="sxs-lookup"><span data-stu-id="858aa-234">Default Value</span></span> | <span data-ttu-id="858aa-235">説明</span><span class="sxs-lookup"><span data-stu-id="858aa-235">Description</span></span> |
| ----------- | ----------------- | ------------- | ----------- |
| `AccessTokenProvider` | `accessTokenFactory` | `null` | <span data-ttu-id="858aa-236">HTTP 要求でベアラー認証トークンとして提供されている文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="858aa-236">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `skipNegotiation` | `false` | <span data-ttu-id="858aa-237">これを設定`true`ネゴシエーションの手順をスキップします。</span><span class="sxs-lookup"><span data-stu-id="858aa-237">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="858aa-238">**Websocket トランスポートがトランスポートには、のみの有効な場合にのみサポート**します。</span><span class="sxs-lookup"><span data-stu-id="858aa-238">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="858aa-239">Azure SignalR サービスを使用する場合は、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="858aa-239">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="858aa-240">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-240">Not configurable \*</span></span> | <span data-ttu-id="858aa-241">Empty</span><span class="sxs-lookup"><span data-stu-id="858aa-241">Empty</span></span> | <span data-ttu-id="858aa-242">要求の認証に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="858aa-242">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="858aa-243">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-243">Not configurable \*</span></span> | <span data-ttu-id="858aa-244">Empty</span><span class="sxs-lookup"><span data-stu-id="858aa-244">Empty</span></span> | <span data-ttu-id="858aa-245">すべての HTTP 要求を送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="858aa-245">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="858aa-246">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-246">Not configurable \*</span></span> | <span data-ttu-id="858aa-247">Empty</span><span class="sxs-lookup"><span data-stu-id="858aa-247">Empty</span></span> | <span data-ttu-id="858aa-248">すべての HTTP 要求を送信する資格情報です。</span><span class="sxs-lookup"><span data-stu-id="858aa-248">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="858aa-249">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-249">Not configurable \*</span></span> | <span data-ttu-id="858aa-250">5 秒</span><span class="sxs-lookup"><span data-stu-id="858aa-250">5 seconds</span></span> | <span data-ttu-id="858aa-251">Websocket だけです。</span><span class="sxs-lookup"><span data-stu-id="858aa-251">WebSockets only.</span></span> <span data-ttu-id="858aa-252">最長時間、クライアントは、サーバーが閉じる要求を確認するための終了後に待機します。</span><span class="sxs-lookup"><span data-stu-id="858aa-252">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="858aa-253">サーバーは、この時間内終了を確認は、クライアントが切断されます。</span><span class="sxs-lookup"><span data-stu-id="858aa-253">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="858aa-254">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-254">Not configurable \*</span></span> | <span data-ttu-id="858aa-255">Empty</span><span class="sxs-lookup"><span data-stu-id="858aa-255">Empty</span></span> | <span data-ttu-id="858aa-256">すべての HTTP 要求を送信する追加の HTTP ヘッダーのディクショナリ。</span><span class="sxs-lookup"><span data-stu-id="858aa-256">A dictionary of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | <span data-ttu-id="858aa-257">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-257">Not configurable \*</span></span> | `null` | <span data-ttu-id="858aa-258">構成または置換するために使用するデリゲート、 `HttpMessageHandler` HTTP 要求を送信するために使用します。</span><span class="sxs-lookup"><span data-stu-id="858aa-258">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="858aa-259">WebSocket 接続に使用されません。</span><span class="sxs-lookup"><span data-stu-id="858aa-259">Not used for WebSocket connections.</span></span> <span data-ttu-id="858aa-260">このデリゲートは、null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="858aa-260">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="858aa-261">その既定値の設定を変更し、それを返すかが返さ新しい`HttpMessageHandler`インスタンス。</span><span class="sxs-lookup"><span data-stu-id="858aa-261">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="858aa-262">**ときは、ハンドラーを置き換える、指定したハンドラーから保持する設定をコピーすることを確認して、それ以外の場合、(Cookie やヘッダー) の構成オプションが新しいハンドラーを適用されません。**</span><span class="sxs-lookup"><span data-stu-id="858aa-262">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | <span data-ttu-id="858aa-263">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-263">Not configurable \*</span></span> | `null` | <span data-ttu-id="858aa-264">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="858aa-264">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | <span data-ttu-id="858aa-265">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-265">Not configurable \*</span></span> | `false` | <span data-ttu-id="858aa-266">HTTP と Websocket の要求の既定の資格情報を送信するこのブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="858aa-266">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="858aa-267">これにより、Windows 認証を使用できます。</span><span class="sxs-lookup"><span data-stu-id="858aa-267">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | <span data-ttu-id="858aa-268">構成できない \*</span><span class="sxs-lookup"><span data-stu-id="858aa-268">Not configurable \*</span></span> | `null` | <span data-ttu-id="858aa-269">追加の WebSocket オプションを構成するために使用するデリゲート。</span><span class="sxs-lookup"><span data-stu-id="858aa-269">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="858aa-270">インスタンスを受け取る[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="858aa-270">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

<span data-ttu-id="858aa-271">アスタリスク (\*) でマークされたオプションはブラウザー Api の制限により、JavaScript クライアントで構成できます。</span><span class="sxs-lookup"><span data-stu-id="858aa-271">Options marked with an asterisk (\*) aren't configurable in the JavaScript client, due to limitations in browser APIs.</span></span>

<span data-ttu-id="858aa-272">指定されたオプションのデリゲートで、.NET クライアントでこれらのオプションを変更できます`WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="858aa-272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="858aa-273">指定された JavaScript オブジェクトで、JavaScript クライアントでこれらのオプションを指定できる`withUrl`:</span><span class="sxs-lookup"><span data-stu-id="858aa-273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

## <a name="additional-resources"></a><span data-ttu-id="858aa-274">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="858aa-274">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
