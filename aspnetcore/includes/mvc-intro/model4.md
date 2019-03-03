<span data-ttu-id="84771-101">次の表で、ASP.NET Core コード ジェネレーターのパラメーターについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="84771-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="84771-102">パラメーター</span><span class="sxs-lookup"><span data-stu-id="84771-102">Parameter</span></span>               | <span data-ttu-id="84771-103">説明</span><span class="sxs-lookup"><span data-stu-id="84771-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="84771-104">-m</span><span class="sxs-lookup"><span data-stu-id="84771-104">-m</span></span>  | <span data-ttu-id="84771-105">モデルの名前。</span><span class="sxs-lookup"><span data-stu-id="84771-105">The name of the model.</span></span> |
| <span data-ttu-id="84771-106">-dc</span><span class="sxs-lookup"><span data-stu-id="84771-106">-dc</span></span>  | <span data-ttu-id="84771-107">データ コンテキスト。</span><span class="sxs-lookup"><span data-stu-id="84771-107">The data context.</span></span> |
| <span data-ttu-id="84771-108">-udl</span><span class="sxs-lookup"><span data-stu-id="84771-108">-udl</span></span> | <span data-ttu-id="84771-109">既定のレイアウトを使用します。</span><span class="sxs-lookup"><span data-stu-id="84771-109">Use the default layout.</span></span> |
| <span data-ttu-id="84771-110">--relativeFolderPath</span><span class="sxs-lookup"><span data-stu-id="84771-110">--relativeFolderPath</span></span> | <span data-ttu-id="84771-111">ビューを作成するための相対出力フォルダー パス。</span><span class="sxs-lookup"><span data-stu-id="84771-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="84771-112">--useDefaultLayout</span><span class="sxs-lookup"><span data-stu-id="84771-112">--useDefaultLayout</span></span> | <span data-ttu-id="84771-113">ビューには既定のレイアウトを使用してください。</span><span class="sxs-lookup"><span data-stu-id="84771-113">The default layout should be used for the views.</span></span> |
| <span data-ttu-id="84771-114">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="84771-114">--referenceScriptLibraries</span></span> | <span data-ttu-id="84771-115">[編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。</span><span class="sxs-lookup"><span data-stu-id="84771-115">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="84771-116">次のように、`h` スイッチを使用して、`aspnet-codegenerator controller` コマンドに関するヘルプを取得します。</span><span class="sxs-lookup"><span data-stu-id="84771-116">Use the `h` switch to get help on the `aspnet-codegenerator controller` command:</span></span>

```console
dotnet aspnet-codegenerator controller -h
```