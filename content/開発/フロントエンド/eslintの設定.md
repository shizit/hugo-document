+++
title = "eslintの設定"
weight = 2
+++

## 参考
https://qiita.com/uhooi/items/f22b53b6e39228fc4826  
https://katanugramer.hatenablog.com/entry/2020/06/20/145437

## 手順
### 1.インストール
Nextjsプロジェクトのガイドで作成しているため、割愛
### 2.eslint init
以下を入力
```
✔ How would you like to use ESLint? · problems
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · react
✔ Does your project use TypeScript? · Yes
✔ Where does your code run? · browser, node
✔ What format do you want your config file to be in? · YAML
The config that you've selected requires the following dependencies:

@typescript-eslint/eslint-plugin@latest eslint-plugin-react@latest @typescript-eslint/parser@latest
✔ Would you like to install them now? · Yes
✔ Which package manager do you want to use? · npm
```

### 3. prettierと併用するためのプラグインをインストール
``` 
npm i -D eslint prettier eslint-plugin-prettier eslint-config-prettier
```

### 4. airbnbのeslintスタイルをインストール
```
npx install-peerdeps --dev eslint-config-airbnb
```

### 5. eslintとprettierの設定
以下ファイルを更新  
.eslintrc.json
```
{
  "extends": ["airbnb", "prettier"],
  "plugins": ["prettier"],
  "rules": {
    "no-console": "off",
    "prettier/prettier": ["error"]
  }
}
```
.prettierrc
```
{
  "printWidth": 100,
  "singleQuote": true
}
```
### 6. vscodeの設定を更新
ctrl + shift + pでコマンドパレットを開き、  
preferences: Open Settings (UI) を検索してクリック  
Editor: Format on Save　をチェックボックスONにする

