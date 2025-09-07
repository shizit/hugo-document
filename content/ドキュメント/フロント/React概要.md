+++
title = "React概要"
weight = 1
+++

## 概要
ReactはMeta（旧Facebook）が開発したUI構築用のJavascriptライブラリで、  
主にWebアプリケーションのユーザーインターフェースを効率的に作成するために利用されます。  
公式サイト:  
https://ja.legacy.reactjs.org/

## 特徴

### 1.宣言型UI
#### 宣言型UI（declarative UI）とは
宣言型UIとは「画面がどうあるべきか」を宣言するスタイルです。
Reactでは「状態変数とUIの対応関係」を宣言しておくだけで  
状態が変化したときにReactが自動的にUIを再レンダリングし、必要な部分だけDOMを更新します（=宣言型UI）。  
以下のコード例でいうと、countという状態変数を宣言し{count}をreturn内に記述すると
countに変更が加わったときにUIに表示される{count}の値を変更します。

``` 
import { useState } from "react";

function Counter() {
const [count, setCount] = useState(0);

return (
    <div>
    <p>現在のカウント: {count}</p>
    <button onClick={() => setCount(count + 1)}>＋1</button>
    </div>
);
}
```

※状態変数 → UI（DOM） の流れは自動ですが  
UI（DOM） → 状態変数 の流れは、イベントハンドラなどを通して明示的に設定する必要があります。  
例えば以下のコード例でtextの値が変わった場合はvalue{text}の記述によりがUIの表示が更新されますが、  
UIでinput内の文字を変更した場合、  
onChangeの記述がなければtextの値は更新されません。  
（=一方向データバインディング）

``` 
import { useState } from "react";

function TextInput() {
  const [text, setText] = useState("");

  return (
    <div>
      <input
        value={text}                // 状態変数 → DOM
        onChange={(e) => setText(e.target.value)} // DOM → 状態変数
      />
      <p>入力内容: {text}</p>
    </div>
  );
}
```

#### ※命令型UI（imperative UI）とは
命令型UIとは「どうやって画面を変えるか」を記述するスタイルです。  
React登場以前はDOMを直接操作して値を書き換える必要がありました。  
Reactと同じようなカウントの処理を従来（命令型UI）の記述で書くと以下のようになります。  
``` 
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>命令型UIの例</title>
</head>
<body>
  <div>
    <p id="counter">現在のカウント: 0</p>
    <button id="btn">＋1</button>
  </div>

  <script>
    let count = 0;

    // DOM要素を直接参照
    const counterElement = document.getElementById("counter");
    const buttonElement = document.getElementById("btn");

    // 値を更新するためにDOMを更新するメソッドを記述する
    buttonElement.addEventListener("click", () => {
      count += 1; // 値を変更
      // counterElement（idがcounterのDOM要素）のtextContentの値を書き換える
      counterElement.textContent = "現在のカウント: " + count; // DOMを直接更新
    });
  </script>
</body>
</html>
```

#### なぜ宣言型のほうがよいのか
##### 1. コードのみやすさ、記述量の少なさ
**1-1. 動的に制御される部分がわかりやすい**  
例えば上の命令型UIのコード例でいうと  
画面描画されている部分だけをみると動的に更新されるのかどうかわからず、  
``` 
    <p id="counter">現在のカウント: 0</p>
    <button id="btn">＋1</button>
```
以下のスクリプトまで見て初めて動的にデータが更新されることがわかります。  
```  
    // DOM要素を直接参照
    const counterElement = document.getElementById("counter");
    const buttonElement = document.getElementById("btn");

    // 値を更新するためにDOMを更新するメソッドを記述する
    buttonElement.addEventListener("click", () => {
      count += 1; // 値を変更
      // counterElement（idがcounterのDOM要素）のtextContentの値を書き換える
      counterElement.textContent = "現在のカウント: " + count; // DOMを直接更新
    });
``` 
Reactの場合は以下のように{}で囲まれているのでぱっと見でわかります。  
``` 
<p>入力内容: {text}</p>
```

**1-2. UI描画の記述が分散しにくい**  
例えば動的にdivを追加する場合、  
命令型の場合document.createElementやappendChildなどで要素を追加していきます。  
⇒HTMLとjavascriptにUIの描画の処理が分散され、全体像が見えにくくなります。  
  （条件分岐などが入ってきたり、いろんなイベント（初期表示、ボタンクリック、etc...）  で更新されだすと余計わかりづらくなります、、）
