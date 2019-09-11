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
```
webSocketServer.on('request', function(request) {
    var connection = request.accept('echo-protocol', request.origin);

    //當websocket server收到訊息時 觸發此事件
    connection.on('message', function(message) {
        console.log(message);
        connection.send("我收到了: " + message.utf8Data);
    });

    //當使用者socket連線中斷時 例如：關閉瀏覽器 觸發此事件
    connection.on('close', function(reasonCode, description) {
        console.log('Close');
    });
});
```

全部寫在一起就會是
```
//引入http websocket
var http = require('http');
var ws = require('websocket').server;

// 建立server 並監聽Port 9999
var PORT = 9999;
var server = http.createServer().listen(PORT);

console.log('server is open...');

// 產生websocketServer
webSocketServer = new ws({
    httpServer: server
});

//當使用者連入時 觸發此事件
webSocketServer.on('request', function(request) {
    var connection = request.accept('echo-protocol', request.origin);

    //當websocket server收到訊息時 觸發此事件
    connection.on('message', function(message) {
        console.log(message);
        connection.send("我收到了: " + message.utf8Data);
    });

    //當使用者socket連線中斷時 例如：關閉瀏覽器 觸發此事件
    connection.on('close', function(reasonCode, description) {
        console.log('Close');
    });
});
```

開啟防火牆 9999 PORT，並重載入防火牆
```
firewall-cmd --add-port=9999/tcp --permanent
firewall-cmd --reload
```

## 使用html 和 js 建立Client端

JS可以使用Web Workers下的WebSocket API。(<a href="https://developer.mozilla.org/zh-TW/docs/WebSockets/Writing_WebSocket_client_applications">參考</a>)

宣告方式:
```
var mySocket = new WebSocket("ws://www.example.com/socketserver", "my-custom-protocol");
```
第一個參數為連線URL，第二個則是所選擇的通訊協定(選填)，且第一個參數有分ws://（非加密連線） 和 wss://（加密連線）

```
<!DOCTYPE html>
<html>
<head>
	<!--載入jQuery-->
	<script   src="https://code.jquery.com/jquery-1.12.4.min.js"   integrity="sha256-ZosEbRLbNQzLpnKIkEdrPv7lOy9C27hHQ+Xp8a4MxAQ="   crossorigin="anonymous"></script>
	<script>

	//建立WebSocket 連到127.0.0.1 Port為9999
	//echo-protocol為約定好的自訂protocol 在WebSocket Server端也要設定
	var mySocket = new WebSocket("ws://122.147.213.61:9999", "echo-protocol");

	//Socket接收訊息時會呼叫此函數，處理接收到的訊息
	mySocket.onmessage = function(e) {
		$('#message').html(e.data);
	}

	mySocket.onopen = function(e) {
		mySocket.send('連線完成！');
	}

	function sendMessage() {
		message = $('#userInput').val();
		//傳送訊息給Server
		mySocket.send(message);
	}

	</script>
</head>
<body>
	<div id="userDiv">
		<input type="text" id="userInput">
		<button id="sendButton" onClick="sendMessage()">Send</button>
	</div>
	<div id="message"></div>
</body>
</html>
```
