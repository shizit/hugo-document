+++
title = "Redux概要"
weight = 2
+++
## 概要
ReduxはJavaScriptアプリケーション向けの状態管理ライブラリです。  
主にReactと組み合わせて使われますが、フレームワークに依存せず単独でも利用可能です。  
公式サイト:
[https://redux.js.org/](https://redux.js.org/)

アプリケーションの状態（state）を 一元管理 し、状態の更新や参照を予測可能にします。

---
## 状態とは
Reactではどの画面だろうがコンポーネントだろうが同じデータは一つの変数（状態変数）に管理します。  
（例えばヘッダーで表示するときも、ユーザー情報で表示するときも同じ状態変数を参照します）  
このデータ（ユーザー情報）のことを状態といいます。  

## 状態管理とは  
色々な画面、コンポーネントから一つの変数にアクセスするために  
複数のコンポーネントをまたいでアクセスできるようにする必要がありますが、  
それを実現するのが状態管理です。  
※コンポーネントからコンポーネントにデータを受け渡していくことも可能ですが、  
これは可読性が低くなったりコンポーネントの結びつきが強くなり  
コンポーネントの再利用性が低くなるため避けるべき事項とされています。  
（「propsのバケツリレー」というワードで調べれば情報が出てきます）

## cookieやlocalStorageを使えばよいのでは？
Reduxによる状態は基本的にブラウザのメモリ上に保持されます。  
自分は状態という概念を知る前はデータを保持するための機能だと思っていたため、  
セッションが切れるごとに消えてしまうなら  
DBだったりCookieやlocalstorageのほうが使い勝手がいいのではと思っていましたが、  
状態管理では複数コンポーネントにまたがって状態の参照や更新をすることが目的です。

## ReactのuseContext、useState、useReducerを使った状態更新
Reactにはコンテキストという機能があり、 
これをコンポーネントの上の階層で作成しておけば  
コンテキスト内の状態やそれを更新するメソッドを参照することができます。  
コンテキストとuseState（状態）やuseReducer（状態を更新するメソッド）を組み合わせれば、  
コンポーネントをまたいで状態を管理することが可能です。

※Contextの利用例
Contextを提供するProvider
``` 
import React, { createContext, useState } from "react";

// Contextを作成
export const ThemeContext = createContext();

// Providerコンポーネント
export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={[theme, setTheme]}>
      {children}
    </ThemeContext.Provider>
  );
};

```
Providerでラッピング（App.jsxの一番上の階層）
``` 
import React from "react";
import { ThemeProvider } from "./ThemeContext";
import ThemeToggleButton from "./ThemeToggleButton";

function App() {
  return (
    <ThemeProvider>
      <div>
        <h1>React Context Example</h1>
        <ThemeToggleButton />
      </div>
    </ThemeProvider>
  );
}

export default App;

```
下の階層のコンポーネントでContextを利用：
``` 
import React, { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

const ThemeToggleButton = () => {
  const [theme, setTheme] = useContext(ThemeContext);

  return (
    <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
      現在のテーマ: {theme}
    </button>
  );
};

export default ThemeToggleButton;
```

## Redux vs useContext、useState、useReducer
それならばReduxを使わなくてよいのではとなりそうですが、  
コンテキストには弱点があり、  
コンテキストで管理している状態などに変更があった場合  
コンテキストを参照しているコンポーネントすべてを再レンダリングしてしまいます。  
例えばコンテキストでA,Bの状態を管理していてAの状態に変更が加わったとき、  
Bの状態を参照するためにコンテキストを参照しているコンポーネントも再レンダリングされます。  
頻繁に更新されるような状態や多くの状態を管理する大規模なアプリケーションでは、  
Reduxを利用することが推奨されています。

## Reduxの特徴
以下chatgptによるReduxの特徴まとめです
### 1. Single Source of Truth（状態の単一管理）

* アプリ全体の状態を 1つの store にまとめる ことで、状態の整合性を保ちやすくなります。
* 例: ユーザー情報、ログイン状態、画面表示フラグなどを一元管理。

```js
import { createStore } from "redux";

// 初期状態
const initialState = { count: 0 };

// Reducer
function counterReducer(state = initialState, action) {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + 1 };
    default:
      return state;
  }
}

// Storeの作成
const store = createStore(counterReducer);

console.log(store.getState()); // { count: 0 }
```

---

### 2. State は読み取り専用（Immutable）

* Redux の状態は 直接変更せず、action を通して変更 します。
* これにより、どの処理で状態が変更されたかを追跡しやすくなり、バグを防ぎやすくなります。

```js
store.dispatch({ type: "INCREMENT" });
console.log(store.getState()); // { count: 1 }
```

---

### 3. Reducer（状態更新のルールを定義）

* 状態の変更方法を 純粋関数で定義 します。
* 同じ入力（状態と action）に対しては常に同じ出力を返すため、予測可能です。

```js
function counterReducer(state = { count: 0 }, action) {
  switch(action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + 1 };
    default:
      return state;
  }
}
```

---

### 4. Action（状態変更の意思表示）

* 状態をどう変えたいかを表す オブジェクト
* `type` プロパティで何をするかを定義

```js
const incrementAction = { type: "INCREMENT" };
store.dispatch(incrementAction);
```

---

### 5. Redux のメリット

1. 状態が一元管理される

   * どこで状態が更新されるか明確
   * デバッグやテストがしやすい
2. 状態の変更が予測可能

   * Reducer が純粋関数なので、同じ action で同じ結果になる
3. コンポーネントから状態を分離

   * UI と状態管理が切り離され、コンポーネントは状態を「表示するだけ」に集中できる
4. 開発者ツールで履歴管理が可能

   * Redux DevTools を使うと、過去の状態やアクション履歴を簡単に確認できる

---

### 6. React との組み合わせ

* React コンポーネントは Redux store から状態を取得（`useSelector`）
* 状態変更は Redux action を dispatch（`useDispatch`）
* 例:

```tsx
import React from "react";
import { useSelector, useDispatch } from "react-redux";

function Counter() {
  const count = useSelector((state: any) => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>＋1</button>
    </div>
  );
}
```

### ReactのuseContextとuse

``` 
import React, { createContext, useState } from "react";

// Contextを作成
export const ThemeContext = createContext();

// Providerコンポーネント
export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={[theme, setTheme]}>
      {children}
    </ThemeContext.Provider>
  );
};

```

``` 
import React, { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

const ThemeToggleButton = () => {
  const [theme, setTheme] = useContext(ThemeContext);

  return (
    <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
      現在のテーマ: {theme}
    </button>
  );
};

export default ThemeToggleButton;
```