---
title: ASP.NET Core のエラーを処理する
author: tdykstra
description: ASP.NET Core アプリでエラーを処理する方法について説明します。
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/error-handling
ms.openlocfilehash: f4358cba81d2aa47a26f90a8d5f4e77310bcad00
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637639"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="7a4cb-103">ASP.NET Core のエラーを処理する</span><span class="sxs-lookup"><span data-stu-id="7a4cb-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="7a4cb-104">執筆: [Steve Smith](https://ardalis.com/)、[Tom Dykstra](https://github.com/tdykstra/)</span><span class="sxs-lookup"><span data-stu-id="7a4cb-104">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra/)</span></span>

<span data-ttu-id="7a4cb-105">この記事では、ASP.NET Core アプリでエラーを処理するための一般的な手法について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="7a4cb-106">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x/ErrorHandlingSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="the-developer-exception-page"></a><span data-ttu-id="7a4cb-107">開発者例外ページ</span><span class="sxs-lookup"><span data-stu-id="7a4cb-107">The Developer Exception Page</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7a4cb-108">例外に関する詳細情報を表示するページを表示するアプリを構成するには、*開発者例外ページ*を使用します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-108">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="7a4cb-109">このページは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app) 内で利用できる、[Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="7a4cb-110">`Startup.Configure` メソッドに次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-110">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7a4cb-111">例外に関する詳細情報を表示するページを表示するアプリを構成するには、*開発者例外ページ*を使用します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-111">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="7a4cb-112">このページは [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) で使用できる、[Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-112">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="7a4cb-113">`Startup.Configure` メソッドに次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-113">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7a4cb-114">例外に関する詳細情報を表示するページを表示するアプリを構成するには、*開発者例外ページ*を使用します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-114">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="7a4cb-115">このページは、プロジェクト ファイルに [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージのパッケージ参照を追加することで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-115">The page is made available by adding a package reference for the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package in the project file.</span></span> <span data-ttu-id="7a4cb-116">`Startup.Configure` メソッドに次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-116">Add a line to the `Startup.Configure` method:</span></span>

::: moniker-end

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=7)]

<span data-ttu-id="7a4cb-117">`app.UseMvc` などの例外をキャッチする任意のミドルウェアの前に [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) の呼び出しを配置します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-117">Place the call to [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) in front of any middleware where you want to catch exceptions, such as `app.UseMvc`.</span></span>

