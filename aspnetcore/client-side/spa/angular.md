---
title: ASP.NET Core で Angular プロジェクト テンプレートを使用する
author: SteveSandersonMS
description: Angular と Angular CLI 用の ASP.NET Core シングル ページ アプリケーション (SPA) プロジェクト テンプレートの使用を開始する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/angular
ms.openlocfilehash: 35a839e31369e8dbf00f5dbfb3751a2985335755
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248122"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="b3c83-103">ASP.NET Core で Angular プロジェクト テンプレートを使用する</span><span class="sxs-lookup"><span data-stu-id="b3c83-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="b3c83-104">更新された Angular プロジェクト テンプレートは、Angular と Angular CLI を使用してリッチなクライアント側ユーザー インターフェイス (UI) を実装する ASP.NET Core アプリのための便利な開始点を提供します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="b3c83-105">このテンプレートは、API バックエンドとして機能する ASP.NET Core プロジェクトと、UI として機能する Angular CLI プロジェクトの作成に相当します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="b3c83-106">このテンプレートは、1 つのアプリ プロジェクトの中で、両方のプロジェクトの種類をホストするという利便性を提供します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="b3c83-107">その結果、アプリ プロジェクトを 1 つのユニットとしてビルドして発行できます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="b3c83-108">新しいアプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b3c83-108">Create a new app</span></span>

