출처 :  https://codelabs.developers.google.com/codelabs/webrtc-web/#6

# 7 . 메시지 교환을위한 신호 서비스 설정

__배우게 될 것__

 이번 단계에서는 다음과 같은 방법을 배웁니다.
  
  `npm`을 이용하여 __package.json__ 지정된 프로젝트를 주입받습니다(의존성을 이용하여 외부의 라이브러리를 받아오는 방식)
  'Node.js' 서버를 실행하고 node-static을 사용하여 정적 파일을 이용합니다.
  Socket.IO를 사용하여 Node.js에서 메시징 서비스를 설정합니다.
  이를 사용하여 'rooms'을 만들고 메시지를 교환합니다.

이번 단계의 완성 버전은 __step-04__ 폴더에 있습니다.

__개념__

WebRTC 호출을 설정하고 유지하려면 WebRTC 클라이언트(피어)간의 메타 데이터를 교환해야합니다.
<ul>
<li>후보자 (네트워크) 정보.</li>
  <li>해상도와 코덱 등의 미디어에 대한 정보를 제공하는 메시지를 제공 또는 응답</li>
 </ul>
 
즉, 오디오, 비디오 또는 데이터의 p2p 스트리밍이 발생하기 전에 메타 데이터 교환이 필요합니다.
이러한 프로세스를 __시그널링__ 이라고 합니다 .

이전 단계에서는 보낸 사람과 받는 사람의 RTCPeerConnection 개체가 같은 페이지에 있으므로 __'시그널링'__ 은 단순히 개체간에 
메타 데이터를 전달하는 문제였습니다.

하지만, 실제 응용 프로그램에서는 보낸 사람과 받는 사람 RTCPeerConnections가 서로 다른 장치의 웹 페이지에서 실행되므로 
메타 데이터를 전달할 수있는 방법이 필요합니다.

이를 위해 __시그널링 서버__ , 즉 WebRTC 클라이언트 (peer)간에 메시지를 전달할 수있는 서버를 이용합니다.
실제 메시지는 일반 텍스트(문자열 화 된 JavaScript 객체)입니다.

# 필수 조건 : Node.js 설치

이 코드랩의 다음 단계(폴더 __step-04__ 에서 __step-06__ )를 실행하기 위해 `Node.js`를 사용하여 localhost에서 서버를 실행해야합니다.

`Node.js`는 <a href="https://nodejs.org/en/download/">이 링크</a> 또는 <a href="https://nodejs.org/en/download/package-manager/">패키지 관리자</a>를 통해 다운로드하여 설치할 수 있습니다.

일단 설치되면 다음 단계(`npm install` 실행하기 위한)에 필요한 종속성을 가져올 수 있고, 코드 랩을 진행하기 위한
소규모 로컬 호스트 서버(`node index.js` 실행을 통해)를 실행할 수 있습니다. 이 명령은 필요할 때 표시됩니다.

__앱 정보__

WebRTC는 클라이언트 측 JavaScript API를 사용하지만 실제로 사용하려면 STUN 및 TURN 서버는 물론 신호 (메시징) 서버가 필요합니다. 
자세한 내용은 <a href="https://www.html5rocks.com/en/tutorials/webrtc/infrastructure/">여기</a>에서 확인할 수 있습니다 .

이번 단계에서는 Socket.IO Node.js 모듈과 메시징을위한 JavaScript 라이브러리를 사용하여 간단한 Node.js 신호 서버를 빌드합니다.
Node.js 및 Socket.IO에 대한 경험이 있으면 좋으나, 없어도 상관없습니다. 메시징 구성 요소는 매우 간단합니다.

---

__올바른 신호 서버 고르기__

이 코드 랩은 신호 서버에 <a href="https://socket.io/">Socket.IO</a> 를 사용 합니다.

Socket.IO의 구조를 통해 메시지를 교환하는 서비스를 쉽게 만들 수 있으며,
Socket.IO에서 기본으로 제공되는 'rooms'개념으로 인해 WebRTC 신호 처리에 대해 학습하는 데 적합합니다.

그러나 이러한 서비스에 대한 더 나은 대안도 있습니다. 다음 <a href="https://bloggeek.me/siganling-protocol-webrtc/">WebRTC 프로젝트를 위한 신호 프로토콜을 선택하는 방법</a>을 참조해보세요.

