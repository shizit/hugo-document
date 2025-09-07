+++
title = "React refによるDOM操作"
weight = 3
+++
## 1. 概要

Reactは基本的に宣言的UIを採用しているためDOMを直接参照したり操作することは少ないですが、  
- 入力フォームに自動でフォーカスを当てたい
- スクロール位置を操作したい
- Canvas や動画の再生コントロールを行いたい
- 外部ライブラリが直接 DOM を必要とする
などの理由でDOMにアクセスしたい場合があります。  
  
こういった場合、Reactを利用しているコンポーネントでgetElementByIdなどで直接参照しようとすると  
Reactが仮想DOM経由でレンダリングしている都合上DOMが生成されておらず、  
DOMを参照できないなどの不具合が発生する可能性があります。  
そこでReactで画面DOMを参照する際利用するのがrefです。

## 2. refの基本

### 2-1. useRefによるDOM参照

```tsx
import React, { useRef } from "react";

const TextInput: React.FC = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    // currentがnullでない場合にアクセス
    inputRef.current?.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" placeholder="ここに入力" />
      <button onClick={focusInput}>フォーカスする</button>
    </div>
  );
};

export default TextInput;
```

* `useRef` はDOM要素や任意の値を保持するオブジェクトを返します。
* `ref.current` に実際のDOMがセットされます。
* 初期値を `null` にしておくのが一般的です。

---

### 2-2. createRefによるクラスコンポーネントでの使用

```tsx
import React, { Component, createRef } from "react";

class TextInputClass extends Component {
  inputRef = createRef<HTMLInputElement>();

  focusInput = () => {
    this.inputRef.current?.focus();
  };

  render() {
    return (
      <div>
        <input ref={this.inputRef} type="text" placeholder="ここに入力" />
        <button onClick={this.focusInput}>フォーカスする</button>
      </div>
    );
  }
}

export default TextInputClass;
```

* クラスコンポーネントでは `createRef` を使って ref を作成します。
* `ref.current` にDOM要素がセットされます。

---

## 3. refを使ったよくある操作

| 操作     | 実装例                                                      |
| ------ | -------------------------------------------------------- |
| フォーカス  | `inputRef.current?.focus()`                              |
| スクロール  | `divRef.current?.scrollIntoView({ behavior: "smooth" })` |
| テキスト選択 | `inputRef.current?.select()`                             |
| サイズ取得  | `const rect = divRef.current?.getBoundingClientRect()`   |

---