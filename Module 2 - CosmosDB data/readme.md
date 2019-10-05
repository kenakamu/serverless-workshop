# モジュール 2 - Cosmos DB の追加

このモジュールでは、モジュール 1 で開発したアプリに実際のデータを取得するロジックを追加します。これにより動的に在庫の状況を取得したり更新することができます。バックエンドのデータベースは Cosmos DB を利用します。

## 前提条件

* モジュール 1 のアプリが動作すること。answers フォルダにコードがあるため自由に利用してください。
* Azure Cosmos DB 拡張と [Azure Storage Explorer](https://azure.microsoft.com/ja-jp/features/storage-explorer/) のインストール
* HTTP テストツールである [Postman](https://www.getpostman.com/apps) のインストール。※ curl でも可。

## チャレンジ

Cosmos DB に接続された API を 2 つ開発する。まず以下の API をコールした場合:

```
GET http://{myFunctionEndpoint}/api/products?id=1
```

以下結果が返る:

```json
{
  "id": "1",
  "flavor": "Rainbow Road",
  "price-per-scoop": 3.99
}
```

ただし結果は Cosmos DB から動的に取得すること。また以下の POST 操作で新しい商品が追加できる。

```
POST http://{myFunctionEndpoint}/api/addProducts
```

```json
{
 "id": "2",
 "flavor": "Coco Mountain",
 "price-per-scoop": 2.99
}
```

結果は Cosmos DB に格納されること。

### Tips

1. まず Azure Cosmos DB データベースを作成。
1. "Azure Cosmos DB バインド" のドキュメントからサンプルを確認。インプットバインディングも追加可能。
1. インプットバインディングで {Query.id} という形式で利用したい場合、`"id": "{Query.id}"` を利用。
1. `addProducts` 関数を追加する必要がある。初めの関数同様 HTTP トリガーだが、Cosmos DB アウトプットバインディングを利用。
1. 他の情報は[ドキュメント](#ドキュメント)参照。

### 詳細な手順

<details><summary>クリックして開く</summary><p>

1. Azure ポータルを開いて、Azure Cosmos DB リソースを追加。**SQL API を選択。VNET は使わない**.
    * 必須ではないが、モジュール 1 で作成した関数アプリと同じリソースグループに追加することを推奨。
1. Cosmos DB 作成完了後、**キー** セクションよりプライマリ接続文字列を確認して保存しておく。作成に時間がかかる事もあるので、次のステップに進んで後から確認してもよい。

    以下 VS Code での作業

1. モジュール 1 で作成したプロジェクトを開く。
1. products/index.js を Cosmos DB バインディングからデータをとれるように変更。

    ```javascript
    module.exports = async function (context, req, product) {
        context.log('JavaScript HTTP trigger function processed a request.');
    
        if (req.query.id && product) {
            context.res = {
                status: 200,
                body: product
            }
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass in a valid \"id\" query parameter"
            }
        }
    };
    ```

    引数に Cosmos DB から取得する product を追加。product はバインディングの機能を利用して呼ばれたタイミングで動的に生成される。

1. 上記コードの product をバインディングから取得できるように `function.json` のコードを変更:
    ```json
    {
      "disabled": false,
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        },
        {
          "type": "CosmosDB",
          "name": "product",
          "databaseName": "icecream",
          "collectionName": "products",
          "connectionStringSetting": "CosmosDBConnectionString",
          "direction": "in",
          "Id": "{Query.id}"
        }
      ]
    }
    ```  

    新しく `CosmosDB` バインディングを追加。プロパティの名前として `product` を指定していて、これが引数の変数名と一致する。Cosmos DB への接続はデータベース名 (`icecream`)、コレクション名 (`products`)、接続文字列の指定で行い、`direction` が `in` であることからデータを取得するクエリを作成する。

    `Id` プロパティで指定されている `{Query.id}` は、トリガーである Http 要求のクエリーまたは Body に含まれる値を取得する構文で、ことにより「Cosmos DB コレクションより Id と同じ `id` を持つドキュメントを取得する」という意味になる。このようにバイディングはデータベースに対するクエリをすべて担うことができ、関数で取得後のデータを受け取れるためコーディングが非常にシンプルになる。

    現時点ではコレクションやデータは無いためこの関数を実行することはできない。

1. 次に商品を追加する API を開発。VS Code の Azure Functions 拡張メニューより「稲妻アイコン」をクリックして新規関数を追加。
1. 現在のフォルダを選択して、`HTTP trigger` を選択。
1. 名前は `addProducts`、承認レベルを `anonymous` にして作成。
1. 新しく追加された `addProducts` フォルダの `index.js` を以下のコードに変更:

    ```javascript
    module.exports = async function (context, req) {
        context.log('Add product processed a request.');
    
        context.bindings.product = req.body;
    };
    ```

    コードの量は非常に少ないが、これは　HTTP 要求のボディを、設定するバインディングに渡すだけで、裏では Cosmos DB のドキュメントが作成されるようにできるため。

1. 次にバインディングを作成するため、同じフォルダの `function.json` を以下のコードに変更:

    ```json
    {
      "disabled": false,
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "post"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        },
        {
          "name": "product",
          "type": "CosmosDB",
          "databaseName": "icecream",
          "collectionName": "products",
          "createIfNotExists": true,
          "connectionStringSetting": "CosmosDBConnectionString",
          "direction": "out"
        }
      ]
    }
    ```

    インプットバインディングと同様に、データベース名、コレクション名、接続文字列があり、名前も `product` と設定。この名前が先ほどのコードの `context.bindings.product` と一致する。今回 `direction` は out のため、受け取ったデータを書き込む処理を実施。その際、`createIfNotExists` プロパティにより同じデータが存在した場合の挙動を制御している。

    尚、接続文字列として `CosmosDBConnectionString` という文字列を何度か利用しているが、この値は `local.settings.json` ファイルより環境変数として取得できる仕組みとなっている。

1. 接続文字列を環境変数として渡せるよう `local.settings.json` ファイルを編集。現在は以下のようになっている:

    ```json
    {
      "IsEncrypted": false,
      "Values": {
        "AzureWebJobsStorage": "",
        "FUNCTIONS_WORKER_RUNTIME": "node"
      }
    }
    ```

    開発した関数が動作するためには、`AzureWebJobsStorage` と `CosmosDBConnectionString` の設定が必要。`AzureWebJobsStorage` は関数実行時にステートの保存をしたり、Cosmos DB のような外部サービスをバインディングとして利用する際に必要。`CosmosDBConnectionString` は Cosmos DB に対する接続文字列を格納。

1. Azure ポータルを開き、モジュール 1 で作成した関数があるリソースグループを開く。「ストレージ アカウント」が作成されているのでクリックし、**アクセスキー** メニューより key 1 の接続文字列をコピー。`local.settings.json` ファイルの `AzureWebJobsStorage` に値を張り付け。
1. `Values` に `CosmosDBConnectionString` を追加。最初の手順で取得した Cosmos DB の接続文字列を張り付け。
1. 現時点で `local.settings.json` ファイルは以下のようになっている:
    
    ```json
    {
        "IsEncrypted": false,
        "Values": {
          "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=amazing-lab;AccountKey=ShhhThisIsASecret;EndpointSuffix=core.windows.net",
          "CosmosDBConnectionString": "AccountEndpoint=https://awesome-function-lab.documents.azure.com:443/;AccountKey=Thisisasecret;",
          "FUNCTIONS_WORKER_RUNTIME": "node"
        }
      }
      
    ```

1. **デバッグ** メニューより **デバッグの開始** をクリック。
    > 初回実行時には HTTP とタイマートリガー以外のバインディング以外は拡張機能がインストールされる。最新の VSCode であれば自動でインストールが完了するが、問題があった場合はプロジェクトのルートフォルダで、手動で `func extensions install` を実行してインストールすることも可能。

    ランタイム実行時には以下のようなアドレスが表示される:

    > Http Functions:
    > addProducts: http://localhost:7071/api/addProducts
    > products: http://localhost:7071/api/products

1. Postman を開き、新しい商品を登録。  
    1. `POST` 要求を `http://localhost:7071/api/addProducts` に対して追加。
    1. **Body** で **raw** を選択し、タイプとして **JSON (application/json)** を選択。
    1. 以下の JSON を追加:

    ```json
    {
         "id": "2",
         "flavor": "Coco Mountain",
         "price-per-scoop": 2.99
    }
    ```  

    ![](media/postman.png)  

1. 要求を送信して、ステータスコード 200 が返ることを確認。Cosmos DB Visual Studio 拡張か Azure ポータルから Cosmos DB のデータを表示。データベース、コレクションおよび商品が 1 つ追加されていることを確認。
1. 別の商品を追加。
    ```json
    {
         "id": "1",
         "flavor": "Rainbow Road",
         "price-per-scoop": 3.99
    }
    ```

    Cosmos DB にも新しいドキュメントが追加されていることを確認。

1. Postman で両方の商品をクエリ
    1. `GET` 要求を `http://localhost:7071/api/products?id=2` と `http://localhost:7071/api/products?id=1` に送信
    1. 以下のようなドキュメントが Cosmos DB から返ることを確認。(Cosmos DB が追加している独自プロパティも返る)
    
    ```json
    {
        "id": "2",
        "_rid": "Z7l8ALN6PzQBAAAAAAAAAA==",
        "_self": "dbs/Z7l8AA==/colls/Z7l8ALN6PzQ=/docs/Z7l8ALN6PzQBAAAAAAAAAA==/",
        "_ts": 1536957687,
        "_etag": "\"6200bebd-0000-0000-0000-5b9c1cf70000\"",
        "flavor": "Coco Mountain",
        "price-per-scoop": 2.99
    }
    ```

    これで関数が完成したので、Azure に公開します。

1. デバッグ停止後、Azure Functions 拡張機能より、「上矢印アイコン」をクリックして発行。
1. 現在のフォルダをソースに、モジュール 1 で作成した関数アプリをターゲットに公開。
    1. 画面右下に更新中のインジケーターが表示される。
1. 最後にローカルの設定で変更した内容をクラウド側のアプリケーションの設定でも変更。VS Code の Azure Functions 拡張より、Azure 上の関数アプリを展開。`Application Settings` 右クリックから **Upload local settings..** をクリック。これでローカルの設定が公開される。

    ![](media/uploadfromlocal.png)

1. Azure ポータルで公開した関数を開き、URL を確認。公開済のアプリも同様に動作することを確認。

</p></details>

## ドキュメント

* [Azure Cosmos DB の概要](https://docs.microsoft.com/ja-jp/azure/cosmos-db/introduction)
* [Azure Functions 2.x の Azure Cosmos DB バインド](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-bindings-CosmosDB-v2)
* [Azure Cosmos DB Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-CosmosDB)
