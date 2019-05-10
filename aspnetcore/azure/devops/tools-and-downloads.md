---
title: ツールとダウンロード - ASP.NET Core および Azure を使用した DevOps
author: CamSoper
description: ツールおよび ASP.NET Core および Azure による開発運用に必要なダウンロード。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ae4827f735274405021e5ee539d1029b7ddb9553
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891989"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="05be6-103">ツールとダウンロード</span><span class="sxs-lookup"><span data-stu-id="05be6-103">Tools and downloads</span></span>

<span data-ttu-id="05be6-104">Azure では、いくつかのインターフェイスのプロビジョニングおよびなどのリソースを管理、 [Azure portal](https://portal.azure.com)、 [Azure CLI](/cli/azure/)、 [Azure PowerShell](/powershell/azure/overview)、 [Azure クラウドシェル](https://shell.azure.com/bash)、および Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="05be6-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="05be6-105">このガイドでは、必要最低限のアプローチを採用し、Azure Cloud Shell を減らすために必要な手順を実行可能な限りを使用します。</span><span class="sxs-lookup"><span data-stu-id="05be6-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="05be6-106">ただし、一部の Azure portal を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05be6-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05be6-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="05be6-107">Prerequisites</span></span>

<span data-ttu-id="05be6-108">次のサブスクリプションが必要です。</span><span class="sxs-lookup"><span data-stu-id="05be6-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="05be6-109">Azure&mdash;アカウントを持っていない場合[無料試用版を入手](https://azure.microsoft.com/free/)します。</span><span class="sxs-lookup"><span data-stu-id="05be6-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="05be6-110">Azure DevOps サービス&mdash;第 4 章で、DevOps の Azure サブスクリプションと組織を作成します。</span><span class="sxs-lookup"><span data-stu-id="05be6-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="05be6-111">GitHub&mdash;アカウントを持っていない場合[無料でサインアップ](https://github.com/join)します。</span><span class="sxs-lookup"><span data-stu-id="05be6-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="05be6-112">次のツールが必要です。</span><span class="sxs-lookup"><span data-stu-id="05be6-112">The following tools are required:</span></span>

* <span data-ttu-id="05be6-113">[Git](https://git-scm.com/downloads) &mdash;このガイドの Git の基本について理解をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="05be6-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="05be6-114">レビュー、 [Git に関するドキュメント](https://git-scm.com/doc)、特に[git リモート](https://git-scm.com/docs/git-remote)と[git プッシュ](https://git-scm.com/docs/git-push)します。</span><span class="sxs-lookup"><span data-stu-id="05be6-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="05be6-115">[.NET core SDK](https://www.microsoft.com/net/download/) &mdash; 2.1.300 のバージョンをビルドして、サンプル アプリの実行以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="05be6-115">[.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="05be6-116">Visual Studio がインストールされている場合、 **.NET Core クロス プラットフォーム開発**ワークロードでは、.NET Core SDK が既にインストールされています。</span><span class="sxs-lookup"><span data-stu-id="05be6-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="05be6-117">.NET Core SDK のインストールを確認します。</span><span class="sxs-lookup"><span data-stu-id="05be6-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="05be6-118">コマンド シェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="05be6-118">Open a command shell, and run the following command:</span></span>

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="05be6-119">推奨されるツール (Windows のみ)</span><span class="sxs-lookup"><span data-stu-id="05be6-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="05be6-120">[Visual Studio](https://visualstudio.microsoft.com)堅牢な Azure ツール説明 GUI このガイドで説明されている機能のほとんどの。</span><span class="sxs-lookup"><span data-stu-id="05be6-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="05be6-121">無料の Visual Studio Community Edition を含む、Visual Studio の任意のエディションは機能します。</span><span class="sxs-lookup"><span data-stu-id="05be6-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="05be6-122">チュートリアルは、Visual Studio の有無にかかわらず、開発、デプロイ、DevOps のデモンストレーションに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="05be6-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="05be6-123">Visual Studio が、次のことを確認します。[ワークロード](/visualstudio/install/modify-visual-studio)インストール。</span><span class="sxs-lookup"><span data-stu-id="05be6-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="05be6-124">ASP.NET と Web 開発</span><span class="sxs-lookup"><span data-stu-id="05be6-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="05be6-125">Azure の開発</span><span class="sxs-lookup"><span data-stu-id="05be6-125">Azure development</span></span>
  * <span data-ttu-id="05be6-126">.NET Core クロスプラットフォームの開発</span><span class="sxs-lookup"><span data-stu-id="05be6-126">.NET Core cross-platform development</span></span>
