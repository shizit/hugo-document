+++
title = "memo"
weight = 2
+++

## Fragment

### 概要

JSX で返却する要素は一つのルート要素で束ねられている必要がある  
（div タグなどの html 要素が複数並列で並んでいるとエラーになる）  
外側を div タグで囲ってもよいが、<React.Fragment>タグというものがあり、  
こちらは html 要素として変換されずかつルート要素として認識される

### 記述方法

#### 1.タグを記述

```
import React from "react";

const Example = () => {
    return (
        <React.Component>
        <div>contents1</div>
        <div>contents2</div>
        </React.Component>
    );
};

export default Example;
```

#### 2.<>で記述

<>で記述する場合、react.fragment のインポートが不要

```
const Example = () => {
    return (
        <>
        <div>contents1</div>
        <div>contents2</div>
        </>
    );
};
export default Example;

```

## JSX での値表示

```
import "./Expression.css";

const Expression = () => {

  const title = "Expression";
  const arry = ["item1", "item2", "item3"];
  const hello = (arg) => `${arg} Function`;
  const jsx = <h3>Hello JSX</h3>;
  const bool = true;

  console.log(jsx);

  return (
      /* jsの定数や変数は{}で囲むことでJSXで利用できる(classNameなどにも利用可能)*/
    <div className={title.toLowerCase()}>
      <h3>{"Hello " + title}</h3>
      /* 配列は文字列結合されて表示される　表示：item1item2item3*/
      <h3>{arry}</h3>
      /* JSXも表示できる　表示：Hello JSX */
      {<h3>Hello JSX</h3>}
      /* JSXを定数として定義できる Hello JSX */
      {jsx}
      /* booleanは表示されない 表示： */
      {bool}
    </div>
  );
};

export default Expression;
```

## JSX の正体

html のような形を javascript のオブジェクトとして扱うことができるのは  
Babel というトランスコンパイラによって関数に変換されているため

Babel 変換前：

```
<h1>Good to see you.</h1>
```

変換後：

```
React.createElement("h1", null, "Good to see you.");
```
