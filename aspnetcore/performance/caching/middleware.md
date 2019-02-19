---
title: 応答キャッシュ ミドルウェアで ASP.NET Core
author: guardrex
description: ASP.NET Core で応答キャッシュ ミドルウェアを構成し、使用する方法について説明します。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/16/2019
uid: performance/caching/middleware
ms.openlocfilehash: bb265d04022ec2f8fdb3f2f3bc42f6b3f0b2b338
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410324"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="141b6-103">応答キャッシュ ミドルウェアで ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="141b6-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="141b6-104">によって[Luke Latham](https://github.com/guardrex)と[John ルオ語](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="141b6-104">By [Luke Latham](https://github.com/guardrex) and [John Luo](https://github.com/JunTaoLuo)</span></span>

<span data-ttu-id="141b6-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="141b6-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="141b6-106">この記事では、ASP.NET Core アプリの応答キャッシュ ミドルウェアを構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="141b6-106">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="141b6-107">応答がキャッシュ可能な場合、ストアの応答、およびキャッシュから応答を返す役割を果たし、ミドルウェアを決定します。</span><span class="sxs-lookup"><span data-stu-id="141b6-107">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="141b6-108">HTTP キャッシュの概要について、`ResponseCache`属性は、「[応答のキャッシュ](xref:performance/caching/response)します。</span><span class="sxs-lookup"><span data-stu-id="141b6-108">For an introduction to HTTP caching and the `ResponseCache` attribute, see [Response Caching](xref:performance/caching/response).</span></span>

## <a name="package"></a><span data-ttu-id="141b6-109">Package</span><span class="sxs-lookup"><span data-stu-id="141b6-109">Package</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="141b6-110">参照、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)へのパッケージ参照を追加したり、 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="141b6-110">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="141b6-111">参照、 [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)へのパッケージ参照を追加したり、 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="141b6-111">Reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="141b6-112">パッケージ参照を追加、 [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="141b6-112">Add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="141b6-113">構成</span><span class="sxs-lookup"><span data-stu-id="141b6-113">Configuration</span></span>

<span data-ttu-id="141b6-114">`Startup.ConfigureServices`ミドルウェアをサービス コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="141b6-114">In `Startup.ConfigureServices`, add the middleware to the service collection.</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="141b6-115">ミドルウェアを使用するアプリの構成、`UseResponseCaching`拡張メソッドは、要求処理パイプラインにミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="141b6-115">Configure the app to use the middleware with the `UseResponseCaching` extension method, which adds the middleware to the request processing pipeline.</span></span> <span data-ttu-id="141b6-116">サンプル アプリを追加、 [ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2)を最大で 10 秒間キャッシュ可能な応答をキャッシュ応答ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="141b6-116">The sample app adds a [`Cache-Control`](https://tools.ietf.org/html/rfc7234#section-5.2) header to the response that caches cacheable responses for up to 10 seconds.</span></span> <span data-ttu-id="141b6-117">サンプル送信を[ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4)キャッシュされた応答の場合にのみを処理するためにミドルウェアを構成するヘッダー、 [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4)後続の要求のヘッダーの元の要求と一致します。</span><span class="sxs-lookup"><span data-stu-id="141b6-117">The sample sends a [`Vary`](https://tools.ietf.org/html/rfc7231#section-7.1.4) header to configure the middleware to serve a cached response only if the [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span> <span data-ttu-id="141b6-118">、次のコード例で[CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue)と[HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames)を必要とする`using`のステートメント、 [Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers)名前空間。</span><span class="sxs-lookup"><span data-stu-id="141b6-118">In the code example that follows, [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue) and [HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames) require a `using` statement for the [Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers) namespace.</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17,22-29)]

<span data-ttu-id="141b6-119">応答キャッシュ ミドルウェアは、200 (OK) 状態コードと、サーバーの応答のみをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="141b6-119">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="141b6-120">その他の応答を含む[エラー ページ](xref:fundamentals/error-handling)、ミドルウェアによって無視されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-120">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="141b6-121">認証されたクライアントのコンテンツを含む応答は、格納して、それらの応答の処理からミドルウェアを防ぐためにキャッシュ不可としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-121">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="141b6-122">参照してください[キャッシュ用の条件](#conditions-for-caching)については、応答がキャッシュ可能なかどうかに、ミドルウェアが決定します。</span><span class="sxs-lookup"><span data-stu-id="141b6-122">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="141b6-123">オプション</span><span class="sxs-lookup"><span data-stu-id="141b6-123">Options</span></span>

<span data-ttu-id="141b6-124">ミドルウェアは、応答のキャッシュを制御する 3 つのオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="141b6-124">The middleware offers three options for controlling response caching.</span></span>

| <span data-ttu-id="141b6-125">オプション</span><span class="sxs-lookup"><span data-stu-id="141b6-125">Option</span></span>                | <span data-ttu-id="141b6-126">説明</span><span class="sxs-lookup"><span data-stu-id="141b6-126">Description</span></span> |
| --------------------- | ----------- |
| <span data-ttu-id="141b6-127">UseCaseSensitivePaths</span><span class="sxs-lookup"><span data-stu-id="141b6-127">UseCaseSensitivePaths</span></span> | <span data-ttu-id="141b6-128">大文字のパスで応答がキャッシュされるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="141b6-128">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="141b6-129">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="141b6-129">The default value is `false`.</span></span> |
| <span data-ttu-id="141b6-130">MaximumBodySize</span><span class="sxs-lookup"><span data-stu-id="141b6-130">MaximumBodySize</span></span>       | <span data-ttu-id="141b6-131">(バイト単位)、応答本文の最大キャッシュ サイズ。</span><span class="sxs-lookup"><span data-stu-id="141b6-131">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="141b6-132">既定値は`64 * 1024 * 1024`(64 MB)。</span><span class="sxs-lookup"><span data-stu-id="141b6-132">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <span data-ttu-id="141b6-133">SizeLimit</span><span class="sxs-lookup"><span data-stu-id="141b6-133">SizeLimit</span></span>             | <span data-ttu-id="141b6-134">応答キャッシュ ミドルウェア (バイト単位) のサイズ制限。</span><span class="sxs-lookup"><span data-stu-id="141b6-134">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="141b6-135">既定値は`100 * 1024 * 1024`(100 MB)。</span><span class="sxs-lookup"><span data-stu-id="141b6-135">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |

<span data-ttu-id="141b6-136">次の例では、ミドルウェアを構成します。</span><span class="sxs-lookup"><span data-stu-id="141b6-136">The following example configures the middleware to:</span></span>

* <span data-ttu-id="141b6-137">1,024 バイト以下の応答をキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="141b6-137">Cache responses smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="141b6-138">パスの大文字小文字を区別して、応答を格納 (たとえば、`/page1`と`/Page1`別に格納されます)。</span><span class="sxs-lookup"><span data-stu-id="141b6-138">Store the responses by case-sensitive paths (for example, `/page1` and `/Page1` are stored separately).</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="141b6-139">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="141b6-139">VaryByQueryKeys</span></span>

<span data-ttu-id="141b6-140">MVC または Web API コント ローラーまたは Razor ページのページ モデルを使用する場合、`ResponseCache`属性が応答のキャッシュ用の適切なヘッダーを設定するために必要なパラメーターを指定します。</span><span class="sxs-lookup"><span data-stu-id="141b6-140">When using MVC/Web API controllers or Razor Pages page models, the `ResponseCache` attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="141b6-141">唯一のパラメーター、`ResponseCache`ミドルウェアを厳密に必要な属性が`VaryByQueryKeys`、する実際の HTTP ヘッダーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="141b6-141">The only parameter of the `ResponseCache` attribute that strictly requires the middleware is `VaryByQueryKeys`, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="141b6-142">詳細については、次を参照してください。 [ResponseCache 属性](xref:performance/caching/response#responsecache-attribute)します。</span><span class="sxs-lookup"><span data-stu-id="141b6-142">For more information, see [ResponseCache Attribute](xref:performance/caching/response#responsecache-attribute).</span></span>

<span data-ttu-id="141b6-143">使用しない場合、`ResponseCache`属性と変化する応答のキャッシュ、`VaryByQueryKeys`機能します。</span><span class="sxs-lookup"><span data-stu-id="141b6-143">When not using the `ResponseCache` attribute, response caching can be varied with the `VaryByQueryKeys` feature.</span></span> <span data-ttu-id="141b6-144">使用して、`ResponseCachingFeature`から直接、`IFeatureCollection`の`HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="141b6-144">Use the `ResponseCachingFeature` directly from the `IFeatureCollection` of the `HttpContext`:</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="141b6-145">等しい 1 つの値を使用して`*`で`VaryByQueryKeys`キャッシュをすべての要求のクエリ パラメーターによって異なります。</span><span class="sxs-lookup"><span data-stu-id="141b6-145">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="141b6-146">応答キャッシュ ミドルウェアで使用される HTTP ヘッダー</span><span class="sxs-lookup"><span data-stu-id="141b6-146">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="141b6-147">応答キャッシュ ミドルウェアには、HTTP ヘッダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-147">Response caching by the middleware is configured using HTTP headers.</span></span>

| <span data-ttu-id="141b6-148">Header</span><span class="sxs-lookup"><span data-stu-id="141b6-148">Header</span></span> | <span data-ttu-id="141b6-149">説明</span><span class="sxs-lookup"><span data-stu-id="141b6-149">Details</span></span> |
| ------ | ------- |
| <span data-ttu-id="141b6-150">承認</span><span class="sxs-lookup"><span data-stu-id="141b6-150">Authorization</span></span> | <span data-ttu-id="141b6-151">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="141b6-151">The response isn't cached if the header exists.</span></span> |
| <span data-ttu-id="141b6-152">キャッシュ制御</span><span class="sxs-lookup"><span data-stu-id="141b6-152">Cache-Control</span></span> | <span data-ttu-id="141b6-153">マークされた応答をキャッシュにのみ考慮、ミドルウェア、`public`キャッシュ ディレクティブ。</span><span class="sxs-lookup"><span data-stu-id="141b6-153">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="141b6-154">次のパラメーターでキャッシュを制御します。</span><span class="sxs-lookup"><span data-stu-id="141b6-154">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="141b6-155">max-age</span><span class="sxs-lookup"><span data-stu-id="141b6-155">max-age</span></span></li><li><span data-ttu-id="141b6-156">max-stale&#8224;</span><span class="sxs-lookup"><span data-stu-id="141b6-156">max-stale&#8224;</span></span></li><li><span data-ttu-id="141b6-157">min 新規</span><span class="sxs-lookup"><span data-stu-id="141b6-157">min-fresh</span></span></li><li><span data-ttu-id="141b6-158">必要があります revalidate</span><span class="sxs-lookup"><span data-stu-id="141b6-158">must-revalidate</span></span></li><li><span data-ttu-id="141b6-159">キャッシュなし</span><span class="sxs-lookup"><span data-stu-id="141b6-159">no-cache</span></span></li><li><span data-ttu-id="141b6-160">no ストア</span><span class="sxs-lookup"><span data-stu-id="141b6-160">no-store</span></span></li><li><span data-ttu-id="141b6-161">専用の場合-キャッシュ</span><span class="sxs-lookup"><span data-stu-id="141b6-161">only-if-cached</span></span></li><li><span data-ttu-id="141b6-162">private</span><span class="sxs-lookup"><span data-stu-id="141b6-162">private</span></span></li><li><span data-ttu-id="141b6-163">public</span><span class="sxs-lookup"><span data-stu-id="141b6-163">public</span></span></li><li><span data-ttu-id="141b6-164">s maxage</span><span class="sxs-lookup"><span data-stu-id="141b6-164">s-maxage</span></span></li><li><span data-ttu-id="141b6-165">proxy-revalidate&#8225;</span><span class="sxs-lookup"><span data-stu-id="141b6-165">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="141b6-166">&#8224;制限が指定されていない場合`max-stale`ミドルウェアは行われません。</span><span class="sxs-lookup"><span data-stu-id="141b6-166">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="141b6-167">&#8225;`proxy-revalidate`同じ効果`must-revalidate`します。</span><span class="sxs-lookup"><span data-stu-id="141b6-167">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="141b6-168">詳細については、次を参照してください。 [RFC 7231。要求のキャッシュ制御ディレクティブ](https://tools.ietf.org/html/rfc7234#section-5.2.1)します。</span><span class="sxs-lookup"><span data-stu-id="141b6-168">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| <span data-ttu-id="141b6-169">プラグマ</span><span class="sxs-lookup"><span data-stu-id="141b6-169">Pragma</span></span> | <span data-ttu-id="141b6-170">A`Pragma: no-cache`要求のヘッダーと同じ効果を生成する`Cache-Control: no-cache`します。</span><span class="sxs-lookup"><span data-stu-id="141b6-170">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="141b6-171">このヘッダーは、関連するディレクティブによってオーバーライドされる、`Cache-Control`ヘッダー、存在する場合。</span><span class="sxs-lookup"><span data-stu-id="141b6-171">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="141b6-172">Http/1.0 との下位互換性と見なされます。</span><span class="sxs-lookup"><span data-stu-id="141b6-172">Considered for backward compatibility with HTTP/1.0.</span></span> |
| <span data-ttu-id="141b6-173">Set-cookie</span><span class="sxs-lookup"><span data-stu-id="141b6-173">Set-Cookie</span></span> | <span data-ttu-id="141b6-174">ヘッダーが存在する場合、応答はキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="141b6-174">The response isn't cached if the header exists.</span></span> <span data-ttu-id="141b6-175">1 つまたは複数の cookie を設定する要求処理パイプラインですべてのミドルウェアが応答をキャッシュから応答キャッシュ ミドルウェアを防ぎます (たとえば、 [cookie ベース TempData プロバイダー](xref:fundamentals/app-state#tempdata))。</span><span class="sxs-lookup"><span data-stu-id="141b6-175">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| <span data-ttu-id="141b6-176">異なる</span><span class="sxs-lookup"><span data-stu-id="141b6-176">Vary</span></span> | <span data-ttu-id="141b6-177">`Vary`別ヘッダーによってヘッダーが、キャッシュされた応答の変更に使用されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-177">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="141b6-178">などを含めることによってエンコードすることによって応答をキャッシュ、`Vary: Accept-Encoding`ヘッダーの要求の応答をキャッシュするヘッダー`Accept-Encoding: gzip`と`Accept-Encoding: text/plain`とは別にします。</span><span class="sxs-lookup"><span data-stu-id="141b6-178">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="141b6-179">応答のヘッダー値が`*`は保存されません。</span><span class="sxs-lookup"><span data-stu-id="141b6-179">A response with a header value of `*` is never stored.</span></span> |
| <span data-ttu-id="141b6-180">有効期限が切れます</span><span class="sxs-lookup"><span data-stu-id="141b6-180">Expires</span></span> | <span data-ttu-id="141b6-181">このヘッダーが古いと見なされる応答はありません格納、またはその他によってオーバーライドされない限りを取得`Cache-Control`ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="141b6-181">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| <span data-ttu-id="141b6-182">None-If-match</span><span class="sxs-lookup"><span data-stu-id="141b6-182">If-None-Match</span></span> | <span data-ttu-id="141b6-183">値がない場合、完全な応答がキャッシュから提供された`*`、`ETag`の応答と一致しない指定された値のいずれか。</span><span class="sxs-lookup"><span data-stu-id="141b6-183">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="141b6-184">それ以外の場合、304 (変更なし) の応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-184">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| <span data-ttu-id="141b6-185">場合の変更-以降</span><span class="sxs-lookup"><span data-stu-id="141b6-185">If-Modified-Since</span></span> | <span data-ttu-id="141b6-186">場合、`If-None-Match`ヘッダーが含まれていない、キャッシュされた応答の日付が指定された値よりも新しい場合は、完全な応答がキャッシュから提供されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-186">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="141b6-187">それ以外の場合、304 (変更なし) の応答が提供されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-187">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| <span data-ttu-id="141b6-188">日付</span><span class="sxs-lookup"><span data-stu-id="141b6-188">Date</span></span> | <span data-ttu-id="141b6-189">キャッシュから提供するときに、`Date`元からの応答で指定されなかった場合、ミドルウェアによってヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-189">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| <span data-ttu-id="141b6-190">コンテンツの長さ</span><span class="sxs-lookup"><span data-stu-id="141b6-190">Content-Length</span></span> | <span data-ttu-id="141b6-191">キャッシュから提供するときに、`Content-Length`元からの応答で指定されなかった場合、ミドルウェアによってヘッダーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-191">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| <span data-ttu-id="141b6-192">経過時間</span><span class="sxs-lookup"><span data-stu-id="141b6-192">Age</span></span> | <span data-ttu-id="141b6-193">`Age`元の応答で送信されたヘッダーは無視されます。</span><span class="sxs-lookup"><span data-stu-id="141b6-193">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="141b6-194">ミドルウェアは、キャッシュされた応答を提供するときに、新しい値を計算します。</span><span class="sxs-lookup"><span data-stu-id="141b6-194">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="141b6-195">要求キャッシュ コントロール ディレクティブを尊重キャッシュ</span><span class="sxs-lookup"><span data-stu-id="141b6-195">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="141b6-196">ミドルウェアの規則を尊重する、[仕様の HTTP 1.1 キャッシュ](https://tools.ietf.org/html/rfc7234#section-5.2)します。</span><span class="sxs-lookup"><span data-stu-id="141b6-196">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="141b6-197">規則を優先する有効なキャッシュを必要と`Cache-Control`クライアントによって送信されたヘッダー。</span><span class="sxs-lookup"><span data-stu-id="141b6-197">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="141b6-198">仕様では、クライアントがで要求を実行できる、`no-cache`ヘッダーの値と force、サーバー要求のたびに新しい応答を生成します。</span><span class="sxs-lookup"><span data-stu-id="141b6-198">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="141b6-199">現時点ではありませんこのキャッシュの動作を開発者の制御、ミドルウェアは、公式のキャッシュの仕様に準拠するために、ミドルウェアを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="141b6-199">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="141b6-200">キャッシュの動作をより細かく制御は、ASP.NET Core の他のキャッシュ機能を探索します。</span><span class="sxs-lookup"><span data-stu-id="141b6-200">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="141b6-201">次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="141b6-201">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="141b6-202">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="141b6-202">Troubleshooting</span></span>

<span data-ttu-id="141b6-203">キャッシュの動作が期待どおりにしていない場合は、応答がキャッシュとキャッシュから提供される機能のことを確認します。</span><span class="sxs-lookup"><span data-stu-id="141b6-203">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="141b6-204">要求の受信ヘッダーおよび応答の送信ヘッダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="141b6-204">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="141b6-205">有効にする[ログ](xref:fundamentals/logging/index)デバッグを支援します。</span><span class="sxs-lookup"><span data-stu-id="141b6-205">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="141b6-206">テストとキャッシュ動作のトラブルシューティング、ブラウザーが好ましくない方法でキャッシュに影響する要求ヘッダーを設定できます。</span><span class="sxs-lookup"><span data-stu-id="141b6-206">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="141b6-207">たとえば、ブラウザー設定、`Cache-Control`ヘッダーを`no-cache`または`max-age=0`ページを更新するときにします。</span><span class="sxs-lookup"><span data-stu-id="141b6-207">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="141b6-208">次のツールでは、要求ヘッダーを明示的に設定することができ、キャッシュのテストに適しています。</span><span class="sxs-lookup"><span data-stu-id="141b6-208">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="141b6-209">Fiddler</span><span class="sxs-lookup"><span data-stu-id="141b6-209">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="141b6-210">Postman</span><span class="sxs-lookup"><span data-stu-id="141b6-210">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="141b6-211">キャッシュの条件</span><span class="sxs-lookup"><span data-stu-id="141b6-211">Conditions for caching</span></span>

* <span data-ttu-id="141b6-212">要求は、200 (OK) 状態コードでサーバーの応答になる必要があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-212">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="141b6-213">要求メソッドは、GET または HEAD である必要があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-213">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="141b6-214">ターミナルのミドルウェアは、応答キャッシュ ミドルウェアの前に応答を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-214">Terminal middleware must not process the response prior to the Response Caching Middleware.</span></span>
* <span data-ttu-id="141b6-215">`Authorization`ヘッダーが存在しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-215">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="141b6-216">`Cache-Control` ヘッダー パラメーターが有効である必要があり、応答をマークする必要があります`public`としてマークされていないと`private`します。</span><span class="sxs-lookup"><span data-stu-id="141b6-216">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="141b6-217">`Pragma: no-cache`ヘッダーが存在しない場合がある場合、`Cache-Control`ヘッダーが存在しない、として、`Cache-Control`ヘッダーの上書き、`Pragma`ヘッダーが存在する場合。</span><span class="sxs-lookup"><span data-stu-id="141b6-217">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="141b6-218">`Set-Cookie`ヘッダーが存在しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-218">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="141b6-219">`Vary` ヘッダーのパラメーターが有効であり、等しくする必要があります`*`します。</span><span class="sxs-lookup"><span data-stu-id="141b6-219">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="141b6-220">`Content-Length`ヘッダーの値 (場合に設定)、応答本文のサイズに一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-220">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="141b6-221">[IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature)は使用されません。</span><span class="sxs-lookup"><span data-stu-id="141b6-221">The [IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) isn't used.</span></span>
* <span data-ttu-id="141b6-222">応答で指定された古いすることはできません、`Expires`ヘッダーと`max-age`と`s-maxage`ディレクティブをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="141b6-222">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="141b6-223">応答バッファリングは、成功するにある必要があるあり、応答のサイズが、構成されているよりも小さくする必要がありますまたは既定の`SizeLimit`します。</span><span class="sxs-lookup"><span data-stu-id="141b6-223">Response buffering must be successful, and the size of the response must be smaller than the configured or default `SizeLimit`.</span></span>
* <span data-ttu-id="141b6-224">応答はに従ってキャッシュ可能である必要があります、 [RFC 7234](https://tools.ietf.org/html/rfc7234)仕様。</span><span class="sxs-lookup"><span data-stu-id="141b6-224">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="141b6-225">たとえば、`no-store`ディレクティブが要求または応答のヘッダー フィールドに存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="141b6-225">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="141b6-226">参照してください*セクション 3。応答をキャッシュに格納する*の[RFC 7234](https://tools.ietf.org/html/rfc7234)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="141b6-226">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="141b6-227">偽造防止システムをクロスサイト リクエスト フォージェリ (CSRF) を防ぐためにセキュリティで保護されたトークンを生成するセットの攻撃、`Cache-Control`と`Pragma`ヘッダーを`no-cache`応答がキャッシュされないようにします。</span><span class="sxs-lookup"><span data-stu-id="141b6-227">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="141b6-228">HTML フォーム要素を偽造防止トークンを無効にする方法については、次を参照してください。 [ASP.NET Core の偽造防止構成](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)します。</span><span class="sxs-lookup"><span data-stu-id="141b6-228">For information on how to disable antiforgery tokens for HTML form elements, see [ASP.NET Core antiforgery configuration](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="141b6-229">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="141b6-229">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