> [!WARNING]
> <span data-ttu-id="7a4cb-118">**アプリを開発環境で実行するときにのみ**、開発者例外ページを有効にします。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-118">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="7a4cb-119">アプリを実稼働環境で実行するときは、詳細な例外情報を公開しません。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-119">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="7a4cb-120">[環境の構成についてはこちらをご覧ください](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-120">[Learn more about configuring environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7a4cb-121">開発者例外ページを表示するには、環境を `Development` に設定してサンプル アプリを実行し、アプリのベース URL に `?throw=true` を追加します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-121">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="7a4cb-122">このページには、例外と要求に関する情報を含むタブがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-122">The page includes several tabs with information about the exception and the request.</span></span> <span data-ttu-id="7a4cb-123">最初のタブにはスタック トレースがあります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-123">The first tab includes a stack trace:</span></span>

![スタック トレース](error-handling/_static/developer-exception-page.png)

<span data-ttu-id="7a4cb-125">次のタブには、クエリ文字列パラメーターがあればそれが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-125">The next tab shows the query string parameters, if any:</span></span>

![クエリ文字列のパラメーター](error-handling/_static/developer-exception-page-query.png)

<span data-ttu-id="7a4cb-127">要求に Cookie がある場合は、**[Cookie]** タブ上に表示されます。ヘッダーは、最後のタブに表示されます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-127">If the request has cookies, they appear on the **Cookies** tab. Headers are seen in the last tab:</span></span>

![ヘッダー](error-handling/_static/developer-exception-page-headers.png)

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="7a4cb-129">カスタム例外処理ページを構成する</span><span class="sxs-lookup"><span data-stu-id="7a4cb-129">Configure a custom exception handling page</span></span>

<span data-ttu-id="7a4cb-130">アプリが `Development` 環境で実行されていないときに使用する例外ハンドラー ページを構成します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-130">Configure an exception handler page to use when the app isn't running in the `Development` environment:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=11)]

<span data-ttu-id="7a4cb-131">Razor Pages アプリでは、[dotnet new](/dotnet/core/tools/dotnet-new) Razor Pages テンプレートがエラー ページとエラー `PageModel` クラスを *Pages* フォルダーで提供します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-131">In a Razor Pages app, the [dotnet new](/dotnet/core/tools/dotnet-new) Razor Pages template provides an Error page and an error `PageModel` class in the *Pages* folder.</span></span>

<span data-ttu-id="7a4cb-132">MVC アプリでは、`HttpGet` など、HTTP メソッド属性でエラー ハンドラー アクション メソッドを修飾しないでください。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-132">In an MVC app, don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="7a4cb-133">明示的な動詞を使用すると、要求がメソッドに届かないことがあります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="7a4cb-134">認証されていないユーザーがエラー ビューを受信できるように、メソッドへの匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

<span data-ttu-id="7a4cb-135">たとえば、次のエラー処理メソッドは [dotnet new](/dotnet/core/tools/dotnet-new) MVC テンプレートによって提供され、ホーム コントローラーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-135">For example, the following error handler method is provided by the [dotnet new](/dotnet/core/tools/dotnet-new) MVC template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

## <a name="configure-status-code-pages"></a><span data-ttu-id="7a4cb-136">状態コード ページを構成する</span><span class="sxs-lookup"><span data-stu-id="7a4cb-136">Configure status code pages</span></span>

<span data-ttu-id="7a4cb-137">アプリは既定で、*404 見つかりません*などの HTTP 状態コードのリッチ状態コード ページを表示しません。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-137">By default, an app doesn't provide a rich status code page for HTTP status codes, such as *404 Not Found*.</span></span> <span data-ttu-id="7a4cb-138">状態コード ページを提供するには、状態コード ページ ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-138">To provide status code pages, use Status Code Pages Middleware.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7a4cb-139">このミドルウェアは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内で使用できる、[Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-139">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="7a4cb-140">このミドルウェアは、[Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)内で使用できる、[Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージによって使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-140">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="7a4cb-141">このミドルウェアは、プロジェクト ファイルに [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) パッケージのパッケージ参照を追加することで使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-141">The middleware is made available by adding a package reference for the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package in the project file.</span></span>

::: moniker-end

<span data-ttu-id="7a4cb-142">`Startup.Configure` メソッドに次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-142">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="7a4cb-143">パイプラインでは要求処理ミドルウェア (静的ファイル ミドルウェアや MVC ミドルウェアなど) の前に <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-143"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> should be called before request handling middlewares in the pipeline (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="7a4cb-144">既定では、状態コード ページ ミドルウェアは 404 などの一般的な状態コードにテキストのみのハンドラーを追加します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-144">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as 404:</span></span>

![404 ページ](error-handling/_static/default-404-status-code.png)

<span data-ttu-id="7a4cb-146">このミドルウェアは、いくつかの拡張メソッドに対応しています。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-146">The middleware supports several extension methods.</span></span> <span data-ttu-id="7a4cb-147">1 つのメソッドがラムダ式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-147">One method takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="7a4cb-148">`UseStatusCodePages` のオーバーロードがコンテンツの種類と書式文字列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-148">An overload of `UseStatusCodePages` takes a content type and format string:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```
### <a name="redirect-re-execute-extension-methods"></a><span data-ttu-id="7a4cb-149">リダイレクト再実行の拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="7a4cb-149">Redirect re-execute extension methods</span></span>

<span data-ttu-id="7a4cb-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span><span class="sxs-lookup"><span data-stu-id="7a4cb-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="7a4cb-151">クライアントに *302 - Found* 状態コードを送信します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-151">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="7a4cb-152">URL テンプレートで指定された場所にクライアントをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-152">Redirects the client to the location provided in the URL template.</span></span> 

<span data-ttu-id="7a4cb-153">テンプレートには、状態コードの `{0}` プレースホルダーが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-153">The template may include a `{0}` placeholder for the status code.</span></span> <span data-ttu-id="7a4cb-154">テンプレートには、最初にスラッシュ (`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-154">The template must start with a forward slash (`/`).</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="7a4cb-155"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span><span class="sxs-lookup"><span data-stu-id="7a4cb-155"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="7a4cb-156">元の状態コードをクライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-156">Returns the original status code to the client.</span></span>
* <span data-ttu-id="7a4cb-157">代替パスを使用して要求パイプラインを再実行することで、応答本文を生成する必要があることを指定します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-157">Specifies that the response body should be generated by re-executing the request pipeline using an alternate path.</span></span> 

<span data-ttu-id="7a4cb-158">テンプレートには、状態コードの `{0}` プレースホルダーが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-158">The template may include a `{0}` placeholder for the status code.</span></span> <span data-ttu-id="7a4cb-159">テンプレートには、最初にスラッシュ (`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-159">The template must start with a forward slash (`/`).</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/error/{0}");
```

<span data-ttu-id="7a4cb-160">状態コード ページは、Razor ページ ハンドラー メソッドまたは MVC コントローラー内の特定の要求に対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-160">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="7a4cb-161">状態コード ページを無効にするには、要求の [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) コレクションから [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) を取得し、機能が有効な場合は無効にします。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-161">To disable status code pages, attempt to retrieve the [IStatusCodePagesFeature](/dotnet/api/microsoft.aspnetcore.diagnostics.istatuscodepagesfeature) from the request's [HttpContext.Features](/dotnet/api/microsoft.aspnetcore.http.httpcontext.features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="7a4cb-162">アプリ内のエンドポイントを指す `UseStatusCodePages*` オーバーロードを使用するには、そのエンドポイントの MVC ビューまたは Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-162">To use a `UseStatusCodePages*` overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="7a4cb-163">たとえば、Razor Pages アプリの [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートでは、次のページとページ モデル クラスを生成します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-163">For example, the [dotnet new](/dotnet/core/tools/dotnet-new) template for a Razor Pages app produces the following page and page model class:</span></span>

<span data-ttu-id="7a4cb-164">*Error.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7a4cb-164">*Error.cshtml*:</span></span>

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

<span data-ttu-id="7a4cb-165">*Error.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a4cb-165">*Error.cshtml.cs*:</span></span>

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="7a4cb-166">例外処理コード</span><span class="sxs-lookup"><span data-stu-id="7a4cb-166">Exception-handling code</span></span>

<span data-ttu-id="7a4cb-167">例外処理ページのコードは例外をスローすることがあります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-167">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="7a4cb-168">実稼働のエラー ページは純粋に静的なコンテンツで構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-168">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="7a4cb-169">また、応答のヘッダーの送信後、応答のステータス コードを変更できなくなり、例外ページやハンドラーを実行できなくなることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-169">Also, be aware that once the headers for a response have been sent, you can't change the response's status code, nor can any exception pages or handlers run.</span></span> <span data-ttu-id="7a4cb-170">応答は完了している必要があります。あるいは、接続が中止となっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-170">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="7a4cb-171">サーバー例外処理</span><span class="sxs-lookup"><span data-stu-id="7a4cb-171">Server exception handling</span></span>

<span data-ttu-id="7a4cb-172">アプリの例外処理ロジックに加え、アプリをホストしている[サーバー](xref:fundamentals/servers/index)がいくつかの例外処理を実行します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-172">In addition to the exception handling logic in your app, the [server](xref:fundamentals/servers/index) hosting your app performs some exception handling.</span></span> <span data-ttu-id="7a4cb-173">サーバーがヘッダーの送信前に例外をキャッチすると、サーバーは *500 内部サーバー エラー*を本文なしで送信します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-173">If the server catches an exception before the headers are sent, the server sends a *500 Internal Server Error* response with no body.</span></span> <span data-ttu-id="7a4cb-174">サーバーがヘッダーの送信後に例外をキャッチすると、サーバーは接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-174">If the server catches an exception after the headers have been sent, the server closes the connection.</span></span> <span data-ttu-id="7a4cb-175">アプリで処理されない要求はサーバーで処理されます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-175">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="7a4cb-176">例外が発生すると、サーバーの例外処理で処理されます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-176">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="7a4cb-177">カスタムのエラー ページ、例外処理ミドルウェア、フィルターを構成しても、この動作は変わりません。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-177">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="7a4cb-178">起動時の例外処理</span><span class="sxs-lookup"><span data-stu-id="7a4cb-178">Startup exception handling</span></span>

<span data-ttu-id="7a4cb-179">アプリの起動中に起こる例外はホスティング層だけが処理できます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-179">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="7a4cb-180">[Web ホスト](xref:fundamentals/host/web-host)を使うと、`captureStartupErrors` と `detailedErrors` キーを利用して、[起動中のエラーに対するホストの動作を構成](xref:fundamentals/host/web-host#detailed-errors)できます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-180">Using the [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="7a4cb-181">ホスティングでは、ホスト アドレス/ポート バインディング後にエラーが発生した場合のみ、キャプチャされた起動時エラーに対してエラー ページを表示できます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-181">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="7a4cb-182">何らかの理由でバインディングに失敗した場合、ホスティング層は重要な例外をログに記録し、dotnet プロセスがクラッシュします。[Kestrel](xref:fundamentals/servers/kestrel) サーバー上でアプリが実行されている場合、エラー ページは表示されません。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-182">If any binding fails for any reason, the hosting layer logs a critical exception, the dotnet process crashes, and no error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="7a4cb-183">[IIS](/iis) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上で実行されている場合、プロセスを開始できなければ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)から *502.5 プロセス エラー*が返されます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-183">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't be started.</span></span> <span data-ttu-id="7a4cb-184">IIS でホストするときの起動の問題のトラブルシューティングについては、<xref:host-and-deploy/iis/troubleshoot> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-184">For information on troubleshooting startup issues when hosting with IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="7a4cb-185">Azure App Service での起動の問題のトラブルシューティングについては、<xref:host-and-deploy/azure-apps/troubleshoot> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-185">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="7a4cb-186">ASP.NET Core MVC エラー処理</span><span class="sxs-lookup"><span data-stu-id="7a4cb-186">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="7a4cb-187">[MVC](xref:mvc/overview) アプリには、エラー処理の追加オプションがいくつかあります。例外フィルターの構成やモデル検証の実行などです。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-187">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="7a4cb-188">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="7a4cb-188">Exception filters</span></span>

<span data-ttu-id="7a4cb-189">例外フィルターはグローバルに構成するか、MVC アプリのコントローラーまたはアクション単位で構成できます。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-189">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="7a4cb-190">このようなフィルターはコントローラー アクションや別のフィルターの実行中に発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-190">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="7a4cb-191">これらのフィルターは、それ以外の場合には呼び出されません。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-191">These filters aren't called otherwise.</span></span> <span data-ttu-id="7a4cb-192">詳細については、[フィルターに関するページ](xref:mvc/controllers/filters)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-192">To learn more, see [Filters](xref:mvc/controllers/filters).</span></span>

> [!TIP]
> <span data-ttu-id="7a4cb-193">例外フィルターは、MVC アクション内で発生する例外をトラップするのには適していますが、エラー処理ミドルウェアほどの柔軟性はありません。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-193">Exception filters are good for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="7a4cb-194">一般的にはミドルウェアの使用を選択し、MVC アクションで選択されたのとは*異なる*方法でエラー処理を行う必要がある場合にのみフィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-194">Generally prefer the use of middleware, and use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handling-model-state-errors"></a><span data-ttu-id="7a4cb-195">モデルの状態エラーの処理</span><span class="sxs-lookup"><span data-stu-id="7a4cb-195">Handling model state errors</span></span>

<span data-ttu-id="7a4cb-196">[モデル検証](xref:mvc/models/validation)は各コントローラー アクションを呼び出す前に行われます。`ModelState.IsValid` を検査し、適切に対処するのはアクション メソッドの仕事です。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-196">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span>

<span data-ttu-id="7a4cb-197">一部のアプリでは、モデル検証エラーを処理するとき、標準的な規則に従います。その場合、そのようなポリシーを実装する場所として[フィルター](xref:mvc/controllers/filters)が適していることがあります。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-197">Some apps choose to follow a standard convention for dealing with model validation errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="7a4cb-198">無効なモデル状態でアクションの動作をテストしてください。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-198">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="7a4cb-199">詳細については、[コントローラー ロジックのテスト](xref:mvc/controllers/testing)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7a4cb-199">Learn more in [Test controller logic](xref:mvc/controllers/testing).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a4cb-200">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7a4cb-200">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
