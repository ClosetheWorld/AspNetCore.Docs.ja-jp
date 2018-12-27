---
title: ASP.NET Core での Google 外部ログインのセットアップ
author: rick-anderson
description: このチュートリアルでは、既存の ASP.NET Core アプリに Google アカウントのユーザー認証の統合について説明します。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/11/2018
uid: security/authentication/google-logins
ms.openlocfilehash: 4bb5a36cf654deb694d60da126fa42baf382f729
ms.sourcegitcommit: 3e94d192b2ed9409fe72e3735e158b333354964c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735766"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="36680-103">ASP.NET Core での Google 外部ログインのセットアップ</span><span class="sxs-lookup"><span data-stu-id="36680-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="36680-104">作成者: [Valeriy Novytskyy](https://github.com/01binary)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="36680-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="36680-105">このチュートリアルでは、サンプルの ASP.NET Core 2.0 プロジェクトが作成を使用して、Google + アカウントでサインインするユーザーを有効にする方法、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="36680-105">This tutorial shows you how to enable your users to sign in with their Google+ account using a sample ASP.NET Core 2.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="36680-106">まず、次の[公式手順](https://developers.google.com/identity/sign-in/web/devconsole-project)Google API コンソールで新しいアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="36680-106">We start by following the [official steps](https://developers.google.com/identity/sign-in/web/devconsole-project) to create a new app in Google API Console.</span></span>

## <a name="create-the-app-in-google-api-console"></a><span data-ttu-id="36680-107">Google API コンソールで、アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="36680-107">Create the app in Google API Console</span></span>

* <span data-ttu-id="36680-108">移動します[ https://console.developers.google.com/projectselector/apis/library ](https://console.developers.google.com/projectselector/apis/library)してサインインします。</span><span class="sxs-lookup"><span data-stu-id="36680-108">Navigate to [https://console.developers.google.com/projectselector/apis/library](https://console.developers.google.com/projectselector/apis/library) and sign in.</span></span> <span data-ttu-id="36680-109">Google アカウントがない場合は、使用**より多くのオプション** > **[アカウントを作成する](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)** 作成へのリンク。</span><span class="sxs-lookup"><span data-stu-id="36680-109">If you don't already have a Google account, use **More options** > **[Create account](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)** link to create one:</span></span>

![Google API コンソール](index/_static/GoogleConsoleLogin.png)

* <span data-ttu-id="36680-111">リダイレクトされます**API Manager ライブラリ**ページ。</span><span class="sxs-lookup"><span data-stu-id="36680-111">You are redirected to **API Manager Library** page:</span></span>

![API Manager ライブラリのページを読み込む](index/_static/GoogleConsoleSwitchboard.png)

* <span data-ttu-id="36680-113">タップ**作成**を入力し、**プロジェクト名**:</span><span class="sxs-lookup"><span data-stu-id="36680-113">Tap **Create** and enter your **Project name**:</span></span>

![[新しいプロジェクト] ダイアログ](index/_static/GoogleConsoleNewProj.png)

* <span data-ttu-id="36680-115">ダイアログを受け入れた場合、新しいアプリの機能を選択できるようにライブラリ ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="36680-115">After accepting the dialog, you are redirected back to the Library page allowing you to choose features for your new app.</span></span> <span data-ttu-id="36680-116">検索**Google + API**リストを API 機能を追加するには、そのリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="36680-116">Find **Google+ API** in the list and click on its link to add the API feature:</span></span>

![API Manager ライブラリ ページで"Google + API"を検索します。](index/_static/GoogleConsoleChooseApi.png)

* <span data-ttu-id="36680-118">新しく追加された API のページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="36680-118">The page for the newly added API is displayed.</span></span> <span data-ttu-id="36680-119">タップ**を有効にする**機能で、アプリに Google + 記号を追加します。</span><span class="sxs-lookup"><span data-stu-id="36680-119">Tap **Enable** to add Google+ sign in feature to your app:</span></span>

![Google + API の API マネージャー ページを読み込む](index/_static/GoogleConsoleEnableApi.png)

* <span data-ttu-id="36680-121">API を有効にした後は、タップ**資格情報を作成**シークレットを構成します。</span><span class="sxs-lookup"><span data-stu-id="36680-121">After enabling the API, tap **Create credentials** to configure the secrets:</span></span>

![Google + API の API マネージャー ページで資格情報 ボタンを作成します。](index/_static/GoogleConsoleGoCredentials.png)

* <span data-ttu-id="36680-123">選択するオプション</span><span class="sxs-lookup"><span data-stu-id="36680-123">Choose:</span></span>
  * <span data-ttu-id="36680-124">**Google+ API**</span><span class="sxs-lookup"><span data-stu-id="36680-124">**Google+ API**</span></span>
  * <span data-ttu-id="36680-125">**Web サーバー (例: node.js、Tomcat)** と</span><span class="sxs-lookup"><span data-stu-id="36680-125">**Web server (e.g. node.js, Tomcat)**, and</span></span>
  * <span data-ttu-id="36680-126">**ユーザー データ**:</span><span class="sxs-lookup"><span data-stu-id="36680-126">**User data**:</span></span>

![API マネージャーの資格情報 ページ:必要なパネルをどのような種類の資格情報をご確認ください。](index/_static/GoogleConsoleChooseCred.png)

* <span data-ttu-id="36680-128">タップ**どのような資格情報が必要ですか?** アプリの構成の 2 番目の手順に移動する**OAuth 2.0 クライアント ID を作成**:</span><span class="sxs-lookup"><span data-stu-id="36680-128">Tap **What credentials do I need?** which takes you to the second step of app configuration, **Create an OAuth 2.0 client ID**:</span></span>

![API マネージャーの資格情報 ページ:OAuth 2.0 クライアント ID を作成します。](index/_static/GoogleConsoleCreateClient.png)

* <span data-ttu-id="36680-130">1 つの機能 (サインイン)、同じ入力で Google + プロジェクトを作成するため**名前**としてプロジェクトを使用した OAuth 2.0 クライアントの id。</span><span class="sxs-lookup"><span data-stu-id="36680-130">Because we are creating a Google+ project with just one feature (sign in), we can enter the same **Name** for the OAuth 2.0 client ID as the one we used for the project.</span></span>

* <span data-ttu-id="36680-131">開発 URI を入力と`/signin-google`に追加されます、**承認済みのリダイレクト Uri**フィールド (例: `https://localhost:44320/signin-google`)。</span><span class="sxs-lookup"><span data-stu-id="36680-131">Enter your development URI with `/signin-google` appended into the **Authorized redirect URIs** field (for example: `https://localhost:44320/signin-google`).</span></span> <span data-ttu-id="36680-132">このチュートリアルの後半で構成されている Google 認証の要求では自動的に処理`/signin-google`OAuth フローを実装するルート。</span><span class="sxs-lookup"><span data-stu-id="36680-132">The Google authentication configured later in this tutorial will automatically handle requests at `/signin-google` route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="36680-133">URI セグメント`/signin-google`Google の認証プロバイダーの既定のコールバックとして設定されます。</span><span class="sxs-lookup"><span data-stu-id="36680-133">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="36680-134">既定のコールバック URI を変更するには、継承を使用して、Google の認証ミドルウェアを構成するときに[RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)のプロパティ、 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)クラス。</span><span class="sxs-lookup"><span data-stu-id="36680-134">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

* <span data-ttu-id="36680-135">Tab キーを追加する、**承認済みのリダイレクト Uri**エントリ。</span><span class="sxs-lookup"><span data-stu-id="36680-135">Press TAB to add the **Authorized redirect URIs** entry.</span></span>

* <span data-ttu-id="36680-136">タップ**クライアント ID の作成**、3 番目の手順に移動する **、OAuth 2.0 同意画面設定**:</span><span class="sxs-lookup"><span data-stu-id="36680-136">Tap **Create client ID**, which takes you to the third step, **Set up the OAuth 2.0 consent screen**:</span></span>

![API マネージャーの資格情報 ページ:OAuth 2.0 同意画面を設定します。](index/_static/GoogleConsoleAddCred.png)

* <span data-ttu-id="36680-138">入力、パブリックを対象に**電子メール アドレス**と**製品名**Google + にサインインするユーザーを要求するときに、アプリに表示します。</span><span class="sxs-lookup"><span data-stu-id="36680-138">Enter your public facing **Email address** and the **Product name** shown for your app when Google+ prompts the user to sign in.</span></span> <span data-ttu-id="36680-139">追加のオプションが 利用可能な**その他のカスタマイズ オプション**します。</span><span class="sxs-lookup"><span data-stu-id="36680-139">Additional options are available under **More customization options**.</span></span>

* <span data-ttu-id="36680-140">タップ**続行**最後の手順を続行する**資格情報をダウンロード**:</span><span class="sxs-lookup"><span data-stu-id="36680-140">Tap **Continue** to proceed to the last step, **Download credentials**:</span></span>

![API マネージャーの資格情報 ページ:資格情報をダウンロードします。](index/_static/GoogleConsoleFinish.png)

* <span data-ttu-id="36680-142">タップ**ダウンロード**アプリケーションのシークレットの JSON ファイルを保存して**完了**新しいアプリの作成を完了します。</span><span class="sxs-lookup"><span data-stu-id="36680-142">Tap **Download** to save a JSON file with application secrets, and **Done** to complete creation of the new app.</span></span>

* <span data-ttu-id="36680-143">サイトをデプロイするときに、再アクセスする必要があります、 **Google コンソール**し、新しいパブリック url を登録します。</span><span class="sxs-lookup"><span data-stu-id="36680-143">When deploying the site you'll need to revisit the **Google Console** and register a new public url.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="36680-144">ストア Google ClientID と ClientSecret</span><span class="sxs-lookup"><span data-stu-id="36680-144">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="36680-145">Google のような機密性の高い設定リンク`Client ID`と`Client Secret`アプリケーションの構成を使用して、 [Secret Manager](xref:security/app-secrets)します。</span><span class="sxs-lookup"><span data-stu-id="36680-145">Link sensitive settings like Google `Client ID` and `Client Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="36680-146">このチュートリアルの目的で、名前トークン`Authentication:Google:ClientId`と`Authentication:Google:ClientSecret`します。</span><span class="sxs-lookup"><span data-stu-id="36680-146">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`.</span></span>

<span data-ttu-id="36680-147">前の手順でダウンロードした JSON ファイルでこれらのトークンの値が見つかります`web.client_id`と`web.client_secret`します。</span><span class="sxs-lookup"><span data-stu-id="36680-147">The values for these tokens can be found in the JSON file downloaded in the previous step under `web.client_id` and `web.client_secret`.</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="36680-148">Google 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="36680-148">Configure Google Authentication</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="36680-149">Google のサービスを追加、`ConfigureServices`メソッド*Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="36680-149">Add the Google service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="36680-150">このチュートリアルで使用するプロジェクト テンプレートにより[Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)パッケージがインストールされています。</span><span class="sxs-lookup"><span data-stu-id="36680-150">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) package is installed.</span></span>