---

이번 예제에서는 서버(Node.js 어플리케이션)는 index.js에 구현되어있고, 클라이언트는는 index.html에 실행함으로써 구현할수 있습니다 .

이번 단계에서 Node.js 어플리케이션에는 두 가지 작업이 수행합니다.

첫째, 메시지 릴레이 역할을합니다.
~~~
socket.on('message', function (message) {
  log('Got message: ', message);
  socket.broadcast.emit('message', message);
});
~~~
둘째, WebRTC 화상 채팅 'rooms'을 관리합니다.
~~~
if (numClients === 0) {
  socket.join(room);
  socket.emit('created', room, socket.id);
} else if (numClients === 1) {
  socket.join(room);
  socket.emit('joined', room, socket.id);
  io.sockets.in(room).emit('ready');
} else { // max two clients
  socket.emit('full', room);
}
~~~

예제에서는 WebRTC 어플리케이션은 room의 최대치를 2명으로 정합니다.

__HTML 및 자바 스크립트__

다음과 같이 index.html을 변경하세요.

~~~
<!DOCTYPE html>
<html>

<head>

  <title>Realtime communication with WebRTC</title>

  <link rel="stylesheet" href="css/main.css" />

</head>

<body>

  <h1>Realtime communication with WebRTC</h1>

  <script src="/socket.io/socket.io.js"></script>
  <script src="js/main.js"></script>
  
</body>

</html>
~~~

이번 단계에서 페이지에 아무 것도 표시되지 않습니다.
모든 로깅은 브라우저 콘솔에서 수행됩니다. 
Chrome에서 콘솔을 보려면 Mac의 경우 `Ctrl-Shift-J` 또는 `Command-Option-J`를 누릅니다.

js / main.js 를 다음으로 변경하세요 .

~~~
'use strict';

var isInitiator;

window.room = prompt("Enter room name:");

var socket = io.connect();

if (room !== "") {
  console.log('Message from client: Asking to join room ' + room);
  socket.emit('create or join', room);
}

socket.on('created', function(room, clientId) {
  isInitiator = true;
});

socket.on('full', function(room) {
  console.log('Message from client: Room ' + room + ' is full :^(');
});

socket.on('ipaddr', function(ipaddr) {
  console.log('Message from client: Server IP address is ' + ipaddr);
});

socket.on('joined', function(room, clientId) {
  isInitiator = false;
});

socket.on('log', function(array) {
  console.log.apply(console, array);
});

~~~

__Node.js에서 실행되도록 Socket.IO 설정__
HTML 파일에서 Socket.IO 파일을 아래와 같이 사용하고있는 것을 보았을 것입니다.
~~~
<script src = "/ socket.io/socket.io.js"> </ script>
~~~

__work__ 디렉토리 의 최상위 레벨에서 다음 내용으로 __package.json__ 파일을 작성 하세요.

~~~
{ 
  "name": "webrtc-codelab", 
  "version": "0.0.1", 
  "description": "WebRTC codelab", 
  "dependencies": { 
    "node-static": "^ 0.7.10", 
    "socket .io ":"^ 1.2.0 " 
  } 
}
~~~

이 패키지는 노드 패키지 관리자 (npm)에게 어떤 프로젝트 종속성을 설치할 것인지 알려주는 응용 프로그램 목록(manifest)입니다 .

의존성(예 : `/socket.io/socket.io.js`)을 주입하려면, __work__ 디렉토리에서 command 창에 다음과 같이 실행하십시오.
~~~
npm install
~~~
그러면 다음과 같이 끝나는 설치 로그가 나타납니다.
<br/>
<image src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/eba5a6336d420b40.png"/>
<br/>
<br/>

위와 같이, `npm`에 정의 된 `package.json`에 따른 의존성을 설치했습니다.

__js__ 디렉토리가 아닌 __work__ 디렉토리 의 최상위 레벨에 __index.js__ 라는 새 파일을 작성 하고 다음 코드를 추가하십시오.

~~~
'use strict';

var os = require('os');
var nodeStatic = require('node-static');
var http = require('http');
var socketIO = require('socket.io');