``` 
<div id="container"></div>
<script>
  const container = document.getElementById("container");
  for (let i = 0; i < 3; i++) {
    const div = document.createElement("div");
    div.textContent = "項目 " + (i + 1);
    container.appendChild(div);
  }
  
</script>
```
Reactの場合はUIの構造（htmlライクな記述）の中にそのまま動的に描画する処理を記述できるため、  
どんなUIになるかが想像しやすいです。
``` 
function ItemList() {
  const items = ["項目 1", "項目 2", "項目 3"];

  return (
    <div>
      // この部分だけ見れば動的にどう描画されるかわかる
      {items.map((item, index) => (
        <div key={index}>{item}</div>
      ))}
    </div>
  );
}
```
**1-3. コード量が減る**  
Domにアクセスする記述もReactでは不要となります。  
更新するUIが少しなら良いですが、一画面を動的に変えようとすると  
命令型UIでは記述量がかなり増えます。
``` 
const counterElement = document.getElementById("counter");
```

##### 2.画面とデータの不整合（=バグ）が起きにくい
命令型UIでは、状態と画面が同期していない不整合が起こりやすいです。  
例えば画面の3箇所（ヘッダー、メニュー、画面本体など）に同じ値（ユーザー名）を表示するような仕様があったとして、  
命令型UIだと値を更新するたびに都度ヘッダー、メニュー、画面本体のDOMを書き換える処理を記述する必要があります。  
（=3箇所の値がバラバラになりうるような構造になっています。）  
一方、宣言型UIでは同じ状態変数を3箇所に記述しておけば必ず同期が保たれます。  


### 2.コンポーネント指向
ReactではUIを「小さな部品（コンポーネント）」に分割して組み合わせる設計になっています。  
各コンポーネントは独立しており、再利用やテストがしやすく、  
大規模開発でも見通しの良い構造を作れます。

- 状態とUIを連動させて宣言的に扱える
``` 
// React コンポーネント例
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```
- 宣言的に組み合わせ可能
``` 
<App>
  <Header />
  <Sidebar />
  <MainContent />
</App>
```

### 3. 仮想DOMによる効率的なUI更新
Reactでは仮想DOM（DOMのコピー）を利用し更新の最適化をします。

#### なぜ仮想DOMを使うのか  
DOM要素を直接追加・削除・更新すると、更新したDOM以外にも  
再描画（repaint）や再レイアウト（reflow）が発生するためコストが高いです。  
それに対し、仮想DOMを使って前回の仮想DOMとの差分のみを計算し  
実際のDOMに対して最小限の更新を行うことができます。

### 4. 副作用のライフサイクル管理
#### 副作用とは
Reactの副作用とは、以下のようなレンダリング以外の処理のことです。
- API通信（データ取得）
- DOM直接操作（ライブラリ利用など）
- タイマーの開始/停止（setInterval, setTimeout）
- イベントリスナ登録/解除  

※関数プログラミングでいう副作用とは、  
　関数の外部に影響を及ぼす処理（関数外の変数の値を書き換えるなど）のことをいいます
 
副作用はレンダリングとは別のタイミングで実行されます。
 | タイミング          | 副作用の実行      | クリーンアップの実行       |
| -------------- | ----------- | ---------------- |
| **初回マウント後**    | 副作用が1回実行される | -                |
| **依存変数が変化した時** | 副作用が再実行される  | 直前に前回のクリーンアップが走る |
| **アンマウント時**    | -           | クリーンアップが実行される    |

以下のようにReactの副作用はuseEffectを使って記述します。

``` 
import React, { useState, useEffect } from "react";

function UserList() {
  const [users, setUsers] = useState<any[]>([]);

  useEffect(() => {
    const fetchUsers = async () => {
      // ここでAPIを呼び出すイメージ
      // const response = await fetch("https://api.example.com/users");
      // const data = await response.json();
      const data = [{ id: 1, name: "Alice" }, { id: 2, name: "Bob" }]; // 仮データ
      setUsers(data);
    };

    fetchUsers();
  }, []); // 初回マウント時のみ実行

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

export default UserList;
```