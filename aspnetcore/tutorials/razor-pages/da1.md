---
title: "生成されたページの更新"
author: rick-anderson
description: "生成されたページを更新して、表示をわかりやすくします。"
keywords: "ASP.NET Core,Razor ページ"
ms.author: riande
manager: wpickett
ms.date: 08/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 8b2018bbf83cbb4b5c9139605fbb97d1c5be959f
ms.sourcegitcommit: f2fb0b45284e4f8c4a9c422bec790aede7c1f0ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2017
---
# <a name="updating-the-generated-pages"></a><span data-ttu-id="4eaf0-104">生成されたページの更新</span><span class="sxs-lookup"><span data-stu-id="4eaf0-104">Updating the generated pages</span></span>

<span data-ttu-id="4eaf0-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4eaf0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4eaf0-106">ムービーアプリは上々の滑り出しでしたが、表示が理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-106">We have a good start to the movie app, but the presentation is not ideal.</span></span> <span data-ttu-id="4eaf0-107">時刻の表示が好ましくなく (下の画像の 12:00:00 AM)、**ReleaseDate** は **Release Date** (2 つの単語) にするべきです。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-107">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be **Release Date** (two words).</span></span>

![ムービー データが表示された、Chrome で開かれているムービー アプリケーション](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="4eaf0-109">生成されたコードの更新</span><span class="sxs-lookup"><span data-stu-id="4eaf0-109">Update the generated code</span></span>

<span data-ttu-id="4eaf0-110">*Models/Movie.cs* ファイルを開き、下のコードで強調表示されている行を追加します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-110">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

<span data-ttu-id="4eaf0-111">[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]</span><span class="sxs-lookup"><span data-stu-id="4eaf0-111">[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieDate.cs?name=snippet_1&highlight=10-11)]</span></span>

<span data-ttu-id="4eaf0-112">赤の波線を右クリックし、[クイック アクションとリファクタリング] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-112">Right click on a red squiggly line > ** Quick Actions and Refactorings**.</span></span>

  ![コンテキスト メニューに **[クイック アクションとリファクタリング]** が表示されます。](da1/qa.png)


<span data-ttu-id="4eaf0-114">`using System.ComponentModel.DataAnnotations;` を選択します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-114">Select `using System.ComponentModel.DataAnnotations;`</span></span>

  ![一覧の一番上にある System.ComponentModel.DataAnnotations を使用する](da1/da.png)

  <span data-ttu-id="4eaf0-116">Visual Studio により `using System.ComponentModel.DataAnnotations;` が追加されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-116">Visual studio adds `using System.ComponentModel.DataAnnotations;`.</span></span>

<span data-ttu-id="4eaf0-117">[DataAnnotations](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) については、次のチュートリアルで説明します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-117">We'll cover [DataAnnotations](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) in the next tutorial.</span></span> <span data-ttu-id="4eaf0-118">[Display](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx) 属性は、フィールドの名前として表示する内容 (ここでは、"ReleaseDate" ではなく、"Release Date") を指定します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-118">The [Display](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayattribute.aspx) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="4eaf0-119">[DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) 属性はデータ型 (Date) を指定するため、フィールドに格納される時刻情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-119">The [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) attribute specifies the type of the data (Date), so the time information stored in the field is not displayed.</span></span>

<span data-ttu-id="4eaf0-120">Pages/Movies を参照し、**[編集]** リンクをポイントしてターゲット URL を確認します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-120">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![[編集] リンクがマウスでポイントされ、リンク URL として http://localhost:1234/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](da1/edit7.png)

<span data-ttu-id="4eaf0-122">**[編集]**、**[詳細]**、および **[削除]** の各リンクは、*Pages/Movies/Index.cshtml* ファイルで[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper)によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-122">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/AnchorTagHelper) in the *Pages/Movies/Index.cshtml* file.</span></span>

<span data-ttu-id="4eaf0-123">[!code-cshtml[Main](razor-pages-start\snapshot_sample\RazorPagesMovie\Pages\Movie\Index.cshtml?highlight=16-18&range=32-)]</span><span class="sxs-lookup"><span data-stu-id="4eaf0-123">[!code-cshtml[Main](razor-pages-start\snapshot_sample\RazorPagesMovie\Pages\Movie\Index.cshtml?highlight=16-18&range=32-)]</span></span>