var fileServer = new(nodeStatic.Server)();
var app = http.createServer(function(req, res) {
  fileServer.serve(req, res);
}).listen(8080);

var io = socketIO.listen(app);
io.sockets.on('connection', function(socket) {

  // convenience function to log server messages on the client
  function log() {
    var array = ['Message from server:'];
    array.push.apply(array, arguments);
    socket.emit('log', array);
  }

  socket.on('message', function(message) {
    log('Client said: ', message);
    // for a real app, would be room-only (not broadcast)
    socket.broadcast.emit('message', message);
  });

  socket.on('create or join', function(room) {
    log('Received request to create or join room ' + room);

    var clientsInRoom = io.sockets.adapter.rooms[room];
    var numClients = clientsInRoom ? Object.keys(clientsInRoom.sockets).length : 0;

    log('Room ' + room + ' now has ' + numClients + ' client(s)');

    if (numClients === 0) {
      socket.join(room);
      log('Client ID ' + socket.id + ' created room ' + room);
      socket.emit('created', room, socket.id);

    } else if (numClients === 1) {
      log('Client ID ' + socket.id + ' joined room ' + room);
      io.sockets.in(room).emit('join', room);
      socket.join(room);
      socket.emit('joined', room, socket.id);
      io.sockets.in(room).emit('ready');
    } else { // max two clients
      socket.emit('full', room);
    }
  });

  socket.on('ipaddr', function() {
    var ifaces = os.networkInterfaces();
    for (var dev in ifaces) {
      ifaces[dev].forEach(function(details) {
        if (details.family === 'IPv4' && details.address !== '127.0.0.1') {
          socket.emit('ipaddr', details.address);
        }
      });
    }
  });

});
~~~

__work__ 디렉토리의 command 창에서 다음 명령을 실행하십시오 .
~~~
node index .js
~~~

브라우저에서 __localhost : 8080__ 을 엽니다 .

이 URL을 열 때마다 rooms의 이름을 입력하라는 메시지가 표시됩니다. 같은 방에 가려면 'foo'와 같이 같은방 이름을 선택하십시오.

새 탭 페이지를 열고 localhost : 8080을 다시 연 후, 같은 방 이름을 선택하십시오.

세 번째 탭이나 창에서 localhost : 8080 을 연 후, 같은 방 이름을 다시 선택하십시오.

각 탭의 콘솔을 확인해보세요. 위에서와 같이 JavaScript에서 로깅을 확인해야합니다.

# 보너스 포인트
  가능한 대체 메시징 메커니즘은 무엇일까요? '순수한' WebSocket을 사용면 어떤 문제가 발생할 수 있을까요?
  이 응용 프로그램을 확장한다면 어떤 문제가 있을 수 있을까요? 
  수천 또는 수백만 개의 방을 동시에 요청하는 테스트 할 수있는 방법을 개발할 수 있습니까?
  이 앱은 JavaScript 프롬프트를 사용하여 rooms 이름을 가져옵니다. 
  URL에서 rooms 이름을 가져 오는 방법을 찾아보세요. (예를 들어 localhost : 8080 / foo 는 'foo'라는 방 이름을 부여합니다)

__배운 내용__

이 단계에서는 다음을 수행하는 방법을 배웠습니다.

  npm을 사용하여 package.json에 지정된대로 프로젝트 의존성을 설치합니다.
  Node.js 서버의 서버 정적 파일을 실행합니다.
  socket.io를 사용하여 Node.js에서 메시징 서비스를 설정합니다.
  이를 사용하여 'rooms'을 만들고 메시지를 교환합니다.
  
이 단계의 완성 버전은 step-04 폴더에 있습니다.

__심화 학습__
<ul>
<li><a href="https://github.com/rauchg/chat-example">Socket.io 채팅 예제</a></li>
<li><a href="https://www.html5rocks.com/en/tutorials/webrtc/infrastructure/">실 생활에서의 WebRTC : STUN, TURN 및 신호 처리</a></li>
<li><a href="https://www.webrtc-experiment.com/docs/WebRTC-Signaling-Concepts.html">WebRTC에서 '시그널링'라는 용어</a></li>
 </ul>
다음
시그널링을 사용하여 두 명의 사용자가 피어 연결을 수행하는 방법을 확인하십시오.
