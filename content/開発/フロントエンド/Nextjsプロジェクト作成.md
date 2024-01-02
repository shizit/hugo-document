+++
title = "Nextjsプロジェクト作成"
weight = 1
+++

## 参考
nextjs公式ドキュメント  
https://nextjs.org/docs/getting-started/installation

## 手順
### 1. create-next-appコマンド実行
``` 
npx create-next-app@latest
```

### 2. 標準入力で以下を入力
```
Need to install the following packages:
create-next-app@14.0.4
Ok to proceed? (y) y
✔ What is your project named? … one-table
✔ Would you like to use TypeScript? … Yes
✔ Would you like to use ESLint? … Yes
✔ Would you like to use Tailwind CSS? … Yes
✔ Would you like to use `src/` directory? … No
✔ Would you like to use App Router? (recommended) … Yes
? Would you like to customize the default import alias (@/*)? › No
```
### 3. プロジェクトが作成されたことを確認
``` 
$ ls -a one-table
.   .eslintrc.json  .gitignore  app            next.config.js  package-lock.json  postcss.config.js  tailwind.config.ts
..  .git            README.md   next-env.d.ts  node_modules    package.json       public             tsconfig.json
```