* <span data-ttu-id="36680-151">Visual Studio 2017 では、このパッケージをインストールするには、クリックし、プロジェクトを右クリックし**NuGet パッケージの管理**します。</span><span class="sxs-lookup"><span data-stu-id="36680-151">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="36680-152">で .NET Core CLI をインストールするには、プロジェクト ディレクトリで、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="36680-152">To install with .NET Core CLI, execute the following in your project directory:</span></span>

`dotnet add package Microsoft.AspNetCore.Authentication.Google`

<span data-ttu-id="36680-153">Google のミドルウェアを追加、`Configure`メソッド*Startup.cs*ファイル。</span><span class="sxs-lookup"><span data-stu-id="36680-153">Add the Google middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

::: moniker-end

<span data-ttu-id="36680-154">参照してください、 [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) Google 認証でサポートされる構成オプションの詳細について、API リファレンス。</span><span class="sxs-lookup"><span data-stu-id="36680-154">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="36680-155">これは、ユーザーに関するさまざまな情報を要求を使用できます。</span><span class="sxs-lookup"><span data-stu-id="36680-155">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-google"></a><span data-ttu-id="36680-156">Google のサインイン</span><span class="sxs-lookup"><span data-stu-id="36680-156">Sign in with Google</span></span>

<span data-ttu-id="36680-157">アプリケーションを実行し、をクリックして**ログイン**します。</span><span class="sxs-lookup"><span data-stu-id="36680-157">Run your application and click **Log in**.</span></span> <span data-ttu-id="36680-158">Google でサインインするためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="36680-158">An option to sign in with Google appears:</span></span>

