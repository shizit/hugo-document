+++
title = "NextJSでよくある間違い"
weight = 4
+++

## 概要
vercel公式のYoutubeの内容を翻訳・要約しています  
https://www.youtube.com/watch?v=RBM03RihZVs

## NG1. 不要なルートハンドラーの実装

### 概要
ルートハンドラーとは   
HTTPリクエスト(GET,POST,PUT, DELETEなど)を処理するためのNextJSの機能です。  

自分はフロントと独立したAPI構成（Expressなど）のイメージで  
データアクセスなどをこの機能にまとめるイメージをもってしまったのですが、  
NextJSではServer Componentに関数を記述してデータアクセスをしたほうが  
以下の理由でシンプルで効率的です。
1. 関数を呼び出すだけでよい（fetchで完全なURLをハードコーディングする必要がない）
2. 不要なネットワークリクエストが発生しない  

### NGコード例
ルートハンドラーでデータ取得するAPIを実装し、  
クライアント側でfetchでAPIを呼び出す
``` tsx
import { NextRequest } from "next/server";
// app/api/hoge/route.ts
export async function GET(request: NextRequest){
    // 何かしらのデータ取得処理を行う
    return Response.json({ hello: "world" });
}
```
``` tsx
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
``` tsx
// app/hoge/hoge.ts
'use server';
export async function getUser(){
  return { hello: "world"};
};
```
クライアントコンポーネント
``` tsx
// app/hoge/page.ts
'use client'
import { useEffect, useState } from 'react';
import { getUser } from '@/app/hoge/hoge';

interface UserData {
  hello: string;
}

export default function Page() {
  const [data, setData] = useState<UserData>({hello: ""});

  useEffect(() => {
    const fetchData = async () => {
      const userData = await getUser();
      setData(userData);
    };
    fetchData();
  }, []);

  return <h1>{data.hello}</h1>;
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

## NG3. Client Component側でデータ取得などの処理を実行する
クライアントコンポーネントでも、actionを使えば  
Route Handlerを呼び出したりする必要はありません。  
（サーバーコンポーネント側でそういった処理を実行できます）

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
      <form action={send}>
      <button>Send It</button>
    </>
  );
}
```
``` tsx
// actions.ts
"use server";

export async function send(){
    console.log("Send it!");
}
```

# NG4 Suspenseの記述場所
Suspenseは子要素の読み込みが完了するまで  
ローディング画面などのフォールバックを表示できる機能です。  
このSuspenseを記述する場所は、データフェッチングしているコンポーネントより上の階層である  
必要があります。  
### NGコード例
``` tsx
import { Suspense } from "react";

async function BlogPosts(){
  let res = await fetch('https://api.vercel.app/blog');
  let posts = await res.json();
  return (
    // Suspenseをデータフェッチングより下の階層で記述してしまっている
    <Suspense fallback={<h2>Loading...</h2>}>
      <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </Suspense>
  );
}

export default function Page() {
  return (
    <section>
      <h1>My Blog</h1>
      <BlogPosts />
    </section>
  );
}
``` 

### 推奨コード例
``` tsx
import { Suspense } from "react";

async function BlogPosts(){
  let res = await fetch('https://api.vercel.app/blog');
  let posts = await res.json();
  return (
    <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
  );
}

export default function Page() {
  return (
    <section>
      <h1>My Blog</h1>
    // Suspenseをデータフェッチングより上の階層に記述する
    <Suspense fallback={<h2>Loading...</h2>}>
      <BlogPosts />
    </Suspense>
    </section>
  );
}
``` 