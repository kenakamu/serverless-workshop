# チャレンジ 4 - API Management

これまでのチャレンジで Best For You Organics Company (BFYOC) が必要とする API は揃いました。次にモバイルクライアントや Web アプリから呼び出せるよう、外部に公開する必要があります。また将来的には外部のパートナーにも API を公開したいと考えています。

## API ゲートウェイパターン

BFYOC は API を外部に直接公開して、クライアントとサービスが密結合になることを避けたいと考えています。また認証やロギング、統一されたアドレスなどの全 API に共通した問題を解決にも取り組む必要があります。BFYOC は [API ゲートウェイパターン](https://docs.microsoft.com/ja-jp/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern#what-is-the-api-gateway-pattern) をソリューションに適用する予定です。

![API Management](../Images/Challenge4.png)

上の図は [API Management](https://docs.microsoft.com/ja-jp/azure/api-management/) をバックエンド API のゲートウェイとする様子を示しています。

## チャレンジ

### API Management の作成

BFYOC は統一されたアドレスを使う API を作成するよう開発者であるあなたに依頼しました。現在はそれぞれの API で異なるアドレスが利用されていますが、全ての API は同じベースアドレスで公開されることが必要となります。

新しく API Management インスタンスを構築して上記要件を満たします:

* `従量課金プラン` を使用
  * ※現在従量課金プランが使えるリージョンは限られています
* API Management サービスでユニークな名前を指定

### 商品とフィードバック API のインポート

* [チャレンジ 2](..//challenge-2-Cosmos-DB-and-Azure-Functions/readme.md) で作成した Azure Functions を API Management サービスにインポート
* [チャレンジ 3](..//challenge-3-Logic-Apps/readme.md) で作成した Logic App を API Management サービスにインポート
* Azure ポータルでそれぞれの API をテスト

### フィードバック API のバージョニングとスロットリング

BFYOC はフィードバック API が頻繁に呼ばれることを懸念していて、一定時間で実行できる回数を制限したいと考えています。またこのスロットリング機能は別のバージョンとして公開して、現在の API には影響が無いようにしたいです。そのためには以下の作業が必要です。

* フィードバック API (Logic App) で新しいバージョンを作成して `v2` とする
* 10 秒間に 2 回までしか API が実行できないようにポリシーを追加
* ポリシーは operation スコープで作成

:bulb: 従量課金プランでは `rate-limit-by-key` によるスロットリングができないため、要求 `rate-limit` ポリシーを使用 [参考](https://docs.microsoft.com/ja-jp/azure/api-management/api-management-sample-flexible-throttling#custom-key-based-throttling)):

``` XML
<rate-limit calls="number" renewal-period="seconds" />
```

### API を外部公開する

API Management のバックエンド API の構成が完了したため、次に API の外部公開を行います。BFYOC は API がセキュアで高可用性を満たすようにしたいと考えています。

* `Product` を API Management サービスに作成して、サブスクリプションを必須にする
* 作成した product 用のサブスクリプションを作成する
* API を product を紐づける

## 達成条件

* API Management サービスが従量課金プランで構成されていることを確認
* [cURL](https://curl.haxx.se/) や [Postman](https://www.getpostman.com/) でサブスクリプションを指定して API を実行
* `v2` フォードバック API でスロットリングが機能するか確認

## ボーナスチャレンジ

### キャッシュサポートの追加

BFYOC の商品は頻繁に変わらないため、キャッシュを有効に利用できます。そこで BFYOC はあなたに以下の追加機能を依頼しました:

* Products API の Get に対してキャッシュを有効にする
* キャッシュは 60 秒有効とする

## 参考情報

* [Azure API Management サービスの新しいインスタンスの作成](https://docs.microsoft.com/ja-jp/azure/api-management/get-started-create-service-instance)
* [最初の API のインポートと発行](https://docs.microsoft.com/ja-jp/azure/api-management/import-and-publish)
* [API の複数のバージョンを発行する](https://docs.microsoft.com/ja-jp/azure/api-management/api-management-get-started-publish-versions)
* [Azure API Management の従量課金レベルの一般提供開始](https://azure.microsoft.com/ja-jp/updates/azure-api-management-consumption-tier-is-now-generally-available/)
* [API Management アクセス制限ポリシー](https://docs.microsoft.com/ja-jp/azure/api-management/api-management-access-restriction-policies#AccessRestrictionPolicies)
* [Azure API Management のサブスクリプション](https://docs.microsoft.com/ja-jp/azure/api-management/api-management-subscriptions)
* [API ゲートウェイ パターンと、クライアントからマイクロサービスへの直接通信との比較](https://docs.microsoft.com/ja-jp/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern)
* [Azure API Management で外部の Azure Cache for Redis を使用する](https://docs.microsoft.com/ja-jp/azure/api-management/api-management-howto-cache-external)
* [Azure API Management を使用してサーバーレス API の OpenAPI 定義を作成する](https://docs.microsoft.com/ja-jp/azure/azure-functions/functions-openapi-definition)

## 次のチャレンジ

API Management の構築が完了したら、次のチャレンジである [Event Grid](..//challenge-5-Event-Grid/readme.md) に進む。
