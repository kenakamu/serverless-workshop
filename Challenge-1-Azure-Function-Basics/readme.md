# チャレンジ 1 - Azure Function の作成とデプロイ

Best For You Organics Company (BFYOC) はサーバーレスでの開発を行うにあたり、Azure Functions から試すことにしました。そのため、まずローカルでの開発とテストができるように、開発環境とツールを構成する必要があります。

![Functions](../Images/challenge1.png)

このチャレンジでは、Azure Function の作成とデプロイを行います。

## チャレンジ

### ローカルの関数を作成する

初めのゴールはローカルで HTTP トリガーの Azure Function の開発とデバッグを行うことです。開発する関数の仕様は以下の通りです:

* **関数名**: `GetProduct`
* **HTTP Verb**: GET
* **インプットパラメーター**: `productId` をクエリパラメーターまたは HTTP パスの一部として渡す
* **応答**: productId に対応する製品名。ここでは Starfruit Explosion を利用
* **サンプル要求**:

    ``` HTTP
    GET http://localhost:7071/api/<your-function-name>?productId=2424
    ```

### Azure へのデプロイ

次のゴールは Azure サブスクリプションにリソースグループを作成し、Azure Functions をデプロイすることです。以下の条件を満たすようにしてください:

* **関数アプリの作成**: 従量課金プランの関数アプリを新規に作成
* **デプロイとテスト**: ローカルで開発した関数をデプロイしてテストを実施

**関数名はユニークなものを使うようにしてください**

## 達成条件

このチャレンジを達成するために、以下条件を満たす必要があります:

* ローカルで開発した Azure Function が HTTP GET によって `productId` を受け取れ、その結果を表示できることを提示すること。

* ローカルで開発した関数を Azure にデプロイできたことおよび実行状況を提示すること。

## 参考

* [Azure Functions の概要](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-overview)
* [Azure Functions でサポートされている言語](https://docs.microsoft.com/ja-jp/azure/azure-functions/supported-languages) より利用する言語のガイドを確認
* [Azure Functions のコードをテストするための戦略](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-test-a-function)
* [Azure Functions をローカルでコーディングしてテストする](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-develop-local)
* [Azure Functions のトリガーとバインド](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-bindings-http-webhook)
* [Visual Studio Code を使用して Azure Functions を開発する](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-develop-vs-code)
* [Visual Studio を使用して初めての関数を作成する](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-create-your-first-function-visual-studio)
* [Java と IntelliJ を使用して初めての Azure 関数を作成する](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-create-maven-intellij)
* [Java と Eclipse を使用して初めての関数を作成する](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-create-maven-eclipse)
* [クイック スタート:Java を使用して関数を作成し、Azure Functions に発行する](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-create-first-java-maven)
* [Python: Azure で HTTP によってトリガーされる関数を作成する](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-create-first-function-python)
* [Azure で初めての PowerShell 関数を作成する (プレビュー)](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-create-first-function-powershell)

## 次のチャレンジ

関数が動作したら、次のチャレンジである [Cosmos DB と Azure Functions](..//Challenge-2-Cosmos-DB-and-Azure-Functions/readme.md) に進んでください。
