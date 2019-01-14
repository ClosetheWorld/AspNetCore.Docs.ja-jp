---
title: ASP.NET Core フィルター
author: ardalis
description: フィルターのしくみと ASP.NET Core MVC でそれを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2018
uid: mvc/controllers/filters
ms.openlocfilehash: d4fe49a9225b9980a956ef9c773ad631beb557ae
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207461"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="7c201-103">ASP.NET Core フィルター</span><span class="sxs-lookup"><span data-stu-id="7c201-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="7c201-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7c201-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7c201-105">ASP.NET Core MVC で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-105">*Filters* in ASP.NET Core MVC allow you to run code before or after specific stages in the request processing pipeline.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7c201-106">このトピックは Razor ページには適用**されません**。</span><span class="sxs-lookup"><span data-stu-id="7c201-106">This topic does **not** apply to Razor Pages.</span></span> <span data-ttu-id="7c201-107">ASP.NET Core 2.1 以降では、Razor ページの [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) と [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) に対応しています。</span><span class="sxs-lookup"><span data-stu-id="7c201-107">ASP.NET Core 2.1 and later supports [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) for Razor Pages.</span></span> <span data-ttu-id="7c201-108">詳細については、[Razor ページのフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c201-108">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

 <span data-ttu-id="7c201-109">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-109">Built-in filters handle tasks such as:</span></span>

 * <span data-ttu-id="7c201-110">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="7c201-110">Authorization (preventing access to resources a user isn't authorized for).</span></span>
 * <span data-ttu-id="7c201-111">すべての要求で HTTPS を使用する。</span><span class="sxs-lookup"><span data-stu-id="7c201-111">Ensuring that all requests use HTTPS.</span></span>
 * <span data-ttu-id="7c201-112">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="7c201-112">Response caching (short-circuiting the request pipeline to return a cached response).</span></span> 

<span data-ttu-id="7c201-113">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-113">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="7c201-114">フィルターでは、アクション間のコード重複を回避できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-114">Filters can avoid duplicating code across actions.</span></span> <span data-ttu-id="7c201-115">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-115">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="7c201-116">[GitHub のサンプルを表示またはダウンロードしてください](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="7c201-116">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

## <a name="how-do-filters-work"></a><span data-ttu-id="7c201-117">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="7c201-117">How do filters work?</span></span>

<span data-ttu-id="7c201-118">*MVC のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="7c201-118">Filters run within the *MVC action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="7c201-119">フィルター パイプラインは、MVC が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-119">The filter pipeline runs after MVC selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、および MVC のアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="7c201-122">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="7c201-122">Filter types</span></span>

<span data-ttu-id="7c201-123">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-123">Each filter type is executed at a different stage in the filter pipeline.</span></span>

* <span data-ttu-id="7c201-124">[承認フィルター](#authorization-filters)は、最初に実行され、現在のユーザーが現在の要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-124">[Authorization filters](#authorization-filters) run first and are used to determine whether the current user is authorized for the current request.</span></span> <span data-ttu-id="7c201-125">要求が承認されていない場合は、パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="7c201-125">They can short-circuit the pipeline if a request is unauthorized.</span></span> 

* <span data-ttu-id="7c201-126">[リソース フィルター](#resource-filters)は、承認後、最初に要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="7c201-126">[Resource filters](#resource-filters) are the first to handle a request after authorization.</span></span>  <span data-ttu-id="7c201-127">このフィルターは、残りのフィルター パイプラインの完了前と完了後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-127">They can run code before the rest of the filter pipeline, and after the rest of the pipeline has completed.</span></span> <span data-ttu-id="7c201-128">キャッシュを実装するか、そうでない場合はパフォーマンス上の理由からフィルター パイプラインをショートサーキットする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="7c201-128">They're useful to implement caching or otherwise short-circuit the filter pipeline for performance reasons.</span></span> <span data-ttu-id="7c201-129">モデル バインドに変化が及ぶように、モデル バインドの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-129">They run before model binding, so they can influence model binding.</span></span>

* <span data-ttu-id="7c201-130">[アクション フィルター](#action-filters)は、個々のアクション メソッドが呼び出される直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-130">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="7c201-131">アクションに渡される引数とアクションから返される結果を操作するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-131">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span>

* <span data-ttu-id="7c201-132">[例外フィルター](#exception-filters)は、応答本文に何かが書き込まれる前に発生する未処理の例外にグローバル ポリシーを適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="7c201-133">[結果フィルター](#result-filters)は、個々のアクション結果の実行の直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="7c201-134">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="7c201-135">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="7c201-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="7c201-136">これらのフィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="7c201-136">The following diagram shows how these filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="7c201-139">実装</span><span class="sxs-lookup"><span data-stu-id="7c201-139">Implementation</span></span>

<span data-ttu-id="7c201-140">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="7c201-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span> 

<span data-ttu-id="7c201-141">パイプライン ステージの前後でコードを実行できる同期フィルターは、On*Stage*Executing メソッドと On*Stage*Executed メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="7c201-141">Synchronous filters that can run code both before and after their pipeline stage define On*Stage*Executing and On*Stage*Executed methods.</span></span> <span data-ttu-id="7c201-142">たとえば、`OnActionExecuting` はアクション メソッドが呼び出される前に呼び出され、`OnActionExecuted` はアクション メソッドが返された後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-142">For example, `OnActionExecuting` is called before the action method is called, and `OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet1)]

<span data-ttu-id="7c201-143">非同期フィルターは、1 つの On*Stage*ExecutionAsync メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="7c201-143">Asynchronous filters define a single On*Stage*ExecutionAsync method.</span></span> <span data-ttu-id="7c201-144">このメソッドは、フィルターのパイプライン ステージを実行する *FilterType*ExecutionDelegate デリゲートを取得します。</span><span class="sxs-lookup"><span data-stu-id="7c201-144">This method takes a *FilterType*ExecutionDelegate delegate which executes the filter's pipeline stage.</span></span> <span data-ttu-id="7c201-145">たとえば、`ActionExecutionDelegate` はアクション メソッドまたは次のアクション フィルターを呼び出すので、その呼び出しの前後でコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-145">For example, `ActionExecutionDelegate` calls the action method or next action filter, and you can execute code before and after you call it.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

<span data-ttu-id="7c201-146">1 つのクラスで複数のフィルター ステージに対してインターフェイスを実装することができます。</span><span class="sxs-lookup"><span data-stu-id="7c201-146">You can implement interfaces for multiple filter stages in a single class.</span></span> <span data-ttu-id="7c201-147">たとえば、[ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) クラスは、`IActionFilter` と `IResultFilter` と、それらの非同期バージョンを実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-147">For example, the [ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

> [!NOTE]
> <span data-ttu-id="7c201-148">フィルター インターフェイスの同期と非同期バージョンの両方ではなく、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-148">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="7c201-149">フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7c201-149">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="7c201-150">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7c201-150">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="7c201-151">1 つのクラスに両方のインターフェイスを実装すると、非同期のメソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-151">If you were to implement both interfaces on one class, only the async method would be called.</span></span> <span data-ttu-id="7c201-152">[ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="7c201-152">When using abstract classes like [ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) you would override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="ifilterfactory"></a><span data-ttu-id="7c201-153">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7c201-153">IFilterFactory</span></span>

<span data-ttu-id="7c201-154">[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) は [IFilterMetadata](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifiltermetadata) を実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-154">[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) implements [IFilterMetadata](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifiltermetadata).</span></span> <span data-ttu-id="7c201-155">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-155">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="7c201-156">フレームワークは、フィルターを呼び出す準備をする際に、それを `IFilterFactory` にキャストしようとします。</span><span class="sxs-lookup"><span data-stu-id="7c201-156">When the framework prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="7c201-157">そのキャストが成功した場合、呼び出される `IFilterMetadata` インスタンスを作成するために [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-157">If that cast succeeds, the [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) method is called to create the `IFilterMetadata` instance that will be invoked.</span></span> <span data-ttu-id="7c201-158">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="7c201-158">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="7c201-159">フィルターを作成するための別の方法として、独自の属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-159">You can implement `IFilterFactory` on your own attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a><span data-ttu-id="7c201-160">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="7c201-160">Built-in filter attributes</span></span>

<span data-ttu-id="7c201-161">フレームワークには、サブクラスを作成したりカスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7c201-161">The framework includes built-in attribute-based filters that you can subclass and customize.</span></span> <span data-ttu-id="7c201-162">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="7c201-162">For example, the following Result filter adds a header to the response.</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

<span data-ttu-id="7c201-163">属性は、上記の例のように、フィルターが引数を受け取れるようにします。</span><span class="sxs-lookup"><span data-stu-id="7c201-163">Attributes allow filters to accept arguments, as shown in the example above.</span></span> <span data-ttu-id="7c201-164">この属性をコントローラーまたはアクション メソッドに追加し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="7c201-164">You would add this attribute to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="7c201-165">`Index` アクションの結果を次に示します。応答ヘッダーが右下に表示されています。</span><span class="sxs-lookup"><span data-stu-id="7c201-165">The result of the `Index` action is shown below - the response headers are displayed on the bottom right.</span></span>

![("Author Steve Smith @ardalis" が含まれている) 応答ヘッダーを表示している Microsoft Edge の開発者ツール](filters/_static/add-header.png)

<span data-ttu-id="7c201-167">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-167">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="7c201-168">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="7c201-168">Filter attributes:</span></span>

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

<span data-ttu-id="7c201-169">`TypeFilterAttribute` と `ServiceFilterAttribute` については、[この記事で後ほど](#dependency-injection)説明します。</span><span class="sxs-lookup"><span data-stu-id="7c201-169">`TypeFilterAttribute` and `ServiceFilterAttribute` are explained [later in this article](#dependency-injection).</span></span>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="7c201-170">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="7c201-170">Filter scopes and order of execution</span></span>

<span data-ttu-id="7c201-171">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="7c201-171">A filter can be added to the pipeline at one of three *scopes*.</span></span> <span data-ttu-id="7c201-172">属性を使用して、特定のアクション メソッドまたはコントローラー クラスにフィルターを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-172">You can add a filter to a particular action method or to a controller class by using an attribute.</span></span> <span data-ttu-id="7c201-173">あるいは、すべてのコントローラーとアクションに対してフィルターをグローバル登録できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-173">Or you can register a filter globally for all controllers and actions.</span></span> <span data-ttu-id="7c201-174">`ConfigureServices` の `MvcOptions.Filters` コレクションに追加することでフィルターをグローバルに追加できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-174">Filters are added globally by adding it to the `MvcOptions.Filters` collection in `ConfigureServices`:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a><span data-ttu-id="7c201-175">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="7c201-175">Default order of execution</span></span>

<span data-ttu-id="7c201-176">パイプラインの特定のステージに対して複数のフィルターがある場合に、スコープがフィルターの実行の既定の順序を決定します。</span><span class="sxs-lookup"><span data-stu-id="7c201-176">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="7c201-177">グローバル フィルターがクラス フィルターを囲み、クラス フィルターがメソッド フィルターを囲みます。</span><span class="sxs-lookup"><span data-stu-id="7c201-177">Global filters surround class filters, which in turn surround method filters.</span></span> <span data-ttu-id="7c201-178">これは、[入れ子人形](https://wikipedia.org/wiki/Matryoshka_doll)のように、スコープが前のスコープを囲むたびに大きくなることから、"マトリョーシカ人形" 入れ子と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="7c201-178">This is sometimes referred to as "Russian doll" nesting, as each increase in scope is wrapped around the previous scope, like a [nesting doll](https://wikipedia.org/wiki/Matryoshka_doll).</span></span> <span data-ttu-id="7c201-179">通常は、明示的に順序を決定しなくても、目的のオーバーライド動作が得られます。</span><span class="sxs-lookup"><span data-stu-id="7c201-179">You generally get the desired overriding behavior without having to explicitly determine ordering.</span></span>

<span data-ttu-id="7c201-180">この入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-180">As a result of this nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="7c201-181">シーケンスは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7c201-181">The sequence looks like this:</span></span>

* <span data-ttu-id="7c201-182">グローバルに適用されるフィルターの *before* コード</span><span class="sxs-lookup"><span data-stu-id="7c201-182">The *before* code of filters applied globally</span></span>
  * <span data-ttu-id="7c201-183">コントローラーに適用されるフィルターの *before* コード</span><span class="sxs-lookup"><span data-stu-id="7c201-183">The *before* code of filters applied to controllers</span></span>
    * <span data-ttu-id="7c201-184">アクション メソッドに適用されるフィルターの *before* コード</span><span class="sxs-lookup"><span data-stu-id="7c201-184">The *before* code of filters applied to action methods</span></span>
    * <span data-ttu-id="7c201-185">アクション メソッドに適用されるフィルターの *after* コード</span><span class="sxs-lookup"><span data-stu-id="7c201-185">The *after* code of filters applied to action methods</span></span>
  * <span data-ttu-id="7c201-186">コントローラーに適用されるフィルターの *after* コード</span><span class="sxs-lookup"><span data-stu-id="7c201-186">The *after* code of filters applied to controllers</span></span>
* <span data-ttu-id="7c201-187">グローバルに適用されるフィルターの *after* コード</span><span class="sxs-lookup"><span data-stu-id="7c201-187">The *after* code of filters applied globally</span></span>
  
<span data-ttu-id="7c201-188">同期アクション フィルターに対して呼び出されるフィルター メソッドの順序を示す例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="7c201-188">Here's an example that illustrates the order in which filter methods are called for synchronous Action filters.</span></span>

| <span data-ttu-id="7c201-189">シーケンス</span><span class="sxs-lookup"><span data-stu-id="7c201-189">Sequence</span></span> | <span data-ttu-id="7c201-190">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="7c201-190">Filter scope</span></span> | <span data-ttu-id="7c201-191">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="7c201-191">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="7c201-192">1</span><span class="sxs-lookup"><span data-stu-id="7c201-192">1</span></span> | <span data-ttu-id="7c201-193">Global</span><span class="sxs-lookup"><span data-stu-id="7c201-193">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c201-194">2</span><span class="sxs-lookup"><span data-stu-id="7c201-194">2</span></span> | <span data-ttu-id="7c201-195">コントローラー</span><span class="sxs-lookup"><span data-stu-id="7c201-195">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c201-196">3</span><span class="sxs-lookup"><span data-stu-id="7c201-196">3</span></span> | <span data-ttu-id="7c201-197">メソッド</span><span class="sxs-lookup"><span data-stu-id="7c201-197">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c201-198">4</span><span class="sxs-lookup"><span data-stu-id="7c201-198">4</span></span> | <span data-ttu-id="7c201-199">メソッド</span><span class="sxs-lookup"><span data-stu-id="7c201-199">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7c201-200">5</span><span class="sxs-lookup"><span data-stu-id="7c201-200">5</span></span> | <span data-ttu-id="7c201-201">コントローラー</span><span class="sxs-lookup"><span data-stu-id="7c201-201">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="7c201-202">6</span><span class="sxs-lookup"><span data-stu-id="7c201-202">6</span></span> | <span data-ttu-id="7c201-203">Global</span><span class="sxs-lookup"><span data-stu-id="7c201-203">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="7c201-204">このシーケンスが示すもの:</span><span class="sxs-lookup"><span data-stu-id="7c201-204">This sequence shows:</span></span>

* <span data-ttu-id="7c201-205">メソッド フィルターは、コントローラー フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="7c201-205">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="7c201-206">コントローラー フィルターは、グローバル フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="7c201-206">The controller filter is nested within the global filter.</span></span> 

<span data-ttu-id="7c201-207">別の言い方をすると、非同期フィルターの On*Stage*ExecutionAsync メソッド内にいる場合、コードがスタックにある間に、厳密なスコープを持つすべてのフィルターが実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-207">To put it another way, if you're inside an async filter's On*Stage*ExecutionAsync method, all of the filters with a tighter scope run while your code is on the stack.</span></span>

> [!NOTE]
> <span data-ttu-id="7c201-208">`Controller` 基底クラスから継承するすべてのコントローラーには、`OnActionExecuting` メソッドと `OnActionExecuted` メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="7c201-208">Every controller that inherits from the `Controller` base class includes `OnActionExecuting` and `OnActionExecuted` methods.</span></span> <span data-ttu-id="7c201-209">これらのメソッドは、特定のアクションに対して実行されるフィルターをラップします。`OnActionExecuting` はどのフィルターよりも前に呼び出され、`OnActionExecuted` はすべてのフィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-209">These methods wrap the filters that run for a given action:  `OnActionExecuting` is called before any of the filters, and `OnActionExecuted` is called after all of the filters.</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="7c201-210">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="7c201-210">Overriding the default order</span></span>

<span data-ttu-id="7c201-211">`IOrderedFilter` を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="7c201-211">You can override the default sequence of execution by implementing `IOrderedFilter`.</span></span> <span data-ttu-id="7c201-212">このインターフェイスは、実行の順序を決定するために、スコープよりも優先される `Order` プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="7c201-212">This interface exposes an `Order` property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="7c201-213">`Order` 値が低いフィルターがその *before* コードを、その `Order` の高い値よりも前に実行させます。</span><span class="sxs-lookup"><span data-stu-id="7c201-213">A filter with a lower `Order` value will have its *before* code executed before that of a filter with a higher value of `Order`.</span></span> <span data-ttu-id="7c201-214">`Order` 値が低いフィルターがその *after* コードを、その高い `Order` の値よりも後に実行させます。</span><span class="sxs-lookup"><span data-stu-id="7c201-214">A filter with a lower `Order` value will have its *after* code executed after that of a filter with a higher `Order` value.</span></span> <span data-ttu-id="7c201-215">`Order` プロパティは、コンストラクター パラメーターを使用して設定できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-215">You can set the `Order` property by using a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="7c201-216">上記の例で示されているのと同じ 3 つのアクション フィルターがあるが、コントローラーとグローバル フィルターの `Order` プロパティがそれぞれ 1 と 2 に設定されている場合、実行の順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="7c201-216">If you have the same 3 Action filters shown in the preceding example but set the `Order` property of the controller and global filters to 1 and 2 respectively, the order of execution would be reversed.</span></span>

| <span data-ttu-id="7c201-217">シーケンス</span><span class="sxs-lookup"><span data-stu-id="7c201-217">Sequence</span></span> | <span data-ttu-id="7c201-218">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="7c201-218">Filter scope</span></span> | <span data-ttu-id="7c201-219">`Order` プロパティ</span><span class="sxs-lookup"><span data-stu-id="7c201-219">`Order` property</span></span> | <span data-ttu-id="7c201-220">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="7c201-220">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="7c201-221">1</span><span class="sxs-lookup"><span data-stu-id="7c201-221">1</span></span> | <span data-ttu-id="7c201-222">メソッド</span><span class="sxs-lookup"><span data-stu-id="7c201-222">Method</span></span> | <span data-ttu-id="7c201-223">0</span><span class="sxs-lookup"><span data-stu-id="7c201-223">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="7c201-224">2</span><span class="sxs-lookup"><span data-stu-id="7c201-224">2</span></span> | <span data-ttu-id="7c201-225">コントローラー</span><span class="sxs-lookup"><span data-stu-id="7c201-225">Controller</span></span> | <span data-ttu-id="7c201-226">1</span><span class="sxs-lookup"><span data-stu-id="7c201-226">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7c201-227">3</span><span class="sxs-lookup"><span data-stu-id="7c201-227">3</span></span> | <span data-ttu-id="7c201-228">Global</span><span class="sxs-lookup"><span data-stu-id="7c201-228">Global</span></span> | <span data-ttu-id="7c201-229">2</span><span class="sxs-lookup"><span data-stu-id="7c201-229">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="7c201-230">4</span><span class="sxs-lookup"><span data-stu-id="7c201-230">4</span></span> | <span data-ttu-id="7c201-231">Global</span><span class="sxs-lookup"><span data-stu-id="7c201-231">Global</span></span> | <span data-ttu-id="7c201-232">2</span><span class="sxs-lookup"><span data-stu-id="7c201-232">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7c201-233">5</span><span class="sxs-lookup"><span data-stu-id="7c201-233">5</span></span> | <span data-ttu-id="7c201-234">コントローラー</span><span class="sxs-lookup"><span data-stu-id="7c201-234">Controller</span></span> | <span data-ttu-id="7c201-235">1</span><span class="sxs-lookup"><span data-stu-id="7c201-235">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="7c201-236">6</span><span class="sxs-lookup"><span data-stu-id="7c201-236">6</span></span> | <span data-ttu-id="7c201-237">メソッド</span><span class="sxs-lookup"><span data-stu-id="7c201-237">Method</span></span> | <span data-ttu-id="7c201-238">0</span><span class="sxs-lookup"><span data-stu-id="7c201-238">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="7c201-239">フィルターの実行順序を決定するときに、`Order` プロパティはスコープより優先されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-239">The `Order` property trumps scope when determining the order in which filters will run.</span></span> <span data-ttu-id="7c201-240">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-240">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="7c201-241">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="7c201-241">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="7c201-242">組み込みのフィルターの場合、`Order` をゼロ以外の値に設定しない限り、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-242">For built-in filters, scope determines order unless you set `Order` to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="7c201-243">キャンセルとショート サーキット</span><span class="sxs-lookup"><span data-stu-id="7c201-243">Cancellation and short circuiting</span></span>

<span data-ttu-id="7c201-244">フィルター メソッドに提供される `context` パラメーターで `Result` プロパティを設定することで、フィルター パイプラインを任意の時点でショート サーキットできます。</span><span class="sxs-lookup"><span data-stu-id="7c201-244">You can short-circuit the filter pipeline at any point by setting the `Result` property on the `context` parameter provided to the filter method.</span></span> <span data-ttu-id="7c201-245">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="7c201-245">For instance, the following Resource filter prevents the rest of the pipeline from executing.</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

<span data-ttu-id="7c201-246">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="7c201-246">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="7c201-247">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="7c201-247">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="7c201-248">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-248">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="7c201-249">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-249">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="7c201-250">そのため、`SomeResource` アクションの場合、`AddHeader` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="7c201-250">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="7c201-251">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="7c201-251">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="7c201-252">そのフィルターの種類が原因で、あるいは `Order` プロパティの明示的な使用により、`ShortCircuitingResourceFilter` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-252">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="7c201-253">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="7c201-253">Dependency injection</span></span>

<span data-ttu-id="7c201-254">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-254">Filters can be added by type or by instance.</span></span> <span data-ttu-id="7c201-255">インスタンスを追加する場合、そのインスタンスがすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-255">If you add an instance, that instance will be used for every request.</span></span> <span data-ttu-id="7c201-256">種類を追加すると、種類でアクティブ化されます。つまり、要求ごとにインスタンスが作成され、[依存関係の挿入](../../fundamentals/dependency-injection.md) (DI) によってコンストラクターの依存関係が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-256">If you add a type, it will be type-activated, meaning an instance will be created for each request and any constructor dependencies will be populated by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="7c201-257">種類ごとにフィルターを追加するのは、`filters.Add(new TypeFilterAttribute(typeof(MyFilter)))` に相当します。</span><span class="sxs-lookup"><span data-stu-id="7c201-257">Adding a filter by type is equivalent to `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.</span></span>

<span data-ttu-id="7c201-258">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](../../fundamentals/dependency-injection.md) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="7c201-258">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="7c201-259">これは、属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要だからです。</span><span class="sxs-lookup"><span data-stu-id="7c201-259">This is because attributes must have their constructor parameters supplied where they're applied.</span></span> <span data-ttu-id="7c201-260">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="7c201-260">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="7c201-261">フィルターに DI からアクセスする必要のある依存関係がある場合、サポートされているいくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="7c201-261">If your filters have dependencies that you need to access from DI, there are several supported approaches.</span></span> <span data-ttu-id="7c201-262">次のいずれかを使用して、クラスまたはアクション メソッドにフィルターを適用できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-262">You can apply your filter to a class or action method using one of the following:</span></span>

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* <span data-ttu-id="7c201-263">属性に実装された `IFilterFactory`</span><span class="sxs-lookup"><span data-stu-id="7c201-263">`IFilterFactory` implemented on your attribute</span></span>

> [!NOTE]
> <span data-ttu-id="7c201-264">DI から取得できる依存関係の 1 つに、ロガーがあります。</span><span class="sxs-lookup"><span data-stu-id="7c201-264">One dependency you might want to get from DI is a logger.</span></span> <span data-ttu-id="7c201-265">ただし、必要な[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)は既に提供されている場合があるため、ログ目的のためだけにフィルターを作成して使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="7c201-265">However, avoid creating and using filters purely for logging purposes, since the [built-in framework logging features](xref:fundamentals/logging/index) may already provide what you need.</span></span> <span data-ttu-id="7c201-266">フィルターにログ記録を追加する場合は、MVC アクションやその他のフレームワーク イベントではなく、ビジネス ドメインの懸案事項や、フィルターに固有の動作を重視する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c201-266">If you're going to add logging to your filters, it should focus on business domain concerns or behavior specific to your filter, rather than MVC actions or other framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="7c201-267">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7c201-267">ServiceFilterAttribute</span></span>

<span data-ttu-id="7c201-268">サービス フィルターの実装の種類は DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="7c201-268">Service filter implementation types are registered in DI.</span></span> <span data-ttu-id="7c201-269">`ServiceFilterAttribute` は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="7c201-269">A `ServiceFilterAttribute` retrieves an instance of the filter from DI.</span></span> <span data-ttu-id="7c201-270">`ServiceFilterAttribute` のコンテナーに `Startup.ConfigureServices` を追加し、`[ServiceFilter]` 属性でそれを参照します。</span><span class="sxs-lookup"><span data-stu-id="7c201-270">Add the `ServiceFilterAttribute` to the container in `Startup.ConfigureServices`, and reference it in a `[ServiceFilter]` attribute:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="7c201-271">`ServiceFilterAttribute` を使用する場合、`IsReusable` を設定すると、フィルター インスタンスが作成された要求の範囲外で再利用できる*可能性*があることのヒントになります。</span><span class="sxs-lookup"><span data-stu-id="7c201-271">When using `ServiceFilterAttribute`, setting `IsReusable` is a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7c201-272">このフレームワークで、フィルターの 1 インスタンスが作成されたり、今後のある時点で DI コンテナーからフィルターが再要求されたりするという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="7c201-272">The framework provides no guarantees that a single instance of the filter will be created or the filter will not be re-requested from the DI container at some later point.</span></span> <span data-ttu-id="7c201-273">シングルトン以外で有効期間があるサービスに依存するフィルターを使用するときは、`IsReusable` の使用は避けてください。</span><span class="sxs-lookup"><span data-stu-id="7c201-273">Avoid using `IsReusable` when using a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7c201-274">フィルターの種類を登録せずに `ServiceFilterAttribute` を使用すると、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="7c201-274">Using `ServiceFilterAttribute` without registering the filter type results in an exception:</span></span>

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

<span data-ttu-id="7c201-275">`ServiceFilterAttribute` は、`IFilterFactory` を実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-275">`ServiceFilterAttribute` implements `IFilterFactory`.</span></span> <span data-ttu-id="7c201-276">`IFilterFactory` は、`IFilterMetadata` インスタンスを作成するために `CreateInstance` メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="7c201-276">`IFilterFactory` exposes the `CreateInstance` method for creating an `IFilterMetadata` instance.</span></span> <span data-ttu-id="7c201-277">`CreateInstance` メソッドは、サービス コンテナー (DI) から、指定した型を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7c201-277">The `CreateInstance` method loads the specified type from the services container (DI).</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="7c201-278">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="7c201-278">TypeFilterAttribute</span></span>

<span data-ttu-id="7c201-279">`TypeFilterAttribute` は`ServiceFilterAttribute` と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="7c201-279">`TypeFilterAttribute` is similar to `ServiceFilterAttribute`, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="7c201-280">`Microsoft.Extensions.DependencyInjection.ObjectFactory` を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="7c201-280">It instantiates the type by using `Microsoft.Extensions.DependencyInjection.ObjectFactory`.</span></span>

<span data-ttu-id="7c201-281">この違いにより:</span><span class="sxs-lookup"><span data-stu-id="7c201-281">Because of this difference:</span></span>

* <span data-ttu-id="7c201-282">`TypeFilterAttribute` を利用して参照される型は、先にコンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="7c201-282">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the container first.</span></span>  <span data-ttu-id="7c201-283">コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="7c201-283">They do have their dependencies fulfilled by the container.</span></span> 
* <span data-ttu-id="7c201-284">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="7c201-284">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="7c201-285">`TypeFilterAttribute` を使用する場合、`IsReusable` を設定すると、フィルター インスタンスが作成された要求の範囲外で再利用できる*可能性*があることのヒントになります。</span><span class="sxs-lookup"><span data-stu-id="7c201-285">When using `TypeFilterAttribute`, setting `IsReusable` is a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="7c201-286">このフレームワークで、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="7c201-286">The framework provides no guarantees that a single instance of the filter will be created.</span></span> <span data-ttu-id="7c201-287">シングルトン以外で有効期間があるサービスに依存するフィルターを使用するときは、`IsReusable` の使用は避けてください。</span><span class="sxs-lookup"><span data-stu-id="7c201-287">Avoid using `IsReusable` when using a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="7c201-288">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7c201-288">The following example demonstrates how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

### <a name="ifilterfactory-implemented-on-your-attribute"></a><span data-ttu-id="7c201-289">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="7c201-289">IFilterFactory implemented on your attribute</span></span>

<span data-ttu-id="7c201-290">次のようなフィルターがある場合:</span><span class="sxs-lookup"><span data-stu-id="7c201-290">If you have a filter that:</span></span>

* <span data-ttu-id="7c201-291">引数を必要としない。</span><span class="sxs-lookup"><span data-stu-id="7c201-291">Doesn't require any arguments.</span></span>
* <span data-ttu-id="7c201-292">DI で満たす必要があるコンストラクター依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="7c201-292">Has constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="7c201-293">`[TypeFilter(typeof(FilterType))]` の代わりに、クラスとメソッドで独自の名前付き属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-293">You can use your own named attribute on classes and methods instead of `[TypeFilter(typeof(FilterType))]`).</span></span> <span data-ttu-id="7c201-294">次のフィルターは、これを実装する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7c201-294">The following filter shows how this can be implemented:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="7c201-295">このフィルターは、`[TypeFilter]` または `[ServiceFilter]` を使用する代わりに、`[SampleActionFilter]` 構文を使用して、クラスまたはメソッドに適用することができます。</span><span class="sxs-lookup"><span data-stu-id="7c201-295">This filter can be applied to classes or methods using the `[SampleActionFilter]` syntax, instead of having to use `[TypeFilter]` or `[ServiceFilter]`.</span></span>

## <a name="authorization-filters"></a><span data-ttu-id="7c201-296">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="7c201-296">Authorization filters</span></span>

<span data-ttu-id="7c201-297">*承認フィルター*:</span><span class="sxs-lookup"><span data-stu-id="7c201-297">*Authorization filters*:</span></span>

* <span data-ttu-id="7c201-298">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="7c201-298">Control access to action methods.</span></span>
* <span data-ttu-id="7c201-299">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="7c201-299">Are the first filters to be executed within the filter pipeline.</span></span> 
* <span data-ttu-id="7c201-300">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="7c201-300">Have a before method, but no after method.</span></span> 

<span data-ttu-id="7c201-301">独自の承認フレームワークを記述している場合は、カスタムの承認フィルターを記述するだけで済みます。</span><span class="sxs-lookup"><span data-stu-id="7c201-301">You should only write a custom authorization filter if you are writing your own authorization framework.</span></span> <span data-ttu-id="7c201-302">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="7c201-302">Prefer configuring your authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="7c201-303">組み込みフィルターの実装は、認証システムを呼び出すことしかしません。</span><span class="sxs-lookup"><span data-stu-id="7c201-303">The built-in filter implementation is just responsible for calling the authorization system.</span></span>

<span data-ttu-id="7c201-304">例外を処理するものがないため (例外フィルターは例外を処理しません)、承認フィルター内で例外をスローしないでください。</span><span class="sxs-lookup"><span data-stu-id="7c201-304">You shouldn't throw exceptions within authorization filters, since nothing will handle the exception (exception filters won't handle them).</span></span> <span data-ttu-id="7c201-305">例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="7c201-305">Consider issuing a challenge when an exception occurs.</span></span>

<span data-ttu-id="7c201-306">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c201-306">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="7c201-307">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="7c201-307">Resource filters</span></span>

* <span data-ttu-id="7c201-308">`IResourceFilter` または `IAsyncResourceFilter` のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-308">Implement either the `IResourceFilter` or `IAsyncResourceFilter` interface,</span></span>
* <span data-ttu-id="7c201-309">その実行では、多くのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="7c201-309">Their execution wraps most of the filter pipeline.</span></span> 
* <span data-ttu-id="7c201-310">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-310">Only [Authorization filters](#authorization-filters) run before Resource filters.</span></span>

<span data-ttu-id="7c201-311">リソース フィルターは、要求が行っている作業の大部分を迂回する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="7c201-311">Resource filters are useful to short-circuit most of the work a request is doing.</span></span> <span data-ttu-id="7c201-312">たとえば、フィルターをキャッシュすることで、応答がキャッシュ内にある場合に、パイプラインの残りの部分を回避することができます。</span><span class="sxs-lookup"><span data-stu-id="7c201-312">For example, a caching filter can avoid the rest of the pipeline if the response is in the cache.</span></span>

<span data-ttu-id="7c201-313">前に示した[リソース フィルターのショート サーキット](#short-circuiting-resource-filter)は、リソース フィルターの一例です。</span><span class="sxs-lookup"><span data-stu-id="7c201-313">The [short circuiting resource filter](#short-circuiting-resource-filter) shown earlier is one example of a resource filter.</span></span> <span data-ttu-id="7c201-314">もう 1 つの例が [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs) です。</span><span class="sxs-lookup"><span data-stu-id="7c201-314">Another example is [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

* <span data-ttu-id="7c201-315">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="7c201-315">It prevents model binding from accessing the form data.</span></span> 
* <span data-ttu-id="7c201-316">大きなファイルのアップロードや、メモリにフォームが読み込まれないようにするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="7c201-316">It's useful for large file uploads and want to prevent the form from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="7c201-317">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="7c201-317">Action filters</span></span>

<span data-ttu-id="7c201-318">*アクション フィルター*:</span><span class="sxs-lookup"><span data-stu-id="7c201-318">*Action filters*:</span></span>

* <span data-ttu-id="7c201-319">`IActionFilter` または `IAsyncActionFilter` のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-319">Implement either the `IActionFilter` or `IAsyncActionFilter` interface.</span></span>
* <span data-ttu-id="7c201-320">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="7c201-320">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="7c201-321">サンプルのアクション フィルターを次に示します。</span><span class="sxs-lookup"><span data-stu-id="7c201-321">Here's a sample action filter:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="7c201-322">[ActionExecutingContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) が次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="7c201-322">The [ActionExecutingContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) provides the following properties:</span></span>

* <span data-ttu-id="7c201-323">`ActionArguments`: アクションへの入力を操作できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-323">`ActionArguments` - lets you manipulate the inputs to the action.</span></span>
* <span data-ttu-id="7c201-324">`Controller`: コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-324">`Controller` - lets you manipulate the controller instance.</span></span> 
* <span data-ttu-id="7c201-325">`Result`: これを設定することで、アクション メソッドと後続のアクション フィルターの実行をショートサーキットします。</span><span class="sxs-lookup"><span data-stu-id="7c201-325">`Result` - setting this short-circuits execution of the action method and subsequent action filters.</span></span> <span data-ttu-id="7c201-326">例外をスローすることで、アクション メソッドと後続のアクション フィルターの実行も防止できますが、成功の結果ではなく、エラーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="7c201-326">Throwing an exception also prevents execution of the action method and subsequent filters, but is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7c201-327">[ActionExecutedContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="7c201-327">The [ActionExecutedContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="7c201-328">`Canceled`: 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="7c201-328">`Canceled` - will be true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="7c201-329">`Exception`: アクションまたは後続のアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="7c201-329">`Exception` - will be non-null if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7c201-330">このプロパティを null に設定すると、例外を効果的に '処理' でき、`Result` がアクション メソッドから通常に返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-330">Setting this property to null effectively 'handles' an exception, and `Result` will be executed as if it were returned from the action method normally.</span></span>

<span data-ttu-id="7c201-331">`IAsyncActionFilter` の場合、`ActionExecutionDelegate` の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="7c201-331">For an `IAsyncActionFilter`, a call to the `ActionExecutionDelegate`:</span></span>

* <span data-ttu-id="7c201-332">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-332">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="7c201-333">`ActionExecutedContext` が返されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-333">returns `ActionExecutedContext`.</span></span> 

<span data-ttu-id="7c201-334">ショートサーキットするには、`ActionExecutingContext.Result` をいくつかの結果インスタンスに割り当てます。`ActionExecutionDelegate` は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="7c201-334">To short-circuit, assign `ActionExecutingContext.Result` to some result instance and don't call the `ActionExecutionDelegate`.</span></span>

<span data-ttu-id="7c201-335">フレームワークは、サブクラス化できる抽象 `ActionFilterAttribute` を提供します。</span><span class="sxs-lookup"><span data-stu-id="7c201-335">The framework provides an abstract `ActionFilterAttribute` that you can subclass.</span></span> 

<span data-ttu-id="7c201-336">アクション フィルターを使用してモデルの状態を検証し、状態が無効な場合に、エラーを返すことができます。</span><span class="sxs-lookup"><span data-stu-id="7c201-336">You can use an action filter to validate model state and return any errors if the state is invalid:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

<span data-ttu-id="7c201-337">`OnActionExecuted` メソッドは、アクション メソッドの後に実行され、`ActionExecutedContext.Result` プロパティを通じてアクションの結果を確認および操作することができます。</span><span class="sxs-lookup"><span data-stu-id="7c201-337">The `OnActionExecuted` method runs after the action method and can see and manipulate the results of the action through the `ActionExecutedContext.Result` property.</span></span> <span data-ttu-id="7c201-338">別のフィルターによってアクションの実行がショートサーキットされた場合、`ActionExecutedContext.Canceled` は true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-338">`ActionExecutedContext.Canceled` will be set to true if the action execution was short-circuited by another filter.</span></span> <span data-ttu-id="7c201-339">アクションまたは後続のアクション フィルターが例外をスローした場合、`ActionExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-339">`ActionExecutedContext.Exception` will be set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="7c201-340">`ActionExecutedContext.Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="7c201-340">Setting `ActionExecutedContext.Exception` to null:</span></span>

* <span data-ttu-id="7c201-341">例外が効果的に '処理' されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-341">Effectively 'handles' an exception.</span></span>
* <span data-ttu-id="7c201-342">`ActionExectedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-342">`ActionExectedContext.Result` is executed as if it were returned normally from the action method.</span></span>

## <a name="exception-filters"></a><span data-ttu-id="7c201-343">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="7c201-343">Exception filters</span></span>

<span data-ttu-id="7c201-344">*例外フィルター*は、`IExceptionFilter` または `IAsyncExceptionFilter` のいずれかのインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-344">*Exception filters* implement either the `IExceptionFilter` or `IAsyncExceptionFilter` interface.</span></span> <span data-ttu-id="7c201-345">これを使用して、アプリの共通のエラー処理ポリシーを実装できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-345">They can be used to implement common error handling policies for an app.</span></span> 

<span data-ttu-id="7c201-346">次の例外フィルターのサンプルでは、カスタムの開発エラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="7c201-346">The following sample exception filter uses a custom developer error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

<span data-ttu-id="7c201-347">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="7c201-347">Exception filters:</span></span>

* <span data-ttu-id="7c201-348">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="7c201-348">Don't have before and after events.</span></span> 
* <span data-ttu-id="7c201-349">`OnException` または `OnExceptionAsync` を実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-349">Implement `OnException` or `OnExceptionAsync`.</span></span> 
* <span data-ttu-id="7c201-350">コントローラーの作成、[モデル バインド](../models/model-binding.md)、アクション フィルター、またはアクション メソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="7c201-350">Handle unhandled exceptions that occur in controller creation, [model binding](../models/model-binding.md), action filters, or action methods.</span></span> 
* <span data-ttu-id="7c201-351">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチしません。</span><span class="sxs-lookup"><span data-stu-id="7c201-351">Do not catch exceptions that occur in Resource filters, Result filters, or MVC Result execution.</span></span>

<span data-ttu-id="7c201-352">例外を処理するには、`ExceptionContext.ExceptionHandled` プロパティを true に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="7c201-352">To handle an exception, set the `ExceptionContext.ExceptionHandled` property to true or write a response.</span></span> <span data-ttu-id="7c201-353">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="7c201-353">This stops propagation of the exception.</span></span> <span data-ttu-id="7c201-354">例外フィルターでは、例外を "成功" に変えられません。</span><span class="sxs-lookup"><span data-stu-id="7c201-354">An Exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="7c201-355">これができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="7c201-355">Only an Action filter can do that.</span></span>

> [!NOTE]
> <span data-ttu-id="7c201-356">ASP.NET Core 1.1 では、`ExceptionHandled` を true に設定し、**さらに**応答を記述すると、応答が送信されません。</span><span class="sxs-lookup"><span data-stu-id="7c201-356">In ASP.NET Core 1.1, the response isn't sent if you set `ExceptionHandled` to true **and** write a response.</span></span> <span data-ttu-id="7c201-357">そのシナリオでは、ASP.NET Core 1.0 は応答を送信し、ASP.NET Core 1.1.2 は 1.0 の動作に戻ります。</span><span class="sxs-lookup"><span data-stu-id="7c201-357">In that scenario, ASP.NET Core 1.0 does send the response, and ASP.NET Core 1.1.2 will return to the 1.0 behavior.</span></span> <span data-ttu-id="7c201-358">詳細については、GitHub リポジトリで「[issue #5594](https://github.com/aspnet/Mvc/issues/5594)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c201-358">For more information, see [issue #5594](https://github.com/aspnet/Mvc/issues/5594) in the GitHub repository.</span></span> 

<span data-ttu-id="7c201-359">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="7c201-359">Exception filters:</span></span>

* <span data-ttu-id="7c201-360">MVC アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="7c201-360">Are good for trapping exceptions that occur within MVC actions.</span></span>
* <span data-ttu-id="7c201-361">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="7c201-361">Are not as flexible as error handling middleware.</span></span> 

<span data-ttu-id="7c201-362">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="7c201-362">Prefer middleware for exception handling.</span></span> <span data-ttu-id="7c201-363">選択された MVC アクションに基づき、*異なる*方法でエラーを処理する必要がある場合にのみ、例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="7c201-363">Use exception filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span> <span data-ttu-id="7c201-364">たとえば、ご利用のアプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c201-364">For example, your app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="7c201-365">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7c201-365">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

<span data-ttu-id="7c201-366">`ExceptionFilterAttribute` はサブクラス化できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-366">The `ExceptionFilterAttribute` can be subclassed.</span></span> 

## <a name="result-filters"></a><span data-ttu-id="7c201-367">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="7c201-367">Result filters</span></span>

* <span data-ttu-id="7c201-368">`IResultFilter` または `IAsyncResultFilter` のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="7c201-368">Implement either the `IResultFilter` or `IAsyncResultFilter` interface.</span></span>
* <span data-ttu-id="7c201-369">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="7c201-369">Their execution surrounds the execution of action results.</span></span> 

<span data-ttu-id="7c201-370">HTTP ヘッダーを追加する結果フィルターの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="7c201-370">Here's an example of a Result filter that adds an HTTP header.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="7c201-371">実行されている結果の種類は、対象のアクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="7c201-371">The kind of result being executed depends on the action in question.</span></span> <span data-ttu-id="7c201-372">ビューを返す MVC アクションには、実行されている `ViewResult` の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7c201-372">An MVC action returning a view would include all razor processing as part of the `ViewResult` being executed.</span></span> <span data-ttu-id="7c201-373">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-373">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="7c201-374">アクション結果に関する詳細は、[こちら](actions.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7c201-374">Learn more about [action results](actions.md)</span></span>

<span data-ttu-id="7c201-375">結果フィルターは、アクションまたはアクション フィルターがアクションの結果を生成するときに、成功した結果に対してのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-375">Result filters are only executed for successful results - when the action or action filters produce an action result.</span></span> <span data-ttu-id="7c201-376">例外フィルターが例外を処理するときには、結果フィルターは実行されません。</span><span class="sxs-lookup"><span data-stu-id="7c201-376">Result filters are not executed when exception filters handle an exception.</span></span>

<span data-ttu-id="7c201-377">`OnResultExecuting` メソッドは、`ResultExecutingContext.Cancel` を true に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="7c201-377">The `OnResultExecuting` method can short-circuit execution of the action result and subsequent result filters by setting `ResultExecutingContext.Cancel` to true.</span></span> <span data-ttu-id="7c201-378">ショートサーキットする場合は、通常、空の応答が生成されないように応答オブジェクトに記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7c201-378">You should generally write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="7c201-379">例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="7c201-379">Throwing an exception will:</span></span>

* <span data-ttu-id="7c201-380">アクション結果と後続フィルターの実行が回避されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-380">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="7c201-381">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-381">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="7c201-382">`OnResultExecuted` メソッドを実行するときに、応答がクライアントに送信され、(例外がスローされない限り) それ以上変更できない可能性があます。</span><span class="sxs-lookup"><span data-stu-id="7c201-382">When the `OnResultExecuted` method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).</span></span> <span data-ttu-id="7c201-383">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-383">`ResultExecutedContext.Canceled` will be set to true if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="7c201-384">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-384">`ResultExecutedContext.Exception` will be set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="7c201-385">`Exception` を null に設定すると、例外を効果的に '処理' でき、パイプラインの後方で MVC によって例外が再スローされることを防げます。</span><span class="sxs-lookup"><span data-stu-id="7c201-385">Setting `Exception` to null effectively 'handles' an exception and prevents the exception from being rethrown by MVC later in the pipeline.</span></span> <span data-ttu-id="7c201-386">結果フィルター内の例外を処理しているときに、応答にどのデータも書き込めない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7c201-386">When you're handling an exception in a result filter, you might not be able to write any data to the response.</span></span> <span data-ttu-id="7c201-387">アクションの結果がその実行の途中でスローして、クライアントに対してはヘッダーが既にフラッシュされている場合、エラー コードを送信するための信頼性の高いメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="7c201-387">If the action result throws partway through its execution, and the headers have already been flushed to the client, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="7c201-388">`IAsyncResultFilter` の場合、`ResultExecutionDelegate` での `await next` への呼び出しは、後続のすべての結果フィルターとアクションの結果を実行します。</span><span class="sxs-lookup"><span data-stu-id="7c201-388">For an `IAsyncResultFilter` a call to `await next` on the `ResultExecutionDelegate` executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="7c201-389">ショートサーキットするには、`ResultExecutingContext.Cancel` を true に設定します。`ResultExectionDelegate` は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="7c201-389">To short-circuit, set `ResultExecutingContext.Cancel` to true and don't call the `ResultExectionDelegate`.</span></span>

<span data-ttu-id="7c201-390">フレームワークは、サブクラス化できる抽象 `ResultFilterAttribute` を提供します。</span><span class="sxs-lookup"><span data-stu-id="7c201-390">The framework provides an abstract `ResultFilterAttribute` that you can subclass.</span></span> <span data-ttu-id="7c201-391">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="7c201-391">The [AddHeaderAttribute](#add-header-attribute) class shown earlier is an example of a result filter attribute.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="7c201-392">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="7c201-392">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="7c201-393">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="7c201-393">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="7c201-394">ただし、フィルターは MVC の一部である点がミドルウェアとは異なります。つまり、フィルターには MVC コンテキストとコンストラクトへのアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="7c201-394">But filters differ from middleware in that they're part of MVC, which means that they have access to MVC context and constructs.</span></span>

<span data-ttu-id="7c201-395">ASP.NET Core 1.1 では、フィルター パイプラインでミドルウェアを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-395">In ASP.NET Core 1.1, you can use middleware in the filter pipeline.</span></span> <span data-ttu-id="7c201-396">MVC ルート データへのアクセスを必要とする、または特定のコントローラーまたはアクションを実行する必要があるミドルウェア コンポーネントがある場合は、ミドルウェアを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7c201-396">You might want to do that if you have a middleware component that needs access to MVC route data, or one that should run only for certain controllers or actions.</span></span>

<span data-ttu-id="7c201-397">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="7c201-397">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware that you want to inject into the filter pipeline.</span></span> <span data-ttu-id="7c201-398">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="7c201-398">Here's an example that uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="7c201-399">`MiddlewareFilterAttribute` を使用して、選択したコントローラーまたはアクションに対してミドルウェアを実行することも、グローバルにミドルウェアを実行することもできます。</span><span class="sxs-lookup"><span data-stu-id="7c201-399">You can then use the `MiddlewareFilterAttribute` to run the middleware for a selected controller or action or globally:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="7c201-400">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="7c201-400">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="7c201-401">次の操作</span><span class="sxs-lookup"><span data-stu-id="7c201-401">Next actions</span></span>

<span data-ttu-id="7c201-402">フィルターを試すには、[ここ](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)からサンプルをダウンロードして、テストおよび変更を行います。</span><span class="sxs-lookup"><span data-stu-id="7c201-402">To experiment with filters, [download, test and modify the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