<span data-ttu-id="4eaf0-124">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-124">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="4eaf0-125">上のコードでは、`AnchorTagHelper` は動的に Razor ページからの HTML `href` 属性値 (ルートは相対)、`asp-page`、およびルート ID (`asp-route-id`) を生成します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-125">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="4eaf0-126">詳細については、「[ページの URL の生成](xref:mvc/razor-pages/index#url-generation-for-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-126">See [URL generation for Pages](xref:mvc/razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="4eaf0-127">お好みのブラウザーから **[ソースの表示]** を使用して、生成されたマークアップを確認します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-127">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="4eaf0-128">生成された HTML の部分を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-128">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>

```

<span data-ttu-id="4eaf0-129">動的に生成されたリンクは、クエリ文字列を含むムービー ID を渡します (例: `http://localhost:5000/Movies/Details?id=2`)。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-129">The dynamically-generated links pass the movie ID with a query string (for example, `http://localhost:5000/Movies/Details?id=2` ).</span></span> 

<span data-ttu-id="4eaf0-130">"{id:int}" ルート テンプレートを使用するには、[編集]、[詳細]、および [削除] Razor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-130">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="4eaf0-131">これらの各ページのページ ディレクティブを `@page "{id:int}"` に変更します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-131">Change the page directive for each of these pages to `@page "{id:int}"`.</span></span> <span data-ttu-id="4eaf0-132">アプリを実行してから、ソースを表示します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-132">Run the app and then view source.</span></span> <span data-ttu-id="4eaf0-133">生成される HTML では、次にように URL のパス部分に ID を追加します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-133">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="4eaf0-134">整数を**含まない**、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-134">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="4eaf0-135">たとえば、`http://localhost:5000/Movies/Details` の場合は 404 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-135">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="4eaf0-136">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-136">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

### <a name="update-concurrency-exception-handling"></a><span data-ttu-id="4eaf0-137">同時実行制御の例外処理の更新</span><span class="sxs-lookup"><span data-stu-id="4eaf0-137">Update concurrency exception handling</span></span>

<span data-ttu-id="4eaf0-138">*Pages/Movies/Edit.cshtml.cs* ファイルで `OnPostAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-138">Update the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file.</span></span> <span data-ttu-id="4eaf0-139">次の強調表示されたコードは変更点を示しています。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-139">The following highlighted code shows the changes:</span></span>

<span data-ttu-id="4eaf0-140">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet1&highlight=17-24)]</span><span class="sxs-lookup"><span data-stu-id="4eaf0-140">[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet1&highlight=17-24)]</span></span>

<span data-ttu-id="4eaf0-141">上のコードでは、最初の同時クライアントがムービーを削除し、2 番目の同時クライアントがムービーに変更を投稿した場合にのみ、同時実行制御の例外を検出します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-141">The previous code only detects concurrency exceptions when the first concurrent client deletes the movie, and the second concurrent client posts changes to the movie.</span></span>

<span data-ttu-id="4eaf0-142">`catch` ブロックをテストするには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-142">To test the `catch` block:</span></span>

* <span data-ttu-id="4eaf0-143">`catch (DbUpdateConcurrencyException)` へのブレークポイントの設定</span><span class="sxs-lookup"><span data-stu-id="4eaf0-143">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="4eaf0-144">ムービーを編集します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-144">Edit a movie.</span></span>
* <span data-ttu-id="4eaf0-145">別のブラウザー ウィンドウで、同じムービーの **[削除]** リンクを選択してから、ムービーを削除します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-145">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="4eaf0-146">前のブラウザー ウィンドウで、ムービーに変更を投稿します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-146">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="4eaf0-147">実稼働コードでは、通常、2 つ以上のクライアントが同時にレコードを更新した場合に、同時実行の競合が検出されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-147">Production code would generally detect concurrency conflicts when two or more clients concurrently updated a record.</span></span> <span data-ttu-id="4eaf0-148">詳細については、「[同時実行の競合の処理](xref:data/ef-mvc/concurrency)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-148">See [Handling concurrency conflicts](xref:data/ef-mvc/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="4eaf0-149">レビューの投稿とバインディング</span><span class="sxs-lookup"><span data-stu-id="4eaf0-149">Posting and binding review</span></span>

<span data-ttu-id="4eaf0-150">*Pages/Movies/Edit.cshtml.cs* ファイルを確認します。[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="4eaf0-150">Examine the *Pages/Movies/Edit.cshtml.cs* file: [!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movie/Edit.cshtml.cs?name=snippet2)]</span></span>

<span data-ttu-id="4eaf0-151">HTTP GET 要求が Movies/Edit ページに対して行われた場合 (例: `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="4eaf0-151">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="4eaf0-152">`OnGetAsync` メソッドはデータベースからムービーをフェッチし、`Page` メソッドを返します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-152">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="4eaf0-153">`Page` メソッドは *Pages/Movies/Edit.cshtml* Razor ページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-153">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="4eaf0-154">*Pages/Movies/Edit.cshtml* ファイルにはモデルのディレクティブ (`@model RazorPagesMovie.Pages.Movies.EditModel`) が含まれています。これにより、ページでムービー モデルが使用可能になります。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-154">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the the movie model available on the page.</span></span>
* <span data-ttu-id="4eaf0-155">[編集] フォームには、ムービーからの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-155">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="4eaf0-156">Movies/Edit ページが投稿された場合:</span><span class="sxs-lookup"><span data-stu-id="4eaf0-156">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="4eaf0-157">ページのフォーム値は `Movie` プロパティにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-157">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="4eaf0-158">`[BindProperty]` 属性により、[モデル バインド](xref:mvc/models/model-binding)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-158">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

```csharp
[BindProperty]
public Movie Movie { get; set; }
```

* <span data-ttu-id="4eaf0-159">モデル状態にエラーがある (たとえば、`ReleaseDate` を日付に変換できない) 場合、フォームは送信された値で再度投稿されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-159">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is posted again with the submitted values.</span></span>
* <span data-ttu-id="4eaf0-160">モデル エラーがない場合、ムービーは保存されます。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-160">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="4eaf0-161">[インデックス]、[作成]、および [削除] Razor ページの HTTP GET メソッドも同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-161">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="4eaf0-162">[作成] Razor ページの HTTP POST `OnPostAsync` メソッドも [編集] Razor ページの `OnPostAsync` メソッドと同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-162">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="4eaf0-163">次のチュートリアルでは検索を追加します。</span><span class="sxs-lookup"><span data-stu-id="4eaf0-163">Search is added in the next tutorial.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="4eaf0-164">[前: SQL Server LocalDB の使用](xref:tutorials/razor-pages/sql)
[検索の追加](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="4eaf0-164">[Previous: Working with SQL Server LocalDB](xref:tutorials/razor-pages/sql)
[Adding Search](xref:tutorials/razor-pages/search)</span></span>