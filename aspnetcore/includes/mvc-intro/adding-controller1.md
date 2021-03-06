モデル ビュー コントローラー (MVC) アーキテクチャ パターンでは、アプリが**モ**デル、**ビ**ュー、**コ**ントローラーという 3 つの主要なコンポーネントに分けられます。 MVC パターンでは、よりテスト可能で、従来のモノリシック アプリより更新しやすいアプリを作成できます。 MVC ベースのアプリには以下が含まれます。

* **モ**デル: アプリのデータを表すクラス。 モデル クラスでは検証ロジックを使用して、そのデータにビジネス ルールを適用します。 通常、モデル オブジェクトはモデルの状態を取得して、データベースに格納します。 このチュートリアルでは、`Movie` モデルはデータベースからムービーデータを取得し、それをビューに提供するか、更新します。 更新されたデータはデータベースに書き込まれます。

* **ビ**ュー: ビューは、アプリのユーザー インターフェイス (UI) を表示するコンポーネントです。 一般に、この UI ではモデル データが表示されます。

* **コ**ントローラー: ブラウザー要求を処理するクラス。 モデル データを取得し、応答を返すビュー テンプレートを呼び出します。 MVC アプリでは、ビューに情報のみが表示され、コントローラーがユーザーの入力と操作を処理して応答します。 たとえば、コントローラーはルート データとクエリ文字列の値を処理し、これらの値をモデルに渡します。 モデルはこれらの値を使用して、データベースを照会する場合があります。 たとえば、`http://localhost:1234/Home/About` には、`Home` (コントローラー) と `About` (ホーム コントローラーに対して呼び出すアクション メソッド) のルート データがあります。 `http://localhost:1234/Movies/Edit/5` は、ムービー コントローラーを使用して ID=5 のムービーを編集する要求です。  ルート データについては、このチュートリアルの後半で説明します。

MVC パターンは、これらの要素間の疎結合を提供しながら、アプリのさまざまな側面 (入力ロジック、ビジネス ロジック、および UI ロジック) を分離するアプリを作成するのに役立ちます。 このパターンは、アプリで各種類のロジックを配置する必要がある場所を指定します。 UI ロジックはビューに属しています。 入力ロジックはコントローラーに属しています。 ビジネス ロジックはモデルに属しています。 このように分離することで、他のコードに影響を与えることなく、実装の 1 つの側面に専念できるため、アプリを構築するときの複雑さが管理しやすくなります。 たとえば、ビジネス ロジック コードに依存することなく、ビュー コードに専念できます。

このチュートリアルで示すこれらの概念を使用して、ムービー アプリを構築する方法を示します。 MVC プロジェクトには、*Controllers* と *Views* の各フォルダーが含まれています。 *Models* フォルダーは後の手順で追加されます。

