---
ms.openlocfilehash: 1f8d3913c83aaf5fe6ec2cec482a30f0f066c16b
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841694"
---

> [!NOTE]
> <span data-ttu-id="97a81-101">このチュートリアルでは、可能な場合は Entity Framework Core の "*移行*" 機能を使います。</span><span class="sxs-lookup"><span data-stu-id="97a81-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="97a81-102">移行では、データ モデルの変更に合わせてデータベース スキーマが更新されます。</span><span class="sxs-lookup"><span data-stu-id="97a81-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="97a81-103">ただし、移行では EF Core プロバイダーでサポートされている種類の変更しか行うことができず、SQLite プロバイダーの機能は制限されています。</span><span class="sxs-lookup"><span data-stu-id="97a81-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="97a81-104">たとえば、列の追加はサポートされていますが、列の削除や変更はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="97a81-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="97a81-105">列を削除または変更するために移行を作成した場合、`ef migrations add` コマンドは成功しますが、`ef database update` コマンドは失敗します。</span><span class="sxs-lookup"><span data-stu-id="97a81-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="97a81-106">これらの制限により、このチュートリアルでは SQLite のスキーマの変更に対しては移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="97a81-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="97a81-107">代わりに、スキーマを変更するときは、データベースを削除して再作成します。</span><span class="sxs-lookup"><span data-stu-id="97a81-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="97a81-108">SQLite の制限に対する回避策としては、テーブル内の何かが変更されたときにテーブルの再構築を実行する移行コードを、手動で作成します。</span><span class="sxs-lookup"><span data-stu-id="97a81-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="97a81-109">テーブルの再構築には次の作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="97a81-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="97a81-110">既存のテーブルの名前の変更。</span><span class="sxs-lookup"><span data-stu-id="97a81-110">Renaming the existing table.</span></span>
>* <span data-ttu-id="97a81-111">新しいテーブルの作成。</span><span class="sxs-lookup"><span data-stu-id="97a81-111">Creating a new table.</span></span>
>* <span data-ttu-id="97a81-112">古いテーブルから新しいテーブルへのデータのコピー。</span><span class="sxs-lookup"><span data-stu-id="97a81-112">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="97a81-113">古いテーブルの削除。</span><span class="sxs-lookup"><span data-stu-id="97a81-113">Dropping the old table.</span></span>
>
><span data-ttu-id="97a81-114">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="97a81-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="97a81-115">SQLite EF Core データベース プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="97a81-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="97a81-116">移行コードをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="97a81-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="97a81-117">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="97a81-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)