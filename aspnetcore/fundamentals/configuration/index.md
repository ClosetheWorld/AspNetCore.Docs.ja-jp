---
title: ASP.NET Core の構成
author: guardrex
description: 構成 API を使用して、ASP.NET Core アプリを構成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: fundamentals/configuration/index
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="f212b-103">ASP.NET Core の構成</span><span class="sxs-lookup"><span data-stu-id="f212b-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="f212b-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f212b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f212b-105">ASP.NET Core でのアプリの構成は、"*構成プロバイダー*" によって設定するキーと値のペアに基づいています。</span><span class="sxs-lookup"><span data-stu-id="f212b-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="f212b-106">構成プロバイダーは、さまざまな構成のソースから構成データを読み取り、キーと値のペアを作成します。</span><span class="sxs-lookup"><span data-stu-id="f212b-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="f212b-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f212b-107">Azure Key Vault</span></span>
* <span data-ttu-id="f212b-108">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="f212b-108">Command-line arguments</span></span>
* <span data-ttu-id="f212b-109">カスタム プロバイダー (インストール済みまたは作成済み)</span><span class="sxs-lookup"><span data-stu-id="f212b-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="f212b-110">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="f212b-110">Directory files</span></span>
* <span data-ttu-id="f212b-111">環境変数</span><span class="sxs-lookup"><span data-stu-id="f212b-111">Environment variables</span></span>
* <span data-ttu-id="f212b-112">メモリ内 .NET オブジェクト</span><span class="sxs-lookup"><span data-stu-id="f212b-112">In-memory .NET objects</span></span>
* <span data-ttu-id="f212b-113">設定ファイル</span><span class="sxs-lookup"><span data-stu-id="f212b-113">Settings files</span></span>

<span data-ttu-id="f212b-114">"*オプション パターン*" は、このトピックで説明する構成の概念を拡張したものです。</span><span class="sxs-lookup"><span data-stu-id="f212b-114">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="f212b-115">オプションでは、クラスを使用して関連する設定のグループを表します。</span><span class="sxs-lookup"><span data-stu-id="f212b-115">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="f212b-116">オプション パターンの使用について詳しくは、「<xref:fundamentals/configuration/options>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f212b-116">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="f212b-117">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f212b-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f212b-118">これらの 3 つのパッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-118">These three packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="host-vs-app-configuration"></a><span data-ttu-id="f212b-119">ホストとアプリの構成</span><span class="sxs-lookup"><span data-stu-id="f212b-119">Host vs. app configuration</span></span>

