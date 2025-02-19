+++
title = "NextJsで実装するAPI"
weight = 1
+++

## 概要
NextJsではフロントエンドだけでなく、  
Fetch APIのリクエスト、レスポンスに対応したリクエストハンドラーを作成することができます。  
（Httpメソッドに対応したResutful APIを作成可能です）

{{% notice style="info" title="Fetch APIとは（chatgptまとめ）" %}}
JavaScriptでHTTPリクエストを送信するためのモダンで使いやすいAPIです。  
Webブラウザでサーバーからデータを取得したり、サーバーにデータを送信したりするために使用されます。  
従来のXMLHttpRequest（XHR）よりも簡潔でPromiseベースで非同期処理が行えるため、  
より直感的に使うことができます。 
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


