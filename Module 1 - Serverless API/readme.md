# モジュール 1 - サーバーレス API

BFYOC がサービスを提供するために、フランチャイズや支店が常に商品カタログに接続できる必要があります。そのため各店から商品や詳細を取得できる API が必要です。

このモジュールではローカルで Azure Function を開発し、その後クラウドに公開する方法を紹介します。この Azure Function が商品の詳細を公開するサーバーレス API となります。

## 前提条件

* Windows 10、 Mac OS X、 Mac OS X 10.12 以上が動作する PC
* Visual Studio Code や Visual Studio のような IDE
    > メモ: 利用言語や IDE は任意で利用できますが、より多くの環境をサポートできるようにサンプルは VS Code と JavaScript で提供されます。  
      * Visual Studio Code を使う場合は、最新版を使ってください。 <a href="https://code.visualstudio.com/tutorials/functions-extension/getting-started" target="_blank">こちらの **最初のページ** にある前提条件</a> を満たしてから Azure Functions 拡張機能を構成します。  
      * Windows で Visual Studio を使う場合は <a href="https://www.visualstudio.com/vs/" target="_blank">最新の Visual Studio</a> で `Azure 開発ワークロード` がインスールされている事を確認してください。  
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
* [NodeJS 8 (LTS) または 10 (Current)](https://nodejs.org/en/download/)
* [Azure Functions Core Tools v2](https://github.com/Azure/azure-functions-core-tools#installing)  

## チャレンジ

Product ID を GET で渡せる Azure Function をローカルで実行してテストできるようにします。結果として渡した Product ID に関する商品の詳細を返してください。例えば以下のような HTTP 要求を送った場合:

```
GET http://{myFunctionEndpoint}/api/products?id=1
```

以下の結果を返します:

```json
{
  "id": "1",
  "flavor": "Rainbow Road",
  "price-per-scoop": 3.99
}
```

このチャレンジでは静的なデータを返すようにするため、データベースに接続する必要はありません。ローカルで実行した後は Azure に公開してください。

**Azure Function の開発は v2 ランタイムを利用してください。**

### Tips

1. 前提条件を満たしているか確認。  
1. 新規の Azure Function プロジェクトを作成して、HTTP テンプレートを選択。
    * HTTP テンプレートは、クエリパラメーターで渡した name の値を使って、"Hello, {name}" を返す。
    * クエリパラメーターの `id` が `1` の場合に、上記の `json` ペイロードを返す用に変更。
1. 他の情報は[ドキュメント](#ドキュメント)参照。

### 詳細な手順

<details><summary>クリックして開く</summary><p>

1. Visual Studio Code を開く。
1. 左のナビゲーションにある Extensions アイコンをクリックして、 **Azure Functions** 拡張がインストールされているか確認。
1. 左のナビゲーションにある **Azure** アイコンをクリック。
1. **Functions** を展開して、Azure にサインインしていることを確認。
1. フォルダアイコンをクリックして新しいプロジェクトを作成。アプリを作成する場所を聞かれるので任意のフォルダを選択。
1. ランタイムを聞かれた場合は、"beta" または "~2" を選択。言語は "JavaScript" を選択。
1. **HTTP Trigger** をトリガーとして選択。名前は "products" とする。
1. 承認レベルは **Anonymous** を選択。**Function** を選択した場合は公開後にキーをヘッダーかクエリパラメーターに渡す必要がある。
1. **Open in new window** を選択。
1. 以下のようなテンプレートを含むプロジェクトが作成される:

    ```javascript
    module.exports = async function (context, req) {
        context.log('JavaScript HTTP trigger function processed a request.');
    
        if (req.query.name || (req.body && req.body.name)) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + (req.query.name || req.body.name)
            };
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
    };
    ```

    >重要: `async` キーワードが無いなどテンプレートが異なる場合、~1 ランタイムを選択した可能性があるので、ツールを最新にしてからプロジェクトを再作成

1. コードを以下のように変更して、意図した結果を返す:

    ```javascript
    module.exports = async function (context, req) {
        context.log('JavaScript HTTP trigger function processed a request.');
    
        if (req.query.id) {
            if (req.query.id == "1") {
                context.res = {
                    status: 200,
                    body: {
                        id: "1",
                        flavor: "Rainbow Road",
                        "price-per-scoop": 3.99
                    }
                }
            }
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass in an id query parameter"
            }
        }
    };
    ```

    尚、`index.js` ファイルと同じ階層に `function.json` ファイルがあり、ここに利用するトリガーやバインドの情報が設定されるため HTTPTrigger があるか開いて確認。

1. 上部メニューの **デバッグ** より **デバッグの開始** をクリック。

    ターミナルウィンドウで Azure Functions のランタイムが実行される様子を確認。コードが正しい場合、`http://localhost:7071/api/products` という API のアドレスが表示される。

1. ランタイムが起動している状態で、表示されたアドレスをクリック。クエリにid を付与して実行。例えば `http://localhost:7071/api/products?id=1` というクエリにして実行した場合以下の結果が返ることを確認:

    ```json
    {
        "id": "1",
        "flavor": "Rainbow Road",
        "price-per-scoop": 3.99
    }
    ```

1. 動作を確認したらデバッグを停止。
1. 最後に Azure に開発したアプリを公開。左側の Azure Function のメニューより、上矢印をクリックしてアプリケーションを発行。**Create New Function App** を選択して *グローバルで*ユニークな名前を指定。任意のリージョンを選択し、公開。    
1. 右下にアプリ公開のインジケーターが表示される。公開が完了したら Azure ポータルから作成した**関数アプリ**を確認。公開した関数を開き、products 関数から **関数の URL の取得** リンクをクリックして URL をコピー。ブラウザを使ってテスト。(id パラメーターを忘れず渡す)

Congratulations! これで開発した API をクラウドに公開できました。

</p></details>

## ドキュメント

* <a href="https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-overview" target="_blank">Azure Functions の概要</a>
* <a href="https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-test-a-function" target="_blank">Azure Functions のコードをテストするための戦略</a>
* <a href="https://code.visualstudio.com/tutorials/functions-extension/getting-started" target="_blank">Creating Azure Functions from Visual Studio Code</a> - このチュートリアルはJavaScript 用ですが、C# でも同じように使えます。
* <a href="https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-develop-local" target="_blank">Azure Functions をローカルでコーディングしてテストする</a>
* <a href="https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-run-local" target="_blank">Azure Functions Core Tools の操作</a>