<span data-ttu-id="b3c83-109">ASP.NET Core 2.1 がインストールされている場合は、Angular プロジェクト テンプレートをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b3c83-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="b3c83-110">コマンド プロンプトで `dotnet new angular` コマンドを使用して、空のディレクトリの中に新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="b3c83-111">たとえば、次のコマンドは、*my-new-app* ディレクトリにアプリを作成し、そのディレクトリに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```console
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="b3c83-112">Visual Studio または .NET Core CLI からアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b3c83-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b3c83-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="b3c83-114">生成された *.csproj* ファイルを開き、そこから通常の方法でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="b3c83-115">ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="b3c83-116">以降のビルドは非常に高速になります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b3c83-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="b3c83-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b3c83-118">値が `Development` である `ASPNETCORE_Environment` という名前の環境変数があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="b3c83-119">Windows では、PowerShell 以外のプロンプトで `SET ASPNETCORE_Environment=Development` を実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="b3c83-120">Linux または macOS では、`export ASPNETCORE_Environment=Development` を実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="b3c83-121">[dotnet build](/dotnet/core/tools/dotnet-build) を実行して、アプリが正しくビルドされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="b3c83-122">ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="b3c83-123">以降のビルドは非常に高速になります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="b3c83-124">[dotnet run](/dotnet/core/tools/dotnet-run) を実行してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="b3c83-125">次のようなメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="b3c83-126">ブラウザーでこの URL に移動します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-126">Navigate to this URL in a browser.</span></span>

<span data-ttu-id="b3c83-127">アプリは、Angular CLI サーバーのインスタンスをバックグラウンドで開始します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="b3c83-128">次のようなメッセージがログに記録されます。*NG ライブ開発サーバーが localhost でリッスンしている:&lt;otherport&gt;でブラウザーを開いて http://localhost:&lt; otherport&gt;/* します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open your browser on http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="b3c83-129">このメッセージは無視してください。それは、結合された ASP.NET Core と Angular CLI アプリの URLでは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="b3c83-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="b3c83-130">プロジェクト テンプレートは、ASP.NET Core アプリと Angular アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="b3c83-131">ASP.NET Core アプリは、データ アクセス、承認、およびその他のサーバー側の操作で使用することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="b3c83-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="b3c83-132">*ClientApp* サブディレクトリ内の Angular アプリは、UI に関するすべての 操作で使用することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="b3c83-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="b3c83-133">ページ、画像、スタイル、モジュールなどを追加する</span><span class="sxs-lookup"><span data-stu-id="b3c83-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="b3c83-134">*ClientApp* ディレクトリには、標準的な Angular CLI アプリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="b3c83-135">詳細については、公式の [Angular ドキュメント](https://github.com/angular/angular-cli/wiki)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b3c83-135">See the official [Angular documentation](https://github.com/angular/angular-cli/wiki) for more information.</span></span>

<span data-ttu-id="b3c83-136">このテンプレートによって作成される Angular アプリと Angular CLI 自体によって (`ng new` で) 作成されるアプリにはわずかな違いがありますが、アプリの機能は変わりません。</span><span class="sxs-lookup"><span data-stu-id="b3c83-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="b3c83-137">テンプレートによって作成されるアプリには、[ブートストラップ](https://getbootstrap.com/)ベースのレイアウトと基本的なルーティングの例が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="b3c83-138">ng コマンドを実行する</span><span class="sxs-lookup"><span data-stu-id="b3c83-138">Run ng commands</span></span>

<span data-ttu-id="b3c83-139">コマンド プロンプトで、*ClientApp* サブディレクトリに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="b3c83-140">`ng` ツールがグローバルにインストールされている場合は、そのコマンドのすべてを実行できます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="b3c83-141">たとえば、`ng lint`、`ng test`、またはその他の [Angular CLI コマンド](https://github.com/angular/angular-cli/wiki#additional-commands) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://github.com/angular/angular-cli/wiki#additional-commands).</span></span> <span data-ttu-id="b3c83-142">ただし、`ng serve` を実行する必要はありません。これは、アプリのサーバー側とクライアント側の両方に関わる部分は、ASP.NET Core アプリが処理するためです。</span><span class="sxs-lookup"><span data-stu-id="b3c83-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="b3c83-143">内部的には、それは、開発時に `ng serve` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="b3c83-144">`ng` ツールがインストールされていない場合は、代わりに `npm run ng` を実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="b3c83-145">たとえば、`npm run ng lint` または `npm run ng test` を実行できます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="b3c83-146">npm パッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="b3c83-146">Install npm packages</span></span>

<span data-ttu-id="b3c83-147">サードパーティ製の npm パッケージをインストールするには、*ClientApp* サブディレクトリでコマンド プロンプトを使用します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="b3c83-148">例:</span><span class="sxs-lookup"><span data-stu-id="b3c83-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="b3c83-149">発行と配置</span><span class="sxs-lookup"><span data-stu-id="b3c83-149">Publish and deploy</span></span>

<span data-ttu-id="b3c83-150">開発中、アプリは、開発者に便利なように最適化されたモードで実行されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="b3c83-151">たとえば、JavaScript バンドルには、ソース マップが含まれます (デバッグ時に元の TypeScript コードを確認できるようにするためです)。</span><span class="sxs-lookup"><span data-stu-id="b3c83-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="b3c83-152">アプリは、ディスク上の TypeScript、HTML および CSS ファイルの変更を監視し、これらのファイルの変更が発生した場合は、再コンパイルと再読み込みを自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="b3c83-153">運用時は、パフォーマンスが最適化されたバージョンのアプリが提供されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="b3c83-154">これは、自動的に実行されるように構成されています。</span><span class="sxs-lookup"><span data-stu-id="b3c83-154">This is configured to happen automatically.</span></span> <span data-ttu-id="b3c83-155">発行すると、ビルド構成によって、クライアント側コードの縮小された Ahead Of Time (AoT) コンパイルが行われたビルドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="b3c83-156">開発ビルドとは異なり、運用ビルドは、サーバーへの Node.js のインストールを必要としません ([サーバー側の事前レンダリング](#server-side-rendering)を有効にしている場合は除きます)。</span><span class="sxs-lookup"><span data-stu-id="b3c83-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled [server-side prerendering](#server-side-rendering)).</span></span>

<span data-ttu-id="b3c83-157">標準的な [ASP.NET Core のホストと展開方法](xref:host-and-deploy/index)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="b3c83-158">"ng serve" を個別に実行する</span><span class="sxs-lookup"><span data-stu-id="b3c83-158">Run "ng serve" independently</span></span>

<span data-ttu-id="b3c83-159">ASP.NET Core アプリが開発モードで起動された場合、プロジェクトは、Angular CLI サーバーの独自のインスタンスをバックグラウンドで開始するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="b3c83-160">これが便利なのは、別のサーバーを手動で実行する必要がないためです。</span><span class="sxs-lookup"><span data-stu-id="b3c83-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="b3c83-161">この既定の設定には欠点があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="b3c83-162">C# コードを変更し、ASP.NET Core アプリを再起動する必要がある場合、Angular CLI サーバーが毎回再起動します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="b3c83-163">再起動には、約 10 秒必要です。</span><span class="sxs-lookup"><span data-stu-id="b3c83-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="b3c83-164">C# コードを何度も編集するが、Angular CLI が再起動するまで待ちたくない場合は、ASP.NET Core プロセスから独立した Angular CLI サーバーを外部で実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="b3c83-165">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="b3c83-165">To do so:</span></span>

1. <span data-ttu-id="b3c83-166">コマンド プロンプトで、*ClientApp* サブディレクトリに切り替え、Angular CLI 開発サーバーを起動します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="b3c83-167">Angular CLI 開発サーバーを起動するには、`ng serve` ではなく `npm start` を使用して、*package.json* の構成が使用されるようにします。</span><span class="sxs-lookup"><span data-stu-id="b3c83-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="b3c83-168">Angular CLI サーバーに追加パラメーターを渡すには、*package.json* ファイルの関連する `scripts` 行にそれらを追加します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="b3c83-169">独自のインスタンスを起動する代わりに外部の Angular CLI インスタンスを使用するように ASP.NET Core アプリケーションを変更します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="b3c83-170">*Startup* クラスで、`spa.UseAngularCliServer` の呼び出しを以下に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="b3c83-171">ASP.NET Core アプリの起動時に Angular CLI サーバーが起動されなくなります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="b3c83-172">代わりに、手動で開始したインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="b3c83-173">これにより、起動と再起動を高速化できます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="b3c83-174">Angular CLI がクライアント アプリを毎回リビルドするまで待つ必要はなくなります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

## <a name="server-side-rendering"></a><span data-ttu-id="b3c83-175">サーバー側のレンダリング</span><span class="sxs-lookup"><span data-stu-id="b3c83-175">Server-side rendering</span></span>

<span data-ttu-id="b3c83-176">パフォーマンス機能として、クライアントでの実行に加え、サーバーでの Angular アプリの事前レンダリングを選択できます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-176">As a performance feature, you can choose to pre-render your Angular app on the server as well as running it on the client.</span></span> <span data-ttu-id="b3c83-177">これは、ブラウザーがアプリの初期 UI を表す HTML マークアップを受信して、JavaScript バンドルをダウンロードして実行する前に UI を表示できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-177">This means that browsers receive HTML markup representing your app's initial UI, so they display it even before downloading and executing your JavaScript bundles.</span></span> <span data-ttu-id="b3c83-178">この実装の大半は、[Angular Universal](https://universal.angular.io/) と呼ばれる Angular 機能によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-178">Most of the implementation of this comes from an Angular feature called [Angular Universal](https://universal.angular.io/).</span></span>

> [!TIP]
> <span data-ttu-id="b3c83-179">サーバー側のレンダリング (SSR) を有効にすると、開発時と配置時の両方で作業が複雑になります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-179">Enabling server-side rendering (SSR) introduces a number of extra complications both during development and deployment.</span></span> <span data-ttu-id="b3c83-180">「[SSR の欠点](#drawbacks-of-ssr)」を参照して、SSR が要件に適しているかどうかを判断してください。</span><span class="sxs-lookup"><span data-stu-id="b3c83-180">Read [drawbacks of SSR](#drawbacks-of-ssr) to determine if SSR is a good fit for your requirements.</span></span>

<span data-ttu-id="b3c83-181">SSR を有効にするには、プロジェクトに多数の追加を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-181">To enable SSR, you need to make a number of additions to your project.</span></span>

<span data-ttu-id="b3c83-182">*Startup* クラスで、`spa.Options.SourcePath` を構成する行の "*後ろ*" で、`UseAngularCliServer` または `UseProxyToSpaDevelopmentServer` への呼び出しの "*前*" の位置に、以下を追加します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-182">In the *Startup* class, *after* the line that configures `spa.Options.SourcePath`, and *before* the call to `UseAngularCliServer` or `UseProxyToSpaDevelopmentServer`, add the following:</span></span>

[!code-csharp[](sample/AngularServerSideRendering/Startup.cs?name=snippet_Call_UseSpa&highlight=5-12)]

<span data-ttu-id="b3c83-183">開発モードでは、このコードは、*ClientApp\package.json* に定義されている `build:ssr` スクリプトを実行して、SSR バンドルのビルドを試みます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-183">In development mode, this code attempts to build the SSR bundle by running the script `build:ssr`, which is defined in *ClientApp\package.json*.</span></span> <span data-ttu-id="b3c83-184">これにより、`ssr` という名前の Angular アプリがビルドされますが、これはまだ定義されていません。</span><span class="sxs-lookup"><span data-stu-id="b3c83-184">This builds an Angular app named `ssr`, which isn't yet defined.</span></span>

<span data-ttu-id="b3c83-185">*ClientApp/.angular-cli.json* の `apps` 配列の最後に、`ssr` という名前の追加アプリを定義します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-185">At the end of the `apps` array in *ClientApp/.angular-cli.json*, define an extra app with name `ssr`.</span></span> <span data-ttu-id="b3c83-186">次のオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-186">Use the following options:</span></span>

[!code-json[](sample/AngularServerSideRendering/ClientApp/.angular-cli.json?range=24-41)]

<span data-ttu-id="b3c83-187">この新しい SSR 対応アプリの構成には、さらに 2 つのファイルが必要です (*tsconfig.server.json* と *main.server.ts*)。</span><span class="sxs-lookup"><span data-stu-id="b3c83-187">This new SSR-enabled app configuration requires two further files: *tsconfig.server.json* and *main.server.ts*.</span></span> <span data-ttu-id="b3c83-188">*tsconfig.server.json* ファイルは、TypeScript のコンパイル オプションを指定します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-188">The *tsconfig.server.json* file specifies TypeScript compilation options.</span></span> <span data-ttu-id="b3c83-189">*main.server.ts* ファイルは、SSR 中のコードのエントリ ポイントとして機能します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-189">The *main.server.ts* file serves as the code entry point during SSR.</span></span>

<span data-ttu-id="b3c83-190">*tsconfig.server.json* という名前の新しいファイルを、*ClientApp/src* に (既存の *tsconfig.app.json* のそばに) 追加します。ファイルの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b3c83-190">Add a new file called *tsconfig.server.json* inside *ClientApp/src* (alongside the existing *tsconfig.app.json*), containing the following:</span></span>

[!code-json[](sample/AngularServerSideRendering/ClientApp/src/tsconfig.server.json)]

<span data-ttu-id="b3c83-191">このファイルは、`app.server.module` という名前のモジュールを検索する Angular の AoT コンパイラを構成します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-191">This file configures Angular's AoT compiler to look for a module called `app.server.module`.</span></span> <span data-ttu-id="b3c83-192">*ClientApp/src/app/app.server.module.ts* で (既存の *app.module.ts* のそばに) 以下の内容の新しいファイルを 作成することで、これを追加します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-192">Add this by creating a new file at *ClientApp/src/app/app.server.module.ts* (alongside the existing *app.module.ts*) containing the following:</span></span>

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/app/app.server.module.ts)]

<span data-ttu-id="b3c83-193">このモジュールは、クライアント側の `app.module` を継承し、SSR 中に使用できる追加の Angular モジュールを定義します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-193">This module inherits from your client-side `app.module` and defines which extra Angular modules are available during SSR.</span></span>

<span data-ttu-id="b3c83-194">*.angular cli.json* 内の新しい `ssr` エントリは、*main.server.ts* という名前のエントリ ポイント ファイルを参照することを思い出してください。</span><span class="sxs-lookup"><span data-stu-id="b3c83-194">Recall that the new `ssr` entry in *.angular-cli.json* referenced an entry point file called *main.server.ts*.</span></span> <span data-ttu-id="b3c83-195">そのファイルはまだ追加していません。ここで、それを実行します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-195">You haven't yet added that file, and now is time to do so.</span></span> <span data-ttu-id="b3c83-196">*ClientApp/src/main.server.ts* で (既存の *main.ts* のそばに) 以下の内容の新しいファイルを 作成します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-196">Create a new file at *ClientApp/src/main.server.ts* (alongside the existing *main.ts*), containing the following:</span></span>

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/main.server.ts)]

<span data-ttu-id="b3c83-197">このファイルのコードは、ASP.NET Core が *Startup* クラスに追加された `UseSpaPrerendering` ミドルウェアを実行するときに、各要求で何を実行するかを指定します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-197">This file's code is what ASP.NET Core executes for each request when it runs the `UseSpaPrerendering` middleware that you added to the *Startup* class.</span></span> <span data-ttu-id="b3c83-198">それは、.NET コードから `params` (要求されている URL など) の受信と、結果の HTML を取得するための Angular SSR API を呼び出しを処理します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-198">It deals with receiving `params` from the .NET code (such as the URL being requested), and making calls to Angular SSR APIs to get the resulting HTML.</span></span>

<span data-ttu-id="b3c83-199">厳密に言うと、開発モードで SSR を有効にするにはこれで十分です。</span><span class="sxs-lookup"><span data-stu-id="b3c83-199">Strictly-speaking, this is sufficient to enable SSR in development mode.</span></span> <span data-ttu-id="b3c83-200">アプリが発行されたときに正常に動作するように、最後に 1 つ変更を行うことが重要です。</span><span class="sxs-lookup"><span data-stu-id="b3c83-200">It's essential to make one final change so that your app works correctly when published.</span></span> <span data-ttu-id="b3c83-201">アプリのメインの *.csproj* ファイルで、`BuildServerSideRenderer` プロパティの値を `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-201">In your app's main *.csproj* file, set the `BuildServerSideRenderer` property value to `true`:</span></span>

