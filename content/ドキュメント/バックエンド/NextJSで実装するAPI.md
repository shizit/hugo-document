+++
title = "NextJSで実装するAPI"
weight = 1
+++

## 概要
NextJsではフロントエンドだけでなく、  
Fetch APIのリクエスト、レスポンスに対応したリクエストハンドラーを作成することができます。  
（Httpメソッドに対応したResutful APIを作成可能です）
※こちらの機能はroute handlerと呼ばれていますが、使うケースを限定するほうが良いようです。  
  ドキュメント/フロントの「NextJSでよくある間違い」を参照ください

{{% notice style="info" title="Restful API・Fetch API（chatgptまとめ）" %}}
RESTful API は、HTTP メソッド（GET、POST、PUT、DELETE）を使ってデータリソースを操作するための
 Web API の設計スタイルです。
Fetch API (JavaScript) は、JavaScript で非同期に HTTP リクエストを送信し、
レスポンスを処理するためのモダンな Web API です。 
{{% /notice %}}

## 実装方法（app routerの場合）
プロジェクトのapp/api下にroute.ts（または route.js）を作成しHTTPメソッド名の関数を記述すると、  
そのメソッドでのリクエストに対して記述した関数が呼び出される。  
呼び出される際のAPIパスはフォルダ構成と同じになる。  
（app/api/hoge/route.tsにGETを記述した場合、xxx/api/hoge　となる）

``` ts
// app/api/hoge/route.ts
export async function GET(request: Request) {
    const data = { "this is response" }
    return Response.json({ data })
}
// ※関数名は大文字でないと認識してくれない
```

## 対応するHTTPメソッド
- GET
- POST
- PUT
- PATCH
- DELETE
- HEAD
- OPTIONS

## キャッシュについて
デフォルトではキャッシュはOFFになっているが、  
GETメソッドについてのみキャッシュをONに設定できる。  
詳細は公式ドキュメント参照

## 公式ドキュメント
https://nextjs.org/docs/app/building-your-application/routing/route-handlers


