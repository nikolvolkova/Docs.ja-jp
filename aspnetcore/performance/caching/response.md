---
title: "ASP.NET Core で応答のキャッシュ"
author: rick-anderson
description: "応答の帯域幅を削減し、パフォーマンスを向上させるキャッシュの使用方法を説明します。"
keywords: "ASP.NET Core、応答の HTTP ヘッダーをキャッシュします。"
ms.author: riande
manager: wpickett
ms.date: 09/20/2017
ms.topic: article
ms.assetid: cb42035a-60b0-472e-a614-cb79f443f654
ms.prod: asp.net-core
uid: performance/caching/response
ms.openlocfilehash: 79d9246632aae0fe9c3629fd7202842836828151
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2017
---
# <a name="response-caching-in-aspnet-core"></a>ASP.NET Core で応答のキャッシュ

によって[John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Steve Smith](https://ardalis.com/)、および[Luke Latham](https://github.com/guardrex)

[表示またはダウンロードするサンプル コード](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/sample)([をダウンロードする方法](xref:tutorials/index#how-to-download-a-sample))

応答のキャッシュ クライアントまたはプロキシ web サーバーに、要求の数を削減します。 応答のキャッシュ量を削減しても、応答を生成する作業の web サーバーを実行します。 応答のキャッシュは、クライアント、プロキシ、およびミドルウェアが応答をキャッシュする方法を指定できるヘッダーによって制御されます。

追加するときに、web サーバーは応答をキャッシュできます[応答キャッシュ ミドルウェア](xref:performance/caching/middleware)です。

## <a name="http-based-response-caching"></a>応答の HTTP ベースのキャッシュ

[仕様の HTTP 1.1 キャッシュ](https://tools.ietf.org/html/rfc7234)インターネット キャッシュの動作方法について説明します。 キャッシュに使用するプライマリ HTTP ヘッダーは[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)、キャッシュの指定に使用される*ディレクティブ*です。 ディレクティブは、回答のサーバーからクライアントには、その方法を変更すると、要求のクライアントからサーバーには、その方法を変更すると、キャッシュ動作を制御します。 要求および応答がプロキシ サーバーで移動し、プロキシ サーバーが HTTP 1.1 キャッシュ仕様に準拠する必要がありますもします。

一般的な`Cache-Control`ディレクティブが次の表に示すようにします。

| ディレクティブ                                                       | 操作 |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | キャッシュは、応答を格納できます。 |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | キャッシュを共有して応答を格納する必要がありません。 プライベート キャッシュは、格納し、応答を再利用可能性があります。 |
| [最大継続期間](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | クライアントが年齢が指定した秒数よりも大きい応答を受け付けませんでした。 例: `max-age=60` (60 秒) `max-age=2592000` (1 か月) |
| [キャッシュなし](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **要求に**: 要求を満たせませんストアド応答がキャッシュでは使用しないでください。 注: は、元のサーバーでは、クライアントの場合、応答を再生成し、ミドルウェアがストアド応答をキャッシュを更新します。<br><br>**応答で**: 元のサーバーで検証を伴わない後続の要求の応答を使用しないでください。 |
| [no ストア](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **要求に**: キャッシュは、要求を格納しないでください。<br><br>**応答で**: キャッシュは、応答の任意の部分を格納しないでください。 |

その他のキャッシュでは、役割を果たすキャッシュ ヘッダーは、次の表に表示されます。

| Header                                                     | 関数 |
| ---------------------------------------------------------- | -------- |
| [経過時間](https://tools.ietf.org/html/rfc7234#section-5.1)     | 応答の生成または送信元のサーバーに正常に検証されてからの秒単位で時間の推定値。 |
| [有効期限が切れる](https://tools.ietf.org/html/rfc7234#section-5.3) | その後、応答は古いと見なされます日付/時刻。 |
| [プラグマ](https://tools.ietf.org/html/rfc7234#section-5.4)  | Http/1.0 との互換性は設定をキャッシュする下位存在`no-cache`動作します。 場合、`Cache-Control`ヘッダーが含まれている、`Pragma`ヘッダーは無視されます。 |
| [異なる](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | 指定するキャッシュされた応答する必要がありますいない送信しない限り、すべての`Vary`ヘッダー フィールドにキャッシュされた応答の元の要求と、新しい要求の両方に一致します。 |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>HTTP ベースのキャッシュは要求のキャッシュ制御ディレクティブ

[Cache-control ヘッダーの HTTP 1.1 キャッシュ仕様](https://tools.ietf.org/html/rfc7234#section-5.2)に従う、有効なキャッシュを必要と`Cache-Control`クライアントによって送信されたヘッダー。 クライアントが要求を行うことができます、`no-cache`ヘッダーの値と force 要求ごとに新しい応答を生成するサーバー。

常にクライアントを受け付けたり`Cache-Control`HTTP キャッシュの目標を考慮する場合に要求ヘッダーを意味します。 公式の仕様では、下にあるキャッシュはクライアント、プロキシ、およびサーバー、ネットワーク経由で要求を満たすの待機時間とネットワークのオーバーヘッドを削減を意図したものです。 必ずしも元のサーバーの負荷を制御する方法です。

使用する場合、このキャッシュ動作上の現在の開発者コントロールはありません、[応答キャッシュ ミドルウェア](xref:performance/caching/middleware)ミドルウェアが公式の仕様をキャッシュに準拠しているためです。 [ミドルウェアは将来拡張](https://github.com/aspnet/ResponseCaching/issues/96)を無視する要求のミドルウェアの構成を許可する`Cache-Control`ヘッダーを提供するキャッシュされた応答を決定するとき。 これが提供されますをサーバーに適切に制御負荷営業案件、ミドルウェアを使用するとします。

## <a name="other-caching-technology-in-aspnet-core"></a>ASP.NET Core でのキャッシュの他のテクノロジ

### <a name="in-memory-caching"></a>メモリ内キャッシュ

キャッシュされたデータを格納するのにサーバーのメモリを使用するメモリ内キャッシュします。 この種類のキャッシュは 1 台のサーバーまたは複数のサーバーを使用するのに適した*スティッキー セッション*です。 スティッキー セッションでは、クライアントからの要求を処理するため、同じサーバーに常にルーティングされることを意味します。

詳細については、次を参照してください。 [ASP.NET Core でのメモリ内キャッシュの概要](xref:performance/caching/memory)です。

### <a name="distributed-cache"></a>分散キャッシュ

分散キャッシュを使用して、アプリが、クラウド サーバー ファームでホストされている場合は、データをメモリに格納します。 キャッシュは、要求を処理するサーバー間で共有されます。 クライアントは、グループ内のサーバーによって処理される要求を送信できる、クライアントのキャッシュされたデータが利用できます。 ASP.NET Core は、SQL Server と分散 Redis キャッシュを提供します。

詳細については、次を参照してください。[分散キャッシュの使用](xref:performance/caching/distributed)です。

### <a name="cache-tag-helper"></a>キャッシュ タグ ヘルパー

キャッシュ タグ ヘルパーの MVC ビューまたは Razor ページからコンテンツをキャッシュできます。 キャッシュ タグ ヘルパーは、メモリ内キャッシュを使用してデータを格納します。

詳細については、次を参照してください。 [ASP.NET Core MVC でのキャッシュ タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)です。

### <a name="distributed-cache-tag-helper"></a>分散キャッシュ タグ ヘルパー

分散キャッシュ タグ ヘルパーの MVC ビューまたは分散クラウドまたは web ファームのシナリオに Razor ページからコンテンツをキャッシュできます。 分散キャッシュ タグ ヘルパーは、データを格納するのに SQL Server または Redis を使用します。

詳細については、次を参照してください。[キャッシュ タグ ヘルパーの分散](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)です。

## <a name="responsecache-attribute"></a>ResponseCache 属性

`ResponseCacheAttribute`応答のキャッシュでは、適切なヘッダーを設定するために必要なパラメーターを指定します。 参照してください[ResponseCacheAttribute](/aspnet/core/api/microsoft.aspnetcore.mvc.responsecacheattribute)パラメーターの詳細についてはします。

> [!WARNING]
> 認証されたクライアントに関する情報を含むコンテンツのキャッシュを無効にします。 キャッシュのみ有効にするユーザーの id や、ユーザーがログインしているかどうかに基づいてコンテンツは変更されません。

`VaryByQueryKeys string[]`(ASP.NET Core 1.1 以降が必要です)。 設定すると、応答のキャッシュ ミドルウェアが応答の識別ストアド クエリ キーの指定された一覧の値。 設定するには、応答のキャッシュ ミドルウェアを有効にする必要があります、`VaryByQueryKeys`プロパティです。 それ以外の場合、ランタイム例外がスローされます。 対応する HTTP ヘッダーがない、`VaryByQueryKeys`プロパティです。 このプロパティは、応答のキャッシュ ミドルウェアによって処理される HTTP 機能です。 キャッシュされた応答を提供するミドルウェアをクエリ文字列とクエリ文字列の値必要がありますと一致前の要求。 たとえば、要求と、次の表に示すように結果のシーケンスがあるとします。

| 要求                          | 結果                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | サーバーから返される     |
| `http://example.com?key1=value1` | ミドルウェアから返される |
| `http://example.com?key1=value2` | サーバーから返される     |

最初の要求がサーバーによって返され、ミドルウェア内でキャッシュします。 2 番目の要求は、クエリ文字列が、前回の要求と一致するので、ミドルウェアによって返されます。 3 番目の要求ではありません、ミドルウェア キャッシュにクエリ文字列の値は、前の要求と一致しません。 

`ResponseCacheAttribute`構成を作成するために使用 (を介して`IFilterFactory`)、`ResponseCacheFilter`です。 `ResponseCacheFilter`適切な HTTP ヘッダーと応答の機能の更新の処理を実行します。 フィルター:

* 既存のすべてのヘッダーを削除`Vary`、 `Cache-Control`、および`Pragma`です。 
* 設定されたプロパティに基づいて、適切なヘッダーを書き込みます、`ResponseCacheAttribute`です。 
* 更新プログラムの応答の場合は、HTTP 機能をキャッシュ`VaryByQueryKeys`が設定されています。

### <a name="vary"></a>異なる

このヘッダーがのみ書き込まれるときに、`VaryByHeader`プロパティを設定します。 設定されている、`Vary`プロパティの値。 次のサンプルは、`VaryByHeader`プロパティ。

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

ブラウザーのネットワーク ツールを使用して応答ヘッダーを表示することができます。 次の図は出力エッジ F12、**ネットワーク** タブのときに、`About2`アクション メソッドが更新されます。

![About2 アクション メソッドが呼び出されたときに、[ネットワーク] タブで F12 出力をエッジします。](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore と Location.None

`NoStore`ほとんどの他のプロパティをオーバーライドします。 このプロパティに設定するときに`true`、`Cache-Control`ヘッダーに設定されて`no-store`です。 場合`Location`に設定されている`None`:

* `Cache-Control` が `no-store,no-cache` に設定されます。
* `Pragma` が `no-cache` に設定されます。

場合`NoStore`は`false`と`Location`は`None`、`Cache-Control`と`Pragma`に設定されている`no-cache`です。

通常は設定`NoStore`に`true`エラー ページにします。 例:

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

これは、結果、次のヘッダーになります。

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>位置と長さ

キャッシュを有効にする`Duration`正の値に設定する必要がありますと`Location`いずれかである必要があります`Any`(既定) または`Client`です。 ここで、`Cache-Control`場所の値の後にヘッダーが設定されている、`max-age`応答のです。

> [!NOTE]
> `Location`オプションの`Any`と`Client`変換`Cache-Control`のヘッダー値`public`と`private`、それぞれします。 前述のように、設定`Location`に`None`設定両方`Cache-Control`と`Pragma`ヘッダーを`no-cache`です。

次のヘッダーを示す例によって生成される設定`Duration`し、既定のままにして`Location`値。

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

これには、次のヘッダーが生成されます。

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a>キャッシュ プロファイル

複製ではなく`ResponseCache`で MVC を設定するとき、オプションとしてコント ローラー アクションの多くの属性でキャッシュ プロファイルの設定を構成できます、`ConfigureServices`メソッド`Startup`です。 によって既定値として参照されているキャッシュ プロファイル内の値が使用されます、`ResponseCache`属性および属性に指定したプロパティによって上書きされます。

キャッシュ プロファイルを設定します。

[!code-csharp[Main](response/sample/Startup.cs?name=snippet1)] 

キャッシュ プロファイルを参照するには。

[!code-csharp[Main](response/sample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

`ResponseCache` (メソッド) のアクションとコント ローラー (クラス) の両方に属性を適用できます。 メソッド レベルの属性は、クラス レベルの属性で指定された設定をオーバーライドします。

上記の例では、クラス レベルの属性は、メソッド レベルの属性は、継続時間が 60 秒に設定するキャッシュ プロファイルを参照中に 30 秒の期間を指定します。

結果として得られるヘッダー。

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a>その他の技術情報

* [指定から HTTP でのキャッシュ](https://tools.ietf.org/html/rfc7234#section-3)
* [キャッシュ制御](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* [応答キャッシュ ミドルウェア](xref:performance/caching/middleware)
