+++
title = "eslintの設定"
weight = 2
+++

## 参考
nextjs公式サイト  
https://nextjs.org/docs/pages/building-your-application/configuring/eslint

## 手順
### 1.インストール
Nextjsプロジェクトのガイドで作成しているため割愛  
core-web-vitalsという
https://nextjs.org/docs/pages/building-your-application/configuring/eslint#core-web-vitals

### 2.試しに実行
``` 
$ npm run lint

> one-table@0.1.0 lint
> next lint

Attention: Next.js now collects completely anonymous telemetry regarding usage.
This information is used to shape Next.js' roadmap and prioritize features.
You can learn more, including how to opt-out if you'd not like to participate in this anonymous program, by visiting the following URL:
https://nextjs.org/telemetry

✔ No ESLint warnings or errors
$ 
```

### 3. vscodeの拡張機能のEslintをインストール  
https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint

### 4. Pritterの設定
pritterと競合しないよう設定を追加し、
``` 
npm install --save-dev eslint-config-prettier
```

.eslintrc.jsonにpritterを追加
``` 
{
  "extends": [
    "next/core-web-vitals",
    "pritter"
  ]
}
```
※参考  
https://github.com/prettier/eslint-config-prettier

### 5. Prettierのインストール
prettierのインストール
``` 
npm install --save-dev prettier
```