<span data-ttu-id="f212b-120">アプリを構成して起動する前に、"*ホスト*" を構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="f212b-120">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="f212b-121">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="f212b-121">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="f212b-122">アプリとホストは、両方ともこのトピックで説明する構成プロバイダーを使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="f212b-122">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="f212b-123">ホストの構成のキーと値のペアは、アプリのグローバル構成の一部となります。</span><span class="sxs-lookup"><span data-stu-id="f212b-123">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="f212b-124">ホストをビルドするときの構成プロバイダーの使用方法、およびホストの構成に対する構成ソースの影響について詳しくは、[ホスト](xref:fundamentals/index#host)に関する説明を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-124">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="f212b-125">既定の構成</span><span class="sxs-lookup"><span data-stu-id="f212b-125">Default configuration</span></span>

<span data-ttu-id="f212b-126">ASP.NET Core の [dotnet new](/dotnet/core/tools/dotnet-new) テンプレートに基づく Web アプリは、ホストの構築時に <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-126">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="f212b-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> により、次の順序でアプリの既定の構成が提供されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="f212b-128">ホストの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-128">Host configuration is provided from:</span></span>
  * <span data-ttu-id="f212b-129">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する、プレフィックス `ASPNETCORE_` (`ASPNETCORE_ENVIRONMENT` など) が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="f212b-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f212b-130">構成のキーと値のペアが読み込まれるときに、プレフィックス (`ASPNETCORE_`) は削除されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-130">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="f212b-131">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="f212b-131">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="f212b-132">アプリの構成は、次から提供されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-132">App configuration is provided from:</span></span>
  * <span data-ttu-id="f212b-133">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="f212b-133">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="f212b-134">[ファイル構成プロバイダー](#file-configuration-provider)を使用する *appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="f212b-134">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="f212b-135">エントリ アセンブリを使用して `Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="f212b-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="f212b-136">[環境変数構成プロバイダー](#environment-variables-configuration-provider)を使用する環境変数。</span><span class="sxs-lookup"><span data-stu-id="f212b-136">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f212b-137">カスタム プレフィックスが使用されている場合 (たとえば、`PREFIX_` と `.AddEnvironmentVariables(prefix: "PREFIX_")`)、構成のキーと値のペアが読み込まれるときにプレフィックスが削除されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-137">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="f212b-138">[コマンドライン構成プロバイダー](#command-line-configuration-provider)を使用するコマンドライン引数。</span><span class="sxs-lookup"><span data-stu-id="f212b-138">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="f212b-139">これらの構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="f212b-139">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="f212b-140">ホストと <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> の詳細については、<xref:fundamentals/host/web-host#set-up-a-host> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-140">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="f212b-141">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="f212b-141">Security</span></span>

<span data-ttu-id="f212b-142">次のベスト プラクティスを採用します。</span><span class="sxs-lookup"><span data-stu-id="f212b-142">Adopt the following best practices:</span></span>

* <span data-ttu-id="f212b-143">構成プロバイダーのコードやプレーンテキストの構成ファイルには、パスワードなどの機密データを格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="f212b-143">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="f212b-144">開発環境やテスト環境では運用シークレットを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="f212b-144">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="f212b-145">プロジェクトの外部にシークレットを指定してください。そうすれば、誤ってリソース コード リポジトリにコミットされることはありません。</span><span class="sxs-lookup"><span data-stu-id="f212b-145">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="f212b-146">[複数の環境を使用する方法](xref:fundamentals/environments)や、[シークレット マネージャーでの開発中のアプリ シークレットの安全な格納](xref:security/app-secrets)の管理 (機密データを格納するための環境変数の使用に関するアドバイスを含む) について、さらに学習することができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-146">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="f212b-147">シークレット マネージャーは、ファイル構成プロバイダーを使用して、ユーザーの機密情報をローカル システム上の JSON ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="f212b-147">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="f212b-148">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="f212b-148">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="f212b-149">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) は、アプリのシークレットを安全に格納するための 1 つのオプションです。</span><span class="sxs-lookup"><span data-stu-id="f212b-149">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="f212b-150">詳細については、「<xref:security/key-vault-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-150">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="f212b-151">階層的な構成データ</span><span class="sxs-lookup"><span data-stu-id="f212b-151">Hierarchical configuration data</span></span>

<span data-ttu-id="f212b-152">構成 API は、構成キー内の区切り記号を使用して階層データをフラット化することにより、階層的な構成データを管理することができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-152">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="f212b-153">次の JSON ファイルでは、2 つのセクションの構造化階層に 4 つのキーが存在します。</span><span class="sxs-lookup"><span data-stu-id="f212b-153">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="f212b-154">ファイルが構成に読み込まれると、構成ソースの元の階層データ構造を維持するために、一意なキーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-154">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="f212b-155">セクションとキーは、元の構造を維持するために、コロン (`:`) を使用してフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-155">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="f212b-156">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-156">section0:key0</span></span>
* <span data-ttu-id="f212b-157">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-157">section0:key1</span></span>
* <span data-ttu-id="f212b-158">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-158">section1:key0</span></span>
* <span data-ttu-id="f212b-159">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-159">section1:key1</span></span>

<span data-ttu-id="f212b-160"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> メソッドと <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> メソッドを使用して、構成データ内のセクションとセクションの子を分離することができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-160"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="f212b-161">これらのメソッドについては、後ほど「[GetSection、GetChildren、Exists](#getsection-getchildren-and-exists)」で説明します。</span><span class="sxs-lookup"><span data-stu-id="f212b-161">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="f212b-162">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-162">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="f212b-163">規約</span><span class="sxs-lookup"><span data-stu-id="f212b-163">Conventions</span></span>

<span data-ttu-id="f212b-164">アプリの起動時に、各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="f212b-164">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="f212b-165">ファイル構成プロバイダーには、アプリの起動後に基になる設定ファイルが変更された場合に、構成を再読み込みする機能があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-165">File Configuration Providers have the ability to reload configuration when an underlying settings file is changed after app startup.</span></span> <span data-ttu-id="f212b-166">ファイル構成プロバイダーについては、このトピックの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="f212b-166">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="f212b-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> は、アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) コンテナーで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f212b-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> を Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> を挿入して、クラスの構成を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="f212b-169">構成プロバイダーでは DI を使用できません。ホストによって構成プロバイダーが設定されている場合、DI を使用できないためです。</span><span class="sxs-lookup"><span data-stu-id="f212b-169">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="f212b-170">構成キーでは、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-170">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="f212b-171">キーでは、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="f212b-171">Keys are case-insensitive.</span></span> <span data-ttu-id="f212b-172">たとえば、`ConnectionString` と `connectionstring` は同等のキーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="f212b-172">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="f212b-173">同じキーに対する値が、同じまたは別の構成プロバイダーによって設定された場合、最後にキーに設定された値が使用される値となります。</span><span class="sxs-lookup"><span data-stu-id="f212b-173">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="f212b-174">階層キー</span><span class="sxs-lookup"><span data-stu-id="f212b-174">Hierarchical keys</span></span>
  * <span data-ttu-id="f212b-175">構成 API 内では、すべてのプラットフォームでコロン (`:`) の区切りが機能します。</span><span class="sxs-lookup"><span data-stu-id="f212b-175">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="f212b-176">環境変数内では、コロン区切りがすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-176">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="f212b-177">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに変換されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-177">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="f212b-178">Azure Key Vault では、階層キーは区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="f212b-178">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="f212b-179">コードを指定して、アプリの構成にシークレットが読み込まれるときにダッシュをコロンで置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-179">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="f212b-180"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="f212b-180">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f212b-181">配列のバインドについては、「[配列をクラスにバインドする](#bind-an-array-to-a-class)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="f212b-181">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="f212b-182">構成値では、次の規則が採用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-182">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="f212b-183">値は文字列です。</span><span class="sxs-lookup"><span data-stu-id="f212b-183">Values are strings.</span></span>
* <span data-ttu-id="f212b-184">Null 値を構成に格納したり、オブジェクトにバインドしたりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="f212b-184">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="f212b-185">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-185">Providers</span></span>

<span data-ttu-id="f212b-186">ASP.NET Core アプリで使用できる構成プロバイダーを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="f212b-186">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="f212b-187">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-187">Provider</span></span> | <span data-ttu-id="f212b-188">&hellip; から構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="f212b-188">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="f212b-189">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="f212b-189">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="f212b-190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f212b-190">Azure Key Vault</span></span> |
| [<span data-ttu-id="f212b-191">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-191">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="f212b-192">コマンド ライン パラメーター</span><span class="sxs-lookup"><span data-stu-id="f212b-192">Command-line parameters</span></span> |
| [<span data-ttu-id="f212b-193">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-193">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="f212b-194">カスタム ソース</span><span class="sxs-lookup"><span data-stu-id="f212b-194">Custom source</span></span> |
| [<span data-ttu-id="f212b-195">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-195">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="f212b-196">環境変数</span><span class="sxs-lookup"><span data-stu-id="f212b-196">Environment variables</span></span> |
| [<span data-ttu-id="f212b-197">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-197">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="f212b-198">ファイル (INI、JSON、XML)</span><span class="sxs-lookup"><span data-stu-id="f212b-198">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="f212b-199">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-199">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="f212b-200">ディレクトリ ファイル</span><span class="sxs-lookup"><span data-stu-id="f212b-200">Directory files</span></span> |
| [<span data-ttu-id="f212b-201">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-201">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="f212b-202">メモリ内コレクション</span><span class="sxs-lookup"><span data-stu-id="f212b-202">In-memory collections</span></span> |
| <span data-ttu-id="f212b-203">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) ("*セキュリティ*" トピック)</span><span class="sxs-lookup"><span data-stu-id="f212b-203">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="f212b-204">ユーザー プロファイル ディレクトリ内のファイル</span><span class="sxs-lookup"><span data-stu-id="f212b-204">File in the user profile directory</span></span> |

<span data-ttu-id="f212b-205">アプリの起動時に各構成プロバイダーが指定されている順序で構成ソースが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="f212b-205">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="f212b-206">このトピックで説明する構成プロバイダーは、それらをコードで配置する順ではなく、アルファベット順で説明します。</span><span class="sxs-lookup"><span data-stu-id="f212b-206">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="f212b-207">基になる構成ソースの優先順位に合わせるために、コード内で構成プロバイダーを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="f212b-207">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="f212b-208">一般的な一連の構成プロバイダーは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f212b-208">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="f212b-209">ファイル (*appsettings.json*、*appsettings.{Environment}.json*。`{Environment}` はアプリの現在のホスト環境です)</span><span class="sxs-lookup"><span data-stu-id="f212b-209">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="f212b-210">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f212b-210">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="f212b-211">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合のみ)</span><span class="sxs-lookup"><span data-stu-id="f212b-211">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="f212b-212">環境変数</span><span class="sxs-lookup"><span data-stu-id="f212b-212">Environment variables</span></span>
1. <span data-ttu-id="f212b-213">コマンド ライン引数</span><span class="sxs-lookup"><span data-stu-id="f212b-213">Command-line arguments</span></span>

<span data-ttu-id="f212b-214">コマンド ライン引数が他のプロバイダーによって設定された構成をオーバーライドできるようにするために、コマンド ラインの構成プロバイダーを一連のプロバイダーの最後に配置するのは、一般的な方法です。</span><span class="sxs-lookup"><span data-stu-id="f212b-214">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="f212b-215">この一連のプロバイダーは、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を使用して新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を初期化するときに配置されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-215">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="f212b-216">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-216">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="f212b-217">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="f212b-217">ConfigureAppConfiguration</span></span>

<span data-ttu-id="f212b-218">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出し、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> によって自動的に追加されるものに加え、アプリの構成プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-218">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

<span data-ttu-id="f212b-219"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> のアプリに指定した構成は、`Startup.ConfigureServices` などのアプリの起動中に使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-219">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f212b-220">詳細については、「[起動中に構成にアクセスする](#access-configuration-during-startup)」のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-220">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="f212b-221">コマンド ライン構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-221">Command-line Configuration Provider</span></span>

<span data-ttu-id="f212b-222"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> では、実行時にコマンドライン引数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-222">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="f212b-223">コマンド ライン構成をアクティブにするために、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 拡張メソッドが <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-223">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f212b-224"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> で新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を初期化すると、自動的に `AddCommandLine` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-224">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="f212b-225">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-225">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="f212b-226">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-226">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f212b-227">*appsettings.json* および *appsettings.{Environment}.json* からのオプションの構成。</span><span class="sxs-lookup"><span data-stu-id="f212b-227">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="f212b-228">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合)。</span><span class="sxs-lookup"><span data-stu-id="f212b-228">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="f212b-229">環境変数。</span><span class="sxs-lookup"><span data-stu-id="f212b-229">Environment variables.</span></span>

<span data-ttu-id="f212b-230">`CreateDefaultBuilder` はコマンド ライン構成プロバイダーを最後に追加します。</span><span class="sxs-lookup"><span data-stu-id="f212b-230">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="f212b-231">実行時に渡されるコマンド ライン引数によって、他のプロバイダーによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="f212b-231">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="f212b-232">`CreateDefaultBuilder` は、ホストが作成されるときに機能します。</span><span class="sxs-lookup"><span data-stu-id="f212b-232">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="f212b-233">そのため、`CreateDefaultBuilder` によってアクティブ化されるコマンド ライン構成によって、ホストの構成方法に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-233">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="f212b-234">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-234">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="f212b-235">`AddCommandLine` は既に `CreateDefaultBuilder` によって呼び出されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-235">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f212b-236">アプリの構成を指定して、引き続きコマンドラインの引数でその構成をオーバーライドできるようにする必要がある場合、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> でアプリの追加のプロバイダーを呼び出し、最後に `AddCommandLine` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-236">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-237"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-237">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f212b-238">**例**</span><span class="sxs-lookup"><span data-stu-id="f212b-238">**Example**</span></span>

<span data-ttu-id="f212b-239">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-239">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="f212b-240">プロジェクトのディレクトリでコマンド プロンプトを開きます。</span><span class="sxs-lookup"><span data-stu-id="f212b-240">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="f212b-241">`dotnet run` コマンドにコマンドライン引数を指定します (`dotnet run CommandLineKey=CommandLineValue`)。</span><span class="sxs-lookup"><span data-stu-id="f212b-241">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="f212b-242">アプリを実行したら、アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="f212b-242">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f212b-243">出力に、`dotnet run` に提供される構成のコマンド ライン引数のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="f212b-243">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="f212b-244">引数</span><span class="sxs-lookup"><span data-stu-id="f212b-244">Arguments</span></span>

<span data-ttu-id="f212b-245">値は等号 (`=`) の後に続ける必要があります。または、値をスペースの後に続ける場合は、キーにプレフィックス (`--`または`/`) を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-245">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="f212b-246">等号を使用する場合は、値に null を指定できます (例: `CommandLineKey=`)。</span><span class="sxs-lookup"><span data-stu-id="f212b-246">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="f212b-247">キーのプレフィックス</span><span class="sxs-lookup"><span data-stu-id="f212b-247">Key prefix</span></span>               | <span data-ttu-id="f212b-248">例</span><span class="sxs-lookup"><span data-stu-id="f212b-248">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="f212b-249">プレフィックスなし</span><span class="sxs-lookup"><span data-stu-id="f212b-249">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="f212b-250">2 つのダッシュ (`--`)</span><span class="sxs-lookup"><span data-stu-id="f212b-250">Two dashes (`--`)</span></span>        | <span data-ttu-id="f212b-251">`--CommandLineKey2=value2`、 `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="f212b-251">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="f212b-252">スラッシュ (`/`)</span><span class="sxs-lookup"><span data-stu-id="f212b-252">Forward slash (`/`)</span></span>      | <span data-ttu-id="f212b-253">`/CommandLineKey3=value3`、 `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="f212b-253">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="f212b-254">同じコマンド内のコマンド ライン引数で、等号を使用するキーと値のペアと、スペースを使用するキーと値のペアを混在させないでください。</span><span class="sxs-lookup"><span data-stu-id="f212b-254">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="f212b-255">コマンドの例:</span><span class="sxs-lookup"><span data-stu-id="f212b-255">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="f212b-256">スイッチ マッピング</span><span class="sxs-lookup"><span data-stu-id="f212b-256">Switch mappings</span></span>

<span data-ttu-id="f212b-257">スイッチ マッピングでは、キー名の交換ロジックが許可されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-257">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="f212b-258"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> で構成を手動でビルドするときに、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> メソッドにスイッチ置換のディクショナリを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-258">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="f212b-259">スイッチ マッピング ディクショナリが使用されている場合、そのディレクトリで、コマンドライン引数によって指定されたキーと一致するキーが確認されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-259">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="f212b-260">コマンド ライン キーがディクショナリで見つかった場合は、アプリの構成にキーと値のペアを設定するためにディクショナリの値 (キー交換) が返されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-260">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="f212b-261">スイッチ マッピングは、単一のダッシュ (`-`) が前に付いたすべてのコマンドライン キーに必要です。</span><span class="sxs-lookup"><span data-stu-id="f212b-261">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="f212b-262">スイッチ マッピング ディクショナリ キーの規則:</span><span class="sxs-lookup"><span data-stu-id="f212b-262">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="f212b-263">スイッチはダッシュ (`-`) または二重ダッシュ (`--`) で開始する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-263">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="f212b-264">スイッチ マッピング ディクショナリに重複キーを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="f212b-264">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="f212b-265">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-265">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-266">前の例で示したように、スイッチ マッピングを使用する場合は `CreateDefaultBuilder` への呼び出しが引数を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="f212b-266">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="f212b-267">`CreateDefaultBuilder` メソッドの `AddCommandLine` の呼び出しにはマップされたスイッチが含まれないため、スイッチ マッピング ディクショナリを `CreateDefaultBuilder` に渡す方法はありません。</span><span class="sxs-lookup"><span data-stu-id="f212b-267">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="f212b-268">引数にマップされたスイッチが含まれており、それが `CreateDefaultBuilder` に渡される場合は、その `AddCommandLine` プロバイダーは <xref:System.FormatException> で初期化に失敗します。</span><span class="sxs-lookup"><span data-stu-id="f212b-268">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="f212b-269">ソリューションでは `CreateDefaultBuilder` に引数を渡す代わりに、`ConfigurationBuilder` メソッドの `AddCommandLine` メソッドに、引数とスイッチ マッピング ディクショナリの両方を処理させることができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-269">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="f212b-270">スイッチ マッピング ディクショナリが作成されると、以下の表に示すデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-270">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="f212b-271">キー</span><span class="sxs-lookup"><span data-stu-id="f212b-271">Key</span></span>       | <span data-ttu-id="f212b-272">[値]</span><span class="sxs-lookup"><span data-stu-id="f212b-272">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="f212b-273">アプリの起動時にスイッチ マッピングされたキーを使用する場合、構成は、ディクショナリによって指定されたキーでの構成値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f212b-273">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="f212b-274">上記のコマンドを実行すると、次の表に示す値が構成に含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-274">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="f212b-275">キー</span><span class="sxs-lookup"><span data-stu-id="f212b-275">Key</span></span>               | <span data-ttu-id="f212b-276">[値]</span><span class="sxs-lookup"><span data-stu-id="f212b-276">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="f212b-277">環境変数構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-277">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="f212b-278"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> では、実行時に環境変数のキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-278">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="f212b-279">環境変数の構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-279">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f212b-280">環境変数内で階層キーを操作する場合、コロン区切り (`:`) がすべてのプラットフォームでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-280">When working with hierarchical keys in environment variables, a colon separator (`:`) may not work on all platforms.</span></span> <span data-ttu-id="f212b-281">二重のアンダースコア (`__`) はすべてのプラットフォームでサポートされ、コロンに置換されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-281">A double underscore (`__`) is supported by all platforms and is replaced by a colon.</span></span>

<span data-ttu-id="f212b-282">[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、環境変数構成プロバイダーを使用してアプリの構成をオーバーライドすることができる環境変数を、Azure Portal で設定できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-282">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="f212b-283">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-283">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="f212b-284">新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を初期化すると、`ASPNETCORE_` というプレフィックスが付いた環境変数に対して自動的に `AddEnvironmentVariables` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-284">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="f212b-285">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-285">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="f212b-286">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-286">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f212b-287">プレフィックスなしの `AddEnvironmentVariables` 呼び出しによる、プレフィックスの付いていない環境変数からのアプリの構成。</span><span class="sxs-lookup"><span data-stu-id="f212b-287">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="f212b-288">*appsettings.json* および *appsettings.{Environment}.json* からのオプションの構成。</span><span class="sxs-lookup"><span data-stu-id="f212b-288">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="f212b-289">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合)。</span><span class="sxs-lookup"><span data-stu-id="f212b-289">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="f212b-290">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="f212b-290">Command-line arguments.</span></span>

<span data-ttu-id="f212b-291">ユーザー シークレットと *appsettings* ファイルから構成が設定された後に、環境変数構成プロバイダーが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-291">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="f212b-292">この位置でプロバイダーを呼び出すことにより、実行時に読み込まれた環境変数が、ユーザー シークレットと *appsettings* ファイルによって設定された構成をオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-292">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="f212b-293">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-293">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="f212b-294">追加の環境変数からアプリの構成を指定する必要がある場合は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> のアプリの追加プロバイダーを呼び出し、そのプレフィックスを含む `AddEnvironmentVariables` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-294">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow environment
                // variables to override values from other providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-295"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-295">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f212b-296">**例**</span><span class="sxs-lookup"><span data-stu-id="f212b-296">**Example**</span></span>

<span data-ttu-id="f212b-297">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddEnvironmentVariables` の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-297">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="f212b-298">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f212b-298">Run the sample app.</span></span> <span data-ttu-id="f212b-299">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="f212b-299">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f212b-300">出力に、環境変数 `ENVIRONMENT` のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="f212b-300">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="f212b-301">値には、アプリを実行している環境が反映されます (ローカルで実行している場合は通常 `Development`)。</span><span class="sxs-lookup"><span data-stu-id="f212b-301">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="f212b-302">アプリによって表示される環境変数のリストを短くするために、アプリでは次で始まる環境変数がフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-302">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="f212b-303">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="f212b-303">ASPNETCORE_</span></span>
* <span data-ttu-id="f212b-304">urls</span><span class="sxs-lookup"><span data-stu-id="f212b-304">urls</span></span>
* <span data-ttu-id="f212b-305">ログの記録</span><span class="sxs-lookup"><span data-stu-id="f212b-305">Logging</span></span>
* <span data-ttu-id="f212b-306">ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="f212b-306">ENVIRONMENT</span></span>
* <span data-ttu-id="f212b-307">contentRoot</span><span class="sxs-lookup"><span data-stu-id="f212b-307">contentRoot</span></span>
* <span data-ttu-id="f212b-308">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="f212b-308">AllowedHosts</span></span>
* <span data-ttu-id="f212b-309">applicationName</span><span class="sxs-lookup"><span data-stu-id="f212b-309">applicationName</span></span>
* <span data-ttu-id="f212b-310">CommandLine</span><span class="sxs-lookup"><span data-stu-id="f212b-310">CommandLine</span></span>

<span data-ttu-id="f212b-311">アプリで使用できるすべての環境変数を公開する場合は、*Pages/Index.cshtml.cs* の `FilteredConfiguration` を次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="f212b-311">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="f212b-312">プレフィックス</span><span class="sxs-lookup"><span data-stu-id="f212b-312">Prefixes</span></span>

<span data-ttu-id="f212b-313">アプリの構成に読み込まれる環境変数は、`AddEnvironmentVariables` メソッドにプレフィックスを指定すると、フィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-313">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="f212b-314">たとえば、プレフィックス `CUSTOM_` で環境変数をフィルター処理するには、構成プロバイダーにプレフィックスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-314">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="f212b-315">構成のキーと値のペアが作成されるときに、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-315">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="f212b-316">静的な簡易メソッド `CreateDefaultBuilder` によって <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> が作成され、アプリのホストが確立されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-316">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="f212b-317">`WebHostBuilder` は、作成されるときに、`ASPNETCORE_` というプレフィックスが付いた環境変数からホストの構成を見つけます。</span><span class="sxs-lookup"><span data-stu-id="f212b-317">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="f212b-318">**接続文字列のプレフィックス**</span><span class="sxs-lookup"><span data-stu-id="f212b-318">**Connection string prefixes**</span></span>

<span data-ttu-id="f212b-319">構成 API には、アプリの環境に向けた Azure の接続文字列の構成に関係する、4 つの接続文字列環境変数のための特別な処理規則があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-319">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="f212b-320">表に示されるプレフィックスを含む環境変数は、`AddEnvironmentVariables` にプレフィックスが指定されていない場合、アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-320">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="f212b-321">接続文字列のプレフィックス</span><span class="sxs-lookup"><span data-stu-id="f212b-321">Connection string prefix</span></span> | <span data-ttu-id="f212b-322">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-322">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="f212b-323">カスタム プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-323">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="f212b-324">MySQL</span><span class="sxs-lookup"><span data-stu-id="f212b-324">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="f212b-325">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="f212b-325">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="f212b-326">SQL Server</span><span class="sxs-lookup"><span data-stu-id="f212b-326">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="f212b-327">表に示す 4 つのプレフィックスのいずれかを使用して、環境変数が検出され構成に読み込まれた場合:</span><span class="sxs-lookup"><span data-stu-id="f212b-327">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="f212b-328">環境変数のプレフィックスを削除し、構成キーのセクション (`ConnectionStrings`) を追加することによって、構成キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-328">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="f212b-329">データベースの接続プロバイダーを表す新しい構成のキーと値のペアが作成されます (示されたプロバイダーを含まない `CUSTOMCONNSTR_` を除く)。</span><span class="sxs-lookup"><span data-stu-id="f212b-329">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="f212b-330">環境変数キー</span><span class="sxs-lookup"><span data-stu-id="f212b-330">Environment variable key</span></span> | <span data-ttu-id="f212b-331">変換された構成キー</span><span class="sxs-lookup"><span data-stu-id="f212b-331">Converted configuration key</span></span> | <span data-ttu-id="f212b-332">プロバイダーの構成エントリ</span><span class="sxs-lookup"><span data-stu-id="f212b-332">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f212b-333">構成エントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="f212b-333">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f212b-334">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f212b-334">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="f212b-335">値: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="f212b-335">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f212b-336">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f212b-336">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="f212b-337">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f212b-337">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="f212b-338">キー: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="f212b-338">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="f212b-339">値: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="f212b-339">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="f212b-340">ファイル構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-340">File Configuration Provider</span></span>

<span data-ttu-id="f212b-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> は、ファイル システムから構成を読み込むための基本クラスです。</span><span class="sxs-lookup"><span data-stu-id="f212b-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="f212b-342">次の構成プロバイダーは、特定のファイルの種類専用です。</span><span class="sxs-lookup"><span data-stu-id="f212b-342">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="f212b-343">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-343">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="f212b-344">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-344">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="f212b-345">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-345">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="f212b-346">INI 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-346">INI Configuration Provider</span></span>

<span data-ttu-id="f212b-347"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> では、実行時に INI ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-347">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="f212b-348">INI ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-348">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f212b-349">INI ファイルの構成では、セクションの区切り記号としてコロンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-349">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="f212b-350">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-350">Overloads permit specifying:</span></span>

* <span data-ttu-id="f212b-351">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="f212b-351">Whether the file is optional.</span></span>
* <span data-ttu-id="f212b-352">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="f212b-352">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f212b-353">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-353">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f212b-354">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-354">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile("config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-355">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-355">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f212b-356">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-356">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-357"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-357">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f212b-358">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-358">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f212b-359">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-359">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-360">INI 構成ファイルの汎用的な例:</span><span class="sxs-lookup"><span data-stu-id="f212b-360">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="f212b-361">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-361">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f212b-362">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-362">section0:key0</span></span>
* <span data-ttu-id="f212b-363">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-363">section0:key1</span></span>
* <span data-ttu-id="f212b-364">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="f212b-364">section1:subsection:key</span></span>
* <span data-ttu-id="f212b-365">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="f212b-365">section2:subsection0:key</span></span>
* <span data-ttu-id="f212b-366">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="f212b-366">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="f212b-367">JSON 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-367">JSON Configuration Provider</span></span>

<span data-ttu-id="f212b-368"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> では、実行時に JSON ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-368">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="f212b-369">JSON ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-369">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f212b-370">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-370">Overloads permit specifying:</span></span>

* <span data-ttu-id="f212b-371">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="f212b-371">Whether the file is optional.</span></span>
* <span data-ttu-id="f212b-372">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="f212b-372">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f212b-373">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-373">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f212b-374"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> で新しい <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を初期化すると、自動的に `AddJsonFile` が 2 回呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-374">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="f212b-375">このメソッドは、次から構成を読み込むために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-375">The method is called to load configuration from:</span></span>

* <span data-ttu-id="f212b-376">*appsettings.json* &ndash; このファイルが最初に読み取られます。</span><span class="sxs-lookup"><span data-stu-id="f212b-376">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="f212b-377">ファイルの環境バージョンは、*appsettings.json* ファイルによって指定される値をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="f212b-377">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="f212b-378">*appsettings.{Environment}.json* &ndash; ファイルの環境バージョンは、[IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) に基づいて読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-378">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="f212b-379">詳細については、「[Web ホスト: ホストを設定する](xref:fundamentals/host/web-host#set-up-a-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-379">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="f212b-380">`CreateDefaultBuilder` では次のものも読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-380">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="f212b-381">環境変数。</span><span class="sxs-lookup"><span data-stu-id="f212b-381">Environment variables.</span></span>
* <span data-ttu-id="f212b-382">[ユーザー シークレット (Secret Manager)](xref:security/app-secrets) (開発環境の場合)。</span><span class="sxs-lookup"><span data-stu-id="f212b-382">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="f212b-383">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="f212b-383">Command-line arguments.</span></span>

<span data-ttu-id="f212b-384">JSON 構成プロバイダーが最初に確立されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-384">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="f212b-385">このため、ユーザー シークレット、環境変数、およびコマンド ライン引数によって、*appsettings* ファイルによって設定された構成がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="f212b-385">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="f212b-386">ホストのビルド時に <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、*appsettings.json* と *appsettings.{Environment}.json* 以外のファイルにアプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-386">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile("config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-387">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-387">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f212b-388">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-388">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-389"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-389">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f212b-390">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-390">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f212b-391">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-391">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-392">**例**</span><span class="sxs-lookup"><span data-stu-id="f212b-392">**Example**</span></span>

<span data-ttu-id="f212b-393">サンプル アプリでは、静的な簡易メソッド `CreateDefaultBuilder` を利用してホストをビルドします。これには `AddJsonFile` の 2 回の呼び出しが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-393">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="f212b-394">構成は、*appsettings.json* および *appsettings.{Environment}.json* から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-394">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="f212b-395">サンプル アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f212b-395">Run the sample app.</span></span> <span data-ttu-id="f212b-396">アプリに対して `http://localhost:5000` でブラウザーを開きます。</span><span class="sxs-lookup"><span data-stu-id="f212b-396">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="f212b-397">出力に、環境に応じて、表に示す構成のキーと値のペアが含まれていることを観察します。</span><span class="sxs-lookup"><span data-stu-id="f212b-397">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="f212b-398">ログの構成キーでは、階層の区切り文字としてコロン (`:`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-398">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="f212b-399">キー</span><span class="sxs-lookup"><span data-stu-id="f212b-399">Key</span></span>                        | <span data-ttu-id="f212b-400">開発時の値</span><span class="sxs-lookup"><span data-stu-id="f212b-400">Development Value</span></span> | <span data-ttu-id="f212b-401">運用時の値</span><span class="sxs-lookup"><span data-stu-id="f212b-401">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="f212b-402">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="f212b-402">Logging:LogLevel:System</span></span>    | <span data-ttu-id="f212b-403">情報</span><span class="sxs-lookup"><span data-stu-id="f212b-403">Information</span></span>       | <span data-ttu-id="f212b-404">情報</span><span class="sxs-lookup"><span data-stu-id="f212b-404">Information</span></span>      |
| <span data-ttu-id="f212b-405">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="f212b-405">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="f212b-406">情報</span><span class="sxs-lookup"><span data-stu-id="f212b-406">Information</span></span>       | <span data-ttu-id="f212b-407">情報</span><span class="sxs-lookup"><span data-stu-id="f212b-407">Information</span></span>      |
| <span data-ttu-id="f212b-408">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="f212b-408">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="f212b-409">デバッグ</span><span class="sxs-lookup"><span data-stu-id="f212b-409">Debug</span></span>             | <span data-ttu-id="f212b-410">Error</span><span class="sxs-lookup"><span data-stu-id="f212b-410">Error</span></span>            |
| <span data-ttu-id="f212b-411">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="f212b-411">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="f212b-412">XML 構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-412">XML Configuration Provider</span></span>

<span data-ttu-id="f212b-413"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> では、実行時に XML ファイルのキーと値のペアから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-413">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="f212b-414">XML ファイルの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-414">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f212b-415">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-415">Overloads permit specifying:</span></span>

* <span data-ttu-id="f212b-416">ファイルを省略可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="f212b-416">Whether the file is optional.</span></span>
* <span data-ttu-id="f212b-417">ファイルが変更された場合に構成を再度読み込むかどうか。</span><span class="sxs-lookup"><span data-stu-id="f212b-417">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="f212b-418">ファイルにアクセスするために <xref:Microsoft.Extensions.FileProviders.IFileProvider> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-418">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="f212b-419">構成ファイルのルート ノードは、構成のキーと値のペアを作成するときには無視されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-419">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="f212b-420">ファイル内でドキュメント型定義 (DTD) または名前空間を指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="f212b-420">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="f212b-421">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-421">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile("config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-422">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-422">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f212b-423">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-423">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-424"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-424">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="f212b-425">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-425">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f212b-426">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-426">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-427">XML 構成ファイルでは、繰り返しのセクション用に個別の要素名を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-427">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="f212b-428">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-428">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f212b-429">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-429">section0:key0</span></span>
* <span data-ttu-id="f212b-430">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-430">section0:key1</span></span>
* <span data-ttu-id="f212b-431">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-431">section1:key0</span></span>
* <span data-ttu-id="f212b-432">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-432">section1:key1</span></span>

<span data-ttu-id="f212b-433">同じ要素名を使用する要素の繰り返しは、要素を区別するために `name` 属性を使用する場合に機能します。</span><span class="sxs-lookup"><span data-stu-id="f212b-433">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="f212b-434">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-434">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f212b-435">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-435">section:section0:key:key0</span></span>
* <span data-ttu-id="f212b-436">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-436">section:section0:key:key1</span></span>
* <span data-ttu-id="f212b-437">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-437">section:section1:key:key0</span></span>
* <span data-ttu-id="f212b-438">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-438">section:section1:key:key1</span></span>

<span data-ttu-id="f212b-439">値を指定するために属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-439">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="f212b-440">前の構成ファイルでは、`value` を使用して次のキーが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="f212b-441">key:attribute</span><span class="sxs-lookup"><span data-stu-id="f212b-441">key:attribute</span></span>
* <span data-ttu-id="f212b-442">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="f212b-442">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="f212b-443">ファイルごとのキーの構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-443">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="f212b-444"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> では、構成のキーと値のペアとしてディレクトリのファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-444">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="f212b-445">キーはファイル名です。</span><span class="sxs-lookup"><span data-stu-id="f212b-445">The key is the file name.</span></span> <span data-ttu-id="f212b-446">値にはファイルのコンテンツが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-446">The value contains the file's contents.</span></span> <span data-ttu-id="f212b-447">ファイルごとのキーの構成プロバイダーは、Docker ホスティングのシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-447">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="f212b-448">ファイルごとのキーの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-448">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="f212b-449">ファイルに対する `directoryPath` は、絶対パスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="f212b-449">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="f212b-450">オーバーロードによって次のものを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="f212b-451">ソースを構成する `Action<KeyPerFileConfigurationSource>` デリゲート。</span><span class="sxs-lookup"><span data-stu-id="f212b-451">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="f212b-452">ディレクトリを省略可能かどうか、またディレクトリへのパス。</span><span class="sxs-lookup"><span data-stu-id="f212b-452">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="f212b-453">アンダースコア 2 つ (`__`) は、ファイル名で構成キーの区切り記号として使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-453">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="f212b-454">たとえば、ファイル名 `Logging__LogLevel__System` では、構成キー `Logging:LogLevel:System` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-454">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="f212b-455">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-455">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-456">基本パスは <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> に設定されています。</span><span class="sxs-lookup"><span data-stu-id="f212b-456">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="f212b-457">`SetBasePath` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-457">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-458"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-458">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="f212b-459">メモリ構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-459">Memory Configuration Provider</span></span>

<span data-ttu-id="f212b-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> では、構成のキーと値のペアとして、メモリ内コレクションが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="f212b-461">メモリ内コレクションの構成をアクティブにするには、<xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> のインスタンスの <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="f212b-462">構成プロバイダーは、`IEnumerable<KeyValuePair<String,String>>` を使用して初期化できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="f212b-463">ホストをビルドするときに <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出して、アプリの構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-463">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="f212b-464"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> を直接作成する場合、次の構成で <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-464">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a><span data-ttu-id="f212b-465">GetValue</span><span class="sxs-lookup"><span data-stu-id="f212b-465">GetValue</span></span>

<span data-ttu-id="f212b-466">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) によって、指定したキーで構成から値が抽出され、それが指定した型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-466">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="f212b-467">オーバーロードを使用すると、キーが見つからない場合に既定値を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-467">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="f212b-468">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="f212b-468">The following example:</span></span>

* <span data-ttu-id="f212b-469">キー `NumberKey` の文字列値を構成から抽出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-469">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="f212b-470">`NumberKey` が構成キーに見つからない場合、既定値の `99` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-470">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="f212b-471">値の型は `int` です。</span><span class="sxs-lookup"><span data-stu-id="f212b-471">Types the value as an `int`.</span></span>
* <span data-ttu-id="f212b-472">`NumberConfig` プロパティの値をページで使用するために格納します。</span><span class="sxs-lookup"><span data-stu-id="f212b-472">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="f212b-473">GetSection、GetChildren、Exists</span><span class="sxs-lookup"><span data-stu-id="f212b-473">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="f212b-474">以下の例では、次の JSON ファイルについて考えます。</span><span class="sxs-lookup"><span data-stu-id="f212b-474">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="f212b-475">4 つのキーが 2 つのセクションに含まれています。そのうちの 1 つには、一組のサブセクションが含まれています。</span><span class="sxs-lookup"><span data-stu-id="f212b-475">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="f212b-476">ファイルが構成に読み取られると、次の一意の階層キーが作成され、構成値が保持されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-476">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="f212b-477">section0:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-477">section0:key0</span></span>
* <span data-ttu-id="f212b-478">section0:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-478">section0:key1</span></span>
* <span data-ttu-id="f212b-479">section1:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-479">section1:key0</span></span>
* <span data-ttu-id="f212b-480">section1:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-480">section1:key1</span></span>
* <span data-ttu-id="f212b-481">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-481">section2:subsection0:key0</span></span>
* <span data-ttu-id="f212b-482">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-482">section2:subsection0:key1</span></span>
* <span data-ttu-id="f212b-483">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="f212b-483">section2:subsection1:key0</span></span>
* <span data-ttu-id="f212b-484">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="f212b-484">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="f212b-485">GetSection</span><span class="sxs-lookup"><span data-stu-id="f212b-485">GetSection</span></span>

<span data-ttu-id="f212b-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) では、指定したサブセクションのキーを持つ構成のサブセクションが抽出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="f212b-487">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-487">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-488">`section1` のキーと値のペアのみを含む <xref:Microsoft.Extensions.Configuration.IConfigurationSection> を返すには、`GetSection` を呼び出してセクション名を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="f212b-489">`configSection` には値はなく、キーとパスのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="f212b-490">同様に、`section2:subsection0` のキーの値を取得するには、`GetSection` を呼び出してセクションのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="f212b-491">`GetSection` が `null` を返すことはありません。</span><span class="sxs-lookup"><span data-stu-id="f212b-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="f212b-492">一致するセクションが見つからない場合は、空の `IConfigurationSection` が返されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="f212b-493">`GetSection` で一致するセクションが返されると、<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> は設定されません。</span><span class="sxs-lookup"><span data-stu-id="f212b-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="f212b-494"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> と <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> はセクションが存在する場合に返されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="f212b-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="f212b-495">GetChildren</span></span>

<span data-ttu-id="f212b-496">`section2` での [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) の呼び出しでは、次を含む `IEnumerable<IConfigurationSection>` が取得されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="f212b-497">既存</span><span class="sxs-lookup"><span data-stu-id="f212b-497">Exists</span></span>

<span data-ttu-id="f212b-498">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) を使用して、構成セクションが存在するかどうかを判断します。</span><span class="sxs-lookup"><span data-stu-id="f212b-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="f212b-499">例のデータの場合、構成データに `section2:subsection2` セクションが存在しないため、`sectionExists` は `false` となります。</span><span class="sxs-lookup"><span data-stu-id="f212b-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="f212b-500">クラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="f212b-500">Bind to a class</span></span>

<span data-ttu-id="f212b-501">"*オプション パターン*" を使用して、関連する設定のグループを表すクラスに構成をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="f212b-502">詳細については、「<xref:fundamentals/configuration/options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="f212b-503">構成値は文字列として返されますが、<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> を呼び出すことで [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) オブジェクトを構築できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="f212b-504">`Bind` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-504">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-505">サンプル アプリには `Starship` モデル (*Models/Starship.cs*) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-505">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="f212b-506">サンプル アプリが JSON 構成プロバイダーを使用して構成を読み込むときに、*starship.json* ファイルの `starship` セクションで構成が作成されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-506">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="f212b-507">次の構成のキーと値のペアが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-507">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="f212b-508">キー</span><span class="sxs-lookup"><span data-stu-id="f212b-508">Key</span></span>                   | <span data-ttu-id="f212b-509">[値]</span><span class="sxs-lookup"><span data-stu-id="f212b-509">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="f212b-510">starship:name</span><span class="sxs-lookup"><span data-stu-id="f212b-510">starship:name</span></span>         | <span data-ttu-id="f212b-511">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="f212b-511">USS Enterprise</span></span>                                    |
| <span data-ttu-id="f212b-512">starship:registry</span><span class="sxs-lookup"><span data-stu-id="f212b-512">starship:registry</span></span>     | <span data-ttu-id="f212b-513">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="f212b-513">NCC-1701</span></span>                                          |
| <span data-ttu-id="f212b-514">starship:class</span><span class="sxs-lookup"><span data-stu-id="f212b-514">starship:class</span></span>        | <span data-ttu-id="f212b-515">Constitution</span><span class="sxs-lookup"><span data-stu-id="f212b-515">Constitution</span></span>                                      |
| <span data-ttu-id="f212b-516">starship:length</span><span class="sxs-lookup"><span data-stu-id="f212b-516">starship:length</span></span>       | <span data-ttu-id="f212b-517">304.8</span><span class="sxs-lookup"><span data-stu-id="f212b-517">304.8</span></span>                                             |
| <span data-ttu-id="f212b-518">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="f212b-518">starship:commissioned</span></span> | <span data-ttu-id="f212b-519">False</span><span class="sxs-lookup"><span data-stu-id="f212b-519">False</span></span>                                             |
| <span data-ttu-id="f212b-520">trademark</span><span class="sxs-lookup"><span data-stu-id="f212b-520">trademark</span></span>             | <span data-ttu-id="f212b-521">Paramount Pictures Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="f212b-521">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="f212b-522">サンプル アプリは `starship` キーを使用して `GetSection` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f212b-522">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="f212b-523">`starship` のキーと値のペアは分離されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-523">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="f212b-524">`Starship` クラスのインスタンスを渡すサブセクションで、`Bind` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-524">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="f212b-525">インスタンスの値をバインドした後、インスタンスがレンダリング用のプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="f212b-525">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="f212b-526">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-526">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="f212b-527">オブジェクト グラフにバインドする</span><span class="sxs-lookup"><span data-stu-id="f212b-527">Bind to an object graph</span></span>

<span data-ttu-id="f212b-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> では、POCO オブジェクト グラフ全体をバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="f212b-529">`Bind` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-529">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-530">サンプルには、オブジェクト グラフに `Metadata` クラスと `Actors` クラスが含まれる `TvShow` モデルが含まれます (*Models/TvShow.cs*)。</span><span class="sxs-lookup"><span data-stu-id="f212b-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="f212b-531">サンプル アプリには、構成データを含む *tvshow.xml* ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="f212b-532">構成は、`Bind` メソッドを使用して、`TvShow` オブジェクト グラフ全体にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="f212b-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="f212b-533">バインドされたインスタンスは、表示のためにプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="f212b-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="f212b-534">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) によってバインドされ、指定した型が返されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-534">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="f212b-535">`Get<T>` は `Bind` を使用するよりも便利です。</span><span class="sxs-lookup"><span data-stu-id="f212b-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="f212b-536">次のコードは、前の例と共に `Get<T>` を使用する方法を示しています。これにより、バインドされたインスタンスを、表示用に使用するプロパティに直接割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="f212b-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="f212b-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f212b-538">`Get<T>` は、ASP.NET Core 1.1 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-538">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f212b-539">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-539">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="f212b-540">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="f212b-540">Bind an array to a class</span></span>

<span data-ttu-id="f212b-541">*サンプル アプリは、このセクションで説明する概念を示しています。*</span><span class="sxs-lookup"><span data-stu-id="f212b-541">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="f212b-542"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> は、構成キーで配列インデックスを使用して、オブジェクトに対する配列のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="f212b-542">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="f212b-543">数値のキー セグメント (`:0:`、`:1:`、&hellip; `:{n}:`) を公開する配列形式は、すべて POCO クラスの配列にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="f212b-543">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="f212b-544">`Bind` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-544">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="f212b-545">バインドは慣例に従って指定されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-545">Binding is provided by convention.</span></span> <span data-ttu-id="f212b-546">カスタム構成プロバイダーが配列のバインドを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f212b-546">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="f212b-547">**メモリ内配列の処理**</span><span class="sxs-lookup"><span data-stu-id="f212b-547">**In-memory array processing**</span></span>

<span data-ttu-id="f212b-548">次の表に示す構成のキーと値について考えます。</span><span class="sxs-lookup"><span data-stu-id="f212b-548">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="f212b-549">キー</span><span class="sxs-lookup"><span data-stu-id="f212b-549">Key</span></span>             | <span data-ttu-id="f212b-550">[値]</span><span class="sxs-lookup"><span data-stu-id="f212b-550">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="f212b-551">配列:エントリ:0</span><span class="sxs-lookup"><span data-stu-id="f212b-551">array:entries:0</span></span> | <span data-ttu-id="f212b-552">value0</span><span class="sxs-lookup"><span data-stu-id="f212b-552">value0</span></span> |
| <span data-ttu-id="f212b-553">配列:エントリ:1</span><span class="sxs-lookup"><span data-stu-id="f212b-553">array:entries:1</span></span> | <span data-ttu-id="f212b-554">value1</span><span class="sxs-lookup"><span data-stu-id="f212b-554">value1</span></span> |
| <span data-ttu-id="f212b-555">配列:エントリ:2</span><span class="sxs-lookup"><span data-stu-id="f212b-555">array:entries:2</span></span> | <span data-ttu-id="f212b-556">value2</span><span class="sxs-lookup"><span data-stu-id="f212b-556">value2</span></span> |
| <span data-ttu-id="f212b-557">配列:エントリ:4</span><span class="sxs-lookup"><span data-stu-id="f212b-557">array:entries:4</span></span> | <span data-ttu-id="f212b-558">value4</span><span class="sxs-lookup"><span data-stu-id="f212b-558">value4</span></span> |
| <span data-ttu-id="f212b-559">配列:エントリ:5</span><span class="sxs-lookup"><span data-stu-id="f212b-559">array:entries:5</span></span> | <span data-ttu-id="f212b-560">value5</span><span class="sxs-lookup"><span data-stu-id="f212b-560">value5</span></span> |

<span data-ttu-id="f212b-561">これらのキーと値は、メモリ構成プロバイダーを使用してサンプル アプリに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-561">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

<span data-ttu-id="f212b-562">配列は、インデックス &num;3 の値をスキップします。</span><span class="sxs-lookup"><span data-stu-id="f212b-562">The array skips a value for index &num;3.</span></span> <span data-ttu-id="f212b-563">構成バインダーは、null 値をバインドしたり、バインドされたオブジェクトに null エントリを作成したりすることはできません。このことは、この配列をオブジェクトにバインドした結果によって明らかになります。</span><span class="sxs-lookup"><span data-stu-id="f212b-563">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="f212b-564">サンプル アプリでは、バインドされた構成データを保持するために POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-564">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="f212b-565">構成データはオブジェクトにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="f212b-565">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="f212b-566">`GetSection` は [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)内の [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) パッケージにあります。</span><span class="sxs-lookup"><span data-stu-id="f212b-566">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f212b-567">また、[ConfigurationBinder.Get&lt;T&gt; ](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 構文を使用して、コードをよりコンパクトにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f212b-567">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="f212b-568">バインドされたオブジェクト (`ArrayExample` のインスタンス) は、構成から配列データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f212b-568">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="f212b-569">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="f212b-569">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="f212b-570">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="f212b-570">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="f212b-571">0</span><span class="sxs-lookup"><span data-stu-id="f212b-571">0</span></span>                            | <span data-ttu-id="f212b-572">value0</span><span class="sxs-lookup"><span data-stu-id="f212b-572">value0</span></span>                       |
| <span data-ttu-id="f212b-573">1</span><span class="sxs-lookup"><span data-stu-id="f212b-573">1</span></span>                            | <span data-ttu-id="f212b-574">value1</span><span class="sxs-lookup"><span data-stu-id="f212b-574">value1</span></span>                       |
| <span data-ttu-id="f212b-575">2</span><span class="sxs-lookup"><span data-stu-id="f212b-575">2</span></span>                            | <span data-ttu-id="f212b-576">value2</span><span class="sxs-lookup"><span data-stu-id="f212b-576">value2</span></span>                       |
| <span data-ttu-id="f212b-577">3</span><span class="sxs-lookup"><span data-stu-id="f212b-577">3</span></span>                            | <span data-ttu-id="f212b-578">value4</span><span class="sxs-lookup"><span data-stu-id="f212b-578">value4</span></span>                       |
| <span data-ttu-id="f212b-579">4</span><span class="sxs-lookup"><span data-stu-id="f212b-579">4</span></span>                            | <span data-ttu-id="f212b-580">value5</span><span class="sxs-lookup"><span data-stu-id="f212b-580">value5</span></span>                       |

<span data-ttu-id="f212b-581">バインドされたオブジェクトのインデックス &num;3 によって、`array:4` 構成キーの構成データと、その値 `value4` が保持されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-581">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="f212b-582">配列を含む構成データがバインドされると、構成キーの配列のインデックスは、オブジェクトを作成するときに構成データを反復処理するためだけに使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-582">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="f212b-583">構成データに null 値を保持することはできません。また、構成キーの配列が 1 つまたは複数のインデックスをスキップしても、バインドされたオブジェクトに null 値のエントリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="f212b-583">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="f212b-584">インデックス &num;3 の不足している構成項目は、`ArrayExample` インスタンスにバインドする前に、構成で適切なキーと値のペアを生成する構成プロバイダーによって指定できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-584">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="f212b-585">不足しているキーと値のペアを含む JSON 構成プロバイダーがサンプルに含まれる場合、`ArrayExample.Entries` は完全な構成の配列と一致します。</span><span class="sxs-lookup"><span data-stu-id="f212b-585">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="f212b-586">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="f212b-586">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="f212b-587"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>の場合:</span><span class="sxs-lookup"><span data-stu-id="f212b-587">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="f212b-588">表に示すキーと値のペアが構成に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-588">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="f212b-589">キー</span><span class="sxs-lookup"><span data-stu-id="f212b-589">Key</span></span>             | <span data-ttu-id="f212b-590">[値]</span><span class="sxs-lookup"><span data-stu-id="f212b-590">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="f212b-591">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="f212b-591">array:entries:3</span></span> | <span data-ttu-id="f212b-592">value3</span><span class="sxs-lookup"><span data-stu-id="f212b-592">value3</span></span> |

<span data-ttu-id="f212b-593">JSON 構成プロバイダーにインデックス &num;3 のエントリが含まれた後に `ArrayExample` クラスのインスタンスがバインドされる場合、`ArrayExample.Entries` 配列に値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f212b-593">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="f212b-594">`ArrayExample.Entries` インデックス</span><span class="sxs-lookup"><span data-stu-id="f212b-594">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="f212b-595">`ArrayExample.Entries` 値</span><span class="sxs-lookup"><span data-stu-id="f212b-595">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="f212b-596">0</span><span class="sxs-lookup"><span data-stu-id="f212b-596">0</span></span>                            | <span data-ttu-id="f212b-597">value0</span><span class="sxs-lookup"><span data-stu-id="f212b-597">value0</span></span>                       |
| <span data-ttu-id="f212b-598">1</span><span class="sxs-lookup"><span data-stu-id="f212b-598">1</span></span>                            | <span data-ttu-id="f212b-599">value1</span><span class="sxs-lookup"><span data-stu-id="f212b-599">value1</span></span>                       |
| <span data-ttu-id="f212b-600">2</span><span class="sxs-lookup"><span data-stu-id="f212b-600">2</span></span>                            | <span data-ttu-id="f212b-601">value2</span><span class="sxs-lookup"><span data-stu-id="f212b-601">value2</span></span>                       |
| <span data-ttu-id="f212b-602">3</span><span class="sxs-lookup"><span data-stu-id="f212b-602">3</span></span>                            | <span data-ttu-id="f212b-603">value3</span><span class="sxs-lookup"><span data-stu-id="f212b-603">value3</span></span>                       |
| <span data-ttu-id="f212b-604">4</span><span class="sxs-lookup"><span data-stu-id="f212b-604">4</span></span>                            | <span data-ttu-id="f212b-605">value4</span><span class="sxs-lookup"><span data-stu-id="f212b-605">value4</span></span>                       |
| <span data-ttu-id="f212b-606">5</span><span class="sxs-lookup"><span data-stu-id="f212b-606">5</span></span>                            | <span data-ttu-id="f212b-607">value5</span><span class="sxs-lookup"><span data-stu-id="f212b-607">value5</span></span>                       |

<span data-ttu-id="f212b-608">**JSON 配列の処理**</span><span class="sxs-lookup"><span data-stu-id="f212b-608">**JSON array processing**</span></span>

<span data-ttu-id="f212b-609">JSON ファイルに配列が含まれる場合、配列要素の構成キーは、0 から始まるセクションのインデックスで作成されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-609">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="f212b-610">次の構成ファイルにおいて、`subsection` は配列です。</span><span class="sxs-lookup"><span data-stu-id="f212b-610">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="f212b-611">JSON 構成プロバイダーは、次のキーと値のペアに構成データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="f212b-611">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="f212b-612">キー</span><span class="sxs-lookup"><span data-stu-id="f212b-612">Key</span></span>                     | <span data-ttu-id="f212b-613">[値]</span><span class="sxs-lookup"><span data-stu-id="f212b-613">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="f212b-614">json_array:key</span><span class="sxs-lookup"><span data-stu-id="f212b-614">json_array:key</span></span>          | <span data-ttu-id="f212b-615">valueA</span><span class="sxs-lookup"><span data-stu-id="f212b-615">valueA</span></span> |
| <span data-ttu-id="f212b-616">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="f212b-616">json_array:subsection:0</span></span> | <span data-ttu-id="f212b-617">valueB</span><span class="sxs-lookup"><span data-stu-id="f212b-617">valueB</span></span> |
| <span data-ttu-id="f212b-618">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="f212b-618">json_array:subsection:1</span></span> | <span data-ttu-id="f212b-619">valueC</span><span class="sxs-lookup"><span data-stu-id="f212b-619">valueC</span></span> |
| <span data-ttu-id="f212b-620">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="f212b-620">json_array:subsection:2</span></span> | <span data-ttu-id="f212b-621">valueD</span><span class="sxs-lookup"><span data-stu-id="f212b-621">valueD</span></span> |

<span data-ttu-id="f212b-622">サンプル アプリでは、構成のキーと値のペアをバインドするために、次の POCO クラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-622">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="f212b-623">バインド後、`JsonArrayExample.Key` は値 `valueA` を保持します。</span><span class="sxs-lookup"><span data-stu-id="f212b-623">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="f212b-624">サブセクションの値は、POCO 配列のプロパティ `Subsection` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-624">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="f212b-625">`JsonArrayExample.Subsection` インデックス</span><span class="sxs-lookup"><span data-stu-id="f212b-625">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="f212b-626">`JsonArrayExample.Subsection` 値</span><span class="sxs-lookup"><span data-stu-id="f212b-626">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="f212b-627">0</span><span class="sxs-lookup"><span data-stu-id="f212b-627">0</span></span>                                   | <span data-ttu-id="f212b-628">valueB</span><span class="sxs-lookup"><span data-stu-id="f212b-628">valueB</span></span>                              |
| <span data-ttu-id="f212b-629">1</span><span class="sxs-lookup"><span data-stu-id="f212b-629">1</span></span>                                   | <span data-ttu-id="f212b-630">valueC</span><span class="sxs-lookup"><span data-stu-id="f212b-630">valueC</span></span>                              |
| <span data-ttu-id="f212b-631">2</span><span class="sxs-lookup"><span data-stu-id="f212b-631">2</span></span>                                   | <span data-ttu-id="f212b-632">valueD</span><span class="sxs-lookup"><span data-stu-id="f212b-632">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="f212b-633">カスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f212b-633">Custom configuration provider</span></span>

<span data-ttu-id="f212b-634">サンプル アプリでは、[Entity Framework (EF)](/ef/core/) を使用してデータベースから構成のキーと値のペアを読み取る、基本的な構成プロバイダーを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="f212b-634">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="f212b-635">プロバイダーの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f212b-635">The provider has the following characteristics:</span></span>

* <span data-ttu-id="f212b-636">EF のメモリ内データベースは、デモンストレーションのために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f212b-636">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="f212b-637">接続文字列を必要とするデータベースを使用するには、第 2 の `ConfigurationBuilder` を実装して、別の構成プロバイダーからの接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="f212b-637">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="f212b-638">プロバイダーは、起動時に、構成にデータベース テーブルを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="f212b-638">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="f212b-639">プロバイダーは、キー単位でデータベースを照会しません。</span><span class="sxs-lookup"><span data-stu-id="f212b-639">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="f212b-640">変更時に再度読み込む機能は実装されていません。このため、アプリの起動後にデータベースを更新しても、アプリの構成には影響がありません。</span><span class="sxs-lookup"><span data-stu-id="f212b-640">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="f212b-641">データベースに構成値を格納するための `EFConfigurationValue` エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="f212b-641">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="f212b-642">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="f212b-642">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="f212b-643">構成した値を格納し、その値にアクセスするための `EFConfigurationContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="f212b-643">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="f212b-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="f212b-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="f212b-645">
  <xref:Microsoft.Extensions.Configuration.IConfigurationSource> を実装するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="f212b-645">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="f212b-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="f212b-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="f212b-647"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider> から継承して、カスタム構成プロバイダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="f212b-647">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="f212b-648">データベースが空だった場合、構成プロバイダーはこれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="f212b-648">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="f212b-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="f212b-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="f212b-650">`AddEFConfiguration` 拡張メソッドを使用すると、`ConfigurationBuilder` に構成ソースを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f212b-650">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="f212b-651">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="f212b-651">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="f212b-652">次のコードでは、*Program.cs* でカスタムの `EFConfigurationProvider` を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="f212b-652">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="f212b-653">起動中に構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="f212b-653">Access configuration during startup</span></span>

<span data-ttu-id="f212b-654">`Startup` コンストラクターに `IConfiguration` を挿入して、`Startup.ConfigureServices` で構成値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="f212b-654">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f212b-655">`Startup.Configure` で構成にアクセスするには、メソッドに直接 `IConfiguration` を挿入するか、コンストラクターからのインスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="f212b-655">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="f212b-656">起動時の簡易メソッドを使用して構成にアクセスする例については、[アプリ起動時の簡易メソッド](xref:fundamentals/startup#convenience-methods)に関連する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f212b-656">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="f212b-657">Razor Pages ページまたは MVC ビューで構成にアクセスする</span><span class="sxs-lookup"><span data-stu-id="f212b-657">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="f212b-658">Razor Pages ページまたは MVC ビューで構成設定にアクセスするには、[Microsoft.Extensions.Configuration 名前空間](xref:Microsoft.Extensions.Configuration)に [using ディレクティブ](xref:mvc/views/razor#using) ([C# リファレンス: using ディレクティブ](/dotnet/csharp/language-reference/keywords/using-directive)) を追加して、<xref:Microsoft.Extensions.Configuration.IConfiguration> をページまたはビューに挿入します。</span><span class="sxs-lookup"><span data-stu-id="f212b-658">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="f212b-659">Razor ページ: </span><span class="sxs-lookup"><span data-stu-id="f212b-659">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="f212b-660">MVC ビュー: </span><span class="sxs-lookup"><span data-stu-id="f212b-660">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="f212b-661">外部アセンブリから構成を追加する</span><span class="sxs-lookup"><span data-stu-id="f212b-661">Add configuration from an external assembly</span></span>

<span data-ttu-id="f212b-662"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> の実装により、アプリの `Startup` クラスの外部にある外部アセンブリから、起動時に拡張機能をアプリに追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="f212b-662">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="f212b-663">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f212b-663">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f212b-664">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f212b-664">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* [<span data-ttu-id="f212b-665">Microsoft の構成について詳しく調べる</span><span class="sxs-lookup"><span data-stu-id="f212b-665">Deep Dive into Microsoft Configuration</span></span>](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)
