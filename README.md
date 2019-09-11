# Nodejs-websocket

## 認識websocket

WebSocket 是 HTML5 裡的一個標準，它是一種 TCP/IP 的連線技術。在協定部份，則是基於 HTTP（Over HTTP）協定。因此，WebSocket 標準定義了一些 HTTP Headers 來進行 Client/Server 的通訊。

WebSocket 分為 Client 端與 Server 端二個部份，本次介紹利用Nodejs還建立一個WebSocket Server，而client端可以利用Nodejs也可以用javascript來連接。

## 安裝 WebSocket 模組

如果要使用 WebSocket 模組，要使用 npm 工具另外安裝。利用 npm 安裝 WebSocket-Node(<a href="https://github.com/theturtle32/WebSocket-Node">WebSocket的github</a>)：
```
npm install websocket
```

根據不同的需求，WebSocket-Node 提供 5 個模組如下：
```
var WebSocketServer = require('websocket').server;
var WebSocketClient = require('websocket').client;
var WebSocketFrame  = require('websocket').frame;
var WebSocketRouter = require('websocket').router;
var W3CWebSocket = require('websocket').w3cwebsocket;
```

## 建立 WebSocket Server

建立一個server.js

引入剛剛下載好的模組
```
var http = require('http');
var ws = require('websocket').server;
```

建立一個server並監聽 9999 Port，並在server輸出一個已經打開的訊息
```
var PORT = 9999;
var server = http.createServer().listen(PORT);
console.log('server is open...');
```

透過新增(new)一個上面宣告的ws，建立一個WebSocket Server
```
webSocketServer = new ws({
    httpServer: server
});
```

撰寫使用者連線時的觸發事件
