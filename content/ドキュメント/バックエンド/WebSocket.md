+++
title = "WebSocket"
weight = 3
+++

## WebSocketとは
クライアントとサーバーの間でリアルタイムに双方向通信を行うための通信プロトコルです。
通常のHttp通信と違い、一度接続するとその接続を維持したまま双方が自由にデータを送れるのが特徴です。

## Http通信の欠点
Http通信の場合、クライアント側からのリクエストをサーバーが受け取ってレスポンスを返す仕様のため、  
常にクライアント側のリクエストが通信のトリガーになります。  
例えばDBデータの変更があったときにサーバー側からクライアントに通信したい場合、  
Httpで実現しようとすると、一定間隔でクライアント側からサーバー側に  
DBデータの変更があるか確認するリクエストを送り続ける（=ポーリングといいます）必要があります。   

## WebSocketの利点
サーバーとクライアントのどちらからも通信することが可能になります。  
また通信ではフレームという単位でデータを通信し、  
ヘッダーのサイズがHttpよりも小さいため通信量を削減することができます。

## どんな場面で使われるか
- チャット（SlackやDiscordなど）
- Webアプリのリアルタイム更新（株価、リアルタイム通知　など）


## WebSocket通信開始の流れ
1. クライアントからサーバーに、HTTP接続をWebSocketプロトコルに  
アップグレードするためのHttpリクエストを送信  
 (ヘッダーのConnectionにUpgrade、Upgradeにwebsocketを設定します)
``` 
GET / HTTP/1.1
Host: localhost:8080
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: abcdefg123456
```
2. サーバー側が101 Switching Protocolsを返す => WebSocket通信が開始します


## 実際に使ってみる
Node.jsでWebSocketに対応した簡易的なサーバーを作成し、  
ブラウザから動作確認します。

### 簡易サーバーの作成（Node.js）
websocketの接続時に"WebSocket connected"と表示し、  
クライアントからメッセージが送られてきた場合に  
クライアントへ"echo: {送られてきたメッセージ}"を返すサーバーを作成します。  
サーバーはdockerでnode.jsのコンテナを使って作成します。

※Dockerfile
``` dockerfile
FROM node:20

WORKDIR /app

COPY server.js .

RUN npm install ws

EXPOSE 8080

CMD ["node", "server.js"]
```
※server.js
``` javascript
const http = require("http");
const WebSocket = require("ws");

const server = http.createServer((req, res) => {
    res.writeHead(200);
    res.end("HTTP server running");
});

const wss = new WebSocket.Server({ server });

wss.on("connection", (ws, request) => {
    console.log("WebSocket connected");

    ws.on("message", (message) => {
        console.log("received:", message.toString()); // 受け取ったメッセージを表示
        ws.send("echo: " + message); // echo: {メッセージ}　をクライアントへ返す
    });
});

server.listen(8080, () => {
    console.log("server started on 8080");
});
```
※コンテナビルド & 起動
``` 
docker build -t ws-test .
docker run -p 8080:8080 ws-test
```

### ブラウザで簡易サーバーにアクセス
#### 1. 作成した簡易サーバーにブラウザからアクセスします。
![alt text](/images/websocket/image-1.png?classes=left)

#### 2. ブラウザのコンソールでサーバーに対してwebsocketの接続を開始します。  
※以下開発者ツールのConsoleで以下javascriptを実行
``` 
const ws = new WebSocket("ws://localhost:8080");
ws.onopen = () => {
  console.log("connected");
};
```
![alt text](/images/websocket/image-1.png?classes=left)  
=> ネットワークタブを確認すると、Upgradeリクエストに対して101が返ってきていることが確認できます。  
![alt text](/images/websocket/image-2.png?classes=left)  
※サーバー側でもWebSocket connectedのメッセージが出力されています  
![alt text](/images/websocket/image-3.png?classes=left)

#### 3. サーバーからメッセージを受け取った際の処理をクライアント側で指定します。  
※サーバーから受け取ったメッセージをコンソールに表示するようにします
``` 
ws.onmessage = (e) => {
  console.log(e.data);
};
```
![alt text](/images/websocket/image-4.png?classes=left)

#### 4. クライアントからサーバーへメッセージを送信します。
``` 
ws.send("test")
```
⇒ echo: testがコンソールに表示されました  
![alt text](/images/websocket/image-5.png?classes=left)

※サーバー側でもtestという文字列を受けとっています  
![alt text](/images/websocket/image-6.png?classes=left)

※ちなみに、開発者ツールのネットワークタブのMessagesで双方向の通信が確認できるようです。  
![alt text](/images/websocket/image-7.png?classes=left)