[!code-xml[](sample/AngularServerSideRendering/AngularServerSideRendering.csproj?name=snippet_EnableBuildServerSideRenderer)]

<span data-ttu-id="b3c83-202">これにより、発行時に `build:ssr` を実行し、SSR ファイルをサーバーに配置するようにビルド プロセスが構成されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-202">This configures the build process to run `build:ssr` during publishing and deploy the SSR files to the server.</span></span> <span data-ttu-id="b3c83-203">これを有効にしない場合、SSR は運用環境では失敗します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-203">If you don't enable this, SSR fails in production.</span></span>

<span data-ttu-id="b3c83-204">アプリを開発または運用モードで実行す場合、Angular コードは、サーバー上で HTML として事前レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-204">When your app runs in either development or production mode, the Angular code pre-renders as HTML on the server.</span></span> <span data-ttu-id="b3c83-205">クライアント側のコードは、通常どおり実行されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-205">The client-side code executes as normal.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="b3c83-206">.NET コードから TypeScript コードに データを渡す</span><span class="sxs-lookup"><span data-stu-id="b3c83-206">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="b3c83-207">SSR 中は、要求ごとのデータを ASP.NET Core アプリから Angular アプリに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-207">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="b3c83-208">たとえば、cookie 情報やデータベースから読み取ったデータを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-208">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="b3c83-209">これを行うには、*Startup* クラスを編集します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-209">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="b3c83-210">`UseSpaPrerendering` のコールバックに、次のような `options.SupplyData` の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-210">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="b3c83-211">`SupplyData` コールバックでは、任意の要求ごとの JSON でシリアル可能なデータ (文字列、ブール値、数値など) を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-211">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="b3c83-212">*main.server.ts* コードは、これを `params.data` として受け取ります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-212">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="b3c83-213">たとえば、上記のコード サンプルは、ブール値を `params.data.isHttpsRequest` として `createServerRenderer` コールバックに渡しています。</span><span class="sxs-lookup"><span data-stu-id="b3c83-213">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="b3c83-214">Angular でサポートされている方法で、アプリの他の部分にこれを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-214">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="b3c83-215">たとえば、*main.server.ts* が、`BASE_URL` 値をコンストラクターが受け取ることを宣言されているコンポーネントにその値を渡す方法を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b3c83-215">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="b3c83-216">SSR の欠点</span><span class="sxs-lookup"><span data-stu-id="b3c83-216">Drawbacks of SSR</span></span>