![Microsoft Edge で実行されている web アプリケーション:ユーザーが認証されていません。](index/_static/DoneGoogle.png)

<span data-ttu-id="36680-160">Google をクリックすると認証に Google にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="36680-160">When you click on Google, you are redirected to Google for authentication:</span></span>

![Google 認証のダイアログ ボックス](index/_static/GoogleLogin.png)

<span data-ttu-id="36680-162">Google の資格情報を入力すると、し、リダイレクトされます、電子メールを設定する web サイトにフェールバックします。</span><span class="sxs-lookup"><span data-stu-id="36680-162">After entering your Google credentials, then you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="36680-163">Google の資格情報を使用してログインしました。</span><span class="sxs-lookup"><span data-stu-id="36680-163">You are now logged in using your Google credentials:</span></span>

![Microsoft Edge で実行されている web アプリケーション:認証されたユーザー](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="36680-165">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="36680-165">Troubleshooting</span></span>

* <span data-ttu-id="36680-166">表示された場合、`403 (Forbidden)`開発モード (または同じエラーでデバッガーを中断) で実行していることを確認すると、独自のアプリからのエラー ページ**Google + API**が有効になって、 **API Manager ライブラリ**記載されている手順に従って[このページで以前](#create-the-app-in-google-api-console)します。</span><span class="sxs-lookup"><span data-stu-id="36680-166">If you receive a `403 (Forbidden)` error page from your own app when running in development mode (or break into the debugger with the same error), ensure that **Google+ API** has been enabled in the **API Manager Library** by following the steps listed [earlier on this page](#create-the-app-in-google-api-console).</span></span> <span data-ttu-id="36680-167">サインインが機能しないすべてのエラー通知が届かない場合は、問題をデバッグしやすくする開発モードに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="36680-167">If the sign in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="36680-168">**ASP.NET Core 2.x のみ。** ユーザーが呼び出すことによって構成されていない場合`services.AddIdentity`で`ConfigureServices`、認証を試みるが*ArgumentException:'SignInScheme' オプションを指定する必要があります*します。</span><span class="sxs-lookup"><span data-stu-id="36680-168">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="36680-169">このチュートリアルで使用するプロジェクト テンプレートによりこれが行われるようになります。</span><span class="sxs-lookup"><span data-stu-id="36680-169">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="36680-170">最初の移行を適用することで、サイト データベースが作成されていない場合になります*要求の処理中にデータベース操作が失敗しました*エラー。</span><span class="sxs-lookup"><span data-stu-id="36680-170">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="36680-171">タップ**適用移行**データベースを作成し、エラーを引き続き更新します。</span><span class="sxs-lookup"><span data-stu-id="36680-171">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="36680-172">次の手順</span><span class="sxs-lookup"><span data-stu-id="36680-172">Next steps</span></span>

* <span data-ttu-id="36680-173">この記事では、Google で認証する方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="36680-173">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="36680-174">記載されているその他のプロバイダーで認証する同様のアプローチを利用できる、[前のページ](xref:security/authentication/social/index)します。</span><span class="sxs-lookup"><span data-stu-id="36680-174">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="36680-175">リセットする必要があります、web サイトを Azure web アプリを発行すると、 `ClientSecret` Google API コンソールでします。</span><span class="sxs-lookup"><span data-stu-id="36680-175">Once you publish your web site to Azure web app, you should reset the `ClientSecret` in the Google API Console.</span></span>

* <span data-ttu-id="36680-176">設定、`Authentication:Google:ClientId`と`Authentication:Google:ClientSecret`として、Azure portal でアプリケーションの設定。</span><span class="sxs-lookup"><span data-stu-id="36680-176">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="36680-177">構成システムは、環境変数からキーの読み取りを設定します。</span><span class="sxs-lookup"><span data-stu-id="36680-177">The configuration system is set up to read keys from environment variables.</span></span>
