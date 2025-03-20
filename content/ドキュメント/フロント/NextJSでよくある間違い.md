+++
title = "NextJSでよくある間違い"
weight = 1
+++

## 概要
vercel公式のYoutubeの内容を翻訳・要約しています  
https://www.youtube.com/watch?v=RBM03RihZVs

## NG1. 不要なルートハンドラーを呼び出す

### 概要
ルートハンドラーとは特定のURLパスに対して  
HTTPリクエスト(GET,POST,PUT, DELETEなど)を処理するためのNextJSの機能で、  
Restful APIとして機能します。  
  
自分はフロントと独立したAPI構成（Expressなど）のイメージで  
データアクセスなどをこの機能にまとめるイメージをもってしまったのですが、  
NextJSではServer Componentに関数を記述してデータアクセスをしたほうが  
以下の理由でシンプルで効率的です。
1. 関数を呼び出すだけでよい（fetchで完全なURLをハードコーディングする必要がない）
2. 不要なネットワークリクエストが発生しない  

また後述のNG2もルートハンドラと関連しています。

### NGコード例
ルートハンドラーでデータ取得するAPIを実装し、  
クライアント側でfetchでAPIを呼び出す
``` typescript
// app/api/hoge/route.ts

// https://server:port/api/hoge に対するGETリクエストに対して  
// { hello: "world" }のJSONを返す
export async function GET(request: Request){
    return Response.json({ hello: "world" });
}
```
``` typescript
// app/hoge/page.ts

// ルートハンドラーで記述したAPIをフェッチで呼び出しデータを取得する
export default async function Page(){
    let data = await fetch('http://localhost:3000/api/hoge');
    let json = await data.json();
    return <h1>{JSON.stringify(json)}</h1>;
}
```

### 推奨コード例
サーバーコンポーネント
``` typescript
// app/api/hoge/route.ts

// https://server:port/api/hoge に対するGETリクエストに対して  
// { hello: "world" }のJSONを返す
export async function GET(request: Request){
    return Response.json({ hello: "world" });
}
```
``` typescript
// app/hoge/page.ts

// ルートハンドラーで記述したAPIをフェッチで呼び出しデータを取得する
export default async function Page(){
    let data = await fetch('http://localhost:3000/api/hoge');
    let json = await data.json();
    return <h1>{JSON.stringify(json)}</h1>;
}
```

## NG2. route handlerのGetのキャッシュに関する勘違い
route handlerのGETはローカル開発環境では毎リクエストごとに実行され、  
プロダクション環境ではデフォルトでキャッシュされるような仕様になっています。  
プロダクション環境でキャッシュされるのは、  
外部データなどの取得が必ずしも毎リクエストで実行されるべきという訳ではないからです。  
（pre-renderの段階（最初の画面表示前）で取得しておけば良いデータだってあります）  
Dynamicに設定しておけば毎リクエストごとに動作するようですが、  
このローカル開発環境とプロダクション環境での動作の違いを理解しておく必要があります。  

## NG3. Client Componentを使うときもRoute Handlerを使う必要はない
クライアントコンポーネントでも、actionを使えば  
Route Handler（Fetch）を呼び出す必要はありません。  

### NGコード例
``` tsx
"use client";

export default function Page() {
    function onSubmit(event: React.FormEvent<HTMLFormElement>){
        event.preventDefault();
        console.log('Submitted!');
    }
  return (
    <>
      <form onSubmit={onSubmit}>
      <button>Send It</button>
    </>
  );
}
```
### 推奨コード例
``` tsx
"use client";
import { send } from "./actions";

export default function Page() {
  return (
    <>
      <form onSubmit={onSubmit}>
      <button>Send It</button>
    </>
  );
}
```
**actions.ts**
``` tsx
"use server";

export async function send(){
    console.log("Send it!");
}
```