<span data-ttu-id="b3c83-217">すべてのアプリが SSR から利益を得られるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="b3c83-217">Not all apps benefit from SSR.</span></span> <span data-ttu-id="b3c83-218">主な利益は、見かけ上のパフォーマンスです。</span><span class="sxs-lookup"><span data-stu-id="b3c83-218">The primary benefit is perceived performance.</span></span> <span data-ttu-id="b3c83-219">JavaScript バンドルのフェッチまたは解析にしばらく時間がかかる場合でも、低速のネットワーク接続または低速のモバイル デバイスでアプリに到達したユーザーに、初期 UI がすぐに表示されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-219">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="b3c83-220">ただし、多くの SPA は、主に高速の社内ネットワーク コンピューターで使用されているため、アプリはほぼ瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-220">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="b3c83-221">同時に、SSR の有効化には重大な欠点があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-221">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="b3c83-222">それは、開発プロセスの複雑さを深めます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-222">It adds complexity to your development process.</span></span> <span data-ttu-id="b3c83-223">ASP.NET Core から呼び出される Node.js 環境の中で、クライアント側とサーバー側 という 2 つの異なる環境でコードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-223">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="b3c83-224">考慮すべき点を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-224">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="b3c83-225">SSR では、運用サーバー上に Node.js をインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-225">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="b3c83-226">これは、Azure App Services などの一部のデプロイ シナリオでは自動的に実行されますが、Azure Service Fabric などの他のシナリオではそうではありません。</span><span class="sxs-lookup"><span data-stu-id="b3c83-226">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="b3c83-227">`BuildServerSideRenderer` ビルド フラグを有効にすると、*node_modules* ディレクトリが発行されます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-227">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="b3c83-228">このフォルダーには、20,000 以上のファイルが含まれているため、配置時間が増加します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-228">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="b3c83-229">Node.js 環境でコードを実行するために、`window` や `localStorage` などのブラウザー固有の JavaScript API の存在に頼ることはできません。</span><span class="sxs-lookup"><span data-stu-id="b3c83-229">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="b3c83-230">コード (または参照された一部のサードパーティ製ライブラリ) がこれらの API を使用しようとすると、SSR 中にエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-230">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="b3c83-231">たとえば、jQuery は多くの場所でブラウザー固有の API を参照するため、それを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="b3c83-231">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="b3c83-232">エラーを防ぐには、SSR を有効にしないか、ブラウザー固有の API またはライブラリを使用しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3c83-232">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="b3c83-233">SSR 中に呼び出されることがないように、このような API への呼び出しをチェックしてラップできます。</span><span class="sxs-lookup"><span data-stu-id="b3c83-233">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="b3c83-234">たとえば、JavaScript または TypeScript コードで、次のようなチェックを使用します。</span><span class="sxs-lookup"><span data-stu-id="b3c83-234">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```
