출처 :  https://codelabs.developers.google.com/codelabs/webrtc-web/#6

7 . 메시지 교환을위한 신호 서비스 설정

배우게 될 것
이 단계에서는 다음과 같은 방법을 찾을 수 있습니다.

package.json에npm 지정된대로 프로젝트 종속성을 설치하는 데 사용 합니다.
Node.js 서버를 실행하고 node-static을 사용하여 정적 파일을 제공하십시오.
Socket.IO를 사용하여 Node.js에서 메시징 서비스를 설정합니다.
이를 사용하여 '회의실'을 만들고 메시지를 교환하십시오.
이 단계의 전체 버전은 step-04 폴더에 있습니다.

개념
WebRTC 호출을 설정하고 유지하려면 WebRTC 클라이언트 (피어)가 메타 데이터를 교환해야합니다.

후보자 (네트워크) 정보.
제안 및 답변 등의 해상도와 코덱 등의 미디어에 대한 정보를 제공하는 메시지를.
즉, 오디오, 비디오 또는 데이터의 피어 투 피어 스트리밍이 발생할 수 있기 전에 메타 데이터 교환이 필요합니다. 이 프로세스를 시그널링 이라고 합니다 .

이전 단계에서는 보낸 사람과받는 사람 RTCPeerConnection 개체가 같은 페이지에 있으므로 '신호'는 단순히 개체간에 메타 데이터를 전달하는 문제입니다.

실제 응용 프로그램에서는 보낸 사람과받는 사람 RTCPeerConnections가 서로 다른 장치의 웹 페이지에서 실행되므로 메타 데이터를 전달할 수있는 방법이 필요합니다.

이를 위해 시그널링 서버 , 즉 WebRTC 클라이언트 (피어)간에 메시지를 전달할 수있는 서버 를 사용합니다 . 실제 메시지는 일반 텍스트 : 문자열 화 된 JavaScript 객체입니다.

필수 조건 : Node.js 설치
이 코드 랩의 다음 단계 (폴더 step-04 에서 단계 -06 )를 실행하려면 Node.js를 사용하여 localhost에서 서버를 실행해야합니다.

Node.js는 이 링크 또는 원하는 패키지 관리자 를 통해 다운로드하여 설치할 수 있습니다 .

일단 설치되면 다음 단계 (실행 중 npm install)에 필요한 종속성을 가져올 수 있고 작은 로컬 호스트 서버를 실행하여 코드 랩 (실행 중 node index.js) 을 실행할 수 있습니다. 이 명령은 필요할 때 나중에 표시됩니다.

앱 정보
WebRTC는 클라이언트 측 JavaScript API를 사용하지만 실제로 사용하려면 STUN 및 TURN 서버는 물론 신호 (메시징) 서버가 필요합니다. 자세한 내용은 여기에서 확인할 수 있습니다 .

이 단계에서는 Socket.IO Node.js 모듈과 메시징을위한 JavaScript 라이브러리를 사용하여 간단한 Node.js 신호 서버를 빌드합니다. Node.js 및 Socket.IO에 대한 경험은 유용 할 것이나 중요하지는 않습니다. 메시징 구성 요소는 매우 간단합니다.

올바른 신호 서버 선택

이 코드 랩은 신호 서버에 Socket.IO 를 사용 합니다.

Socket.IO의 설계로 메시지를 교환하는 서비스를 쉽게 만들 수 있으며 Socket.IO는 기본 제공되는 '회의실'개념으로 인해 WebRTC 신호 처리에 대해 학습하는 데 적합합니다.

그러나 프로덕션 서비스에는 더 나은 대안이 있습니다. 다음 WebRTC 프로젝트를위한 신호 프로토콜을 선택하는 방법을 참조하십시오 .

이 예제에서 서버 (Node.js 응용 프로그램)는 index.js에 구현되고 실행되는 클라이언트 (웹 응용 프로그램)는 index.html에 구현됩니다 .

이 단계에서 Node.js 응용 프로그램에는 두 가지 작업이 있습니다.

첫째, 메시지 릴레이 역할을합니다.

socket.on('message', function (message) {
  log('Got message: ', message);
  socket.broadcast.emit('message', message);
});
둘째, WebRTC 화상 채팅 '객실'을 관리합니다.

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
우리의 간단한 WebRTC 응용 프로그램은 최대 두 명의 동료가 한 방을 공유하도록 허용합니다.

HTML 및 자바 스크립트
다음과 같이 index.html을 업데이트 하십시오.

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
이 단계에서 페이지에 아무 것도 표시되지 않습니다. 모든 로깅은 브라우저 콘솔에서 수행됩니다. Chrome에서 콘솔을 보려면 Mac의 경우 Ctrl-Shift-J 또는 Command-Option-J를 누릅니다.

js / main.js 를 다음으로 대체하십시오 .

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

Node.js에서 실행되도록 Socket.IO 설정
HTML 파일에서 Socket.IO 파일을 사용하고있는 것을 보았을 수 있습니다.

<script src = "/ socket.io/socket.io.js"> </ script>
작업 디렉토리 의 최상위 레벨 에서 다음 내용으로 package.json 파일을 작성 하십시오 .

{ 
  "name": "webrtc-codelab", 
  "version": "0.0.1", 
  "description": "WebRTC codelab", 
  "dependencies": { 
    "node-static": "^ 0.7.10", 
    "socket .io ":"^ 1.2.0 " 
  } 
}
이 패키지는 노드 패키지 관리자 ( npm)에게 어떤 프로젝트 종속성을 설치할 것인지 알려주는 응용 프로그램 목록입니다 .

종속성 (예 :)을 설치하려면 작업 디렉토리 /socket.io/socket.io.js의 명령 행 터미널에서 다음을 실행하십시오 .

npm 설치
다음과 같이 끝나는 설치 로그가 나타납니다.



보시다시피, package.json에npm 정의 된 종속성을 설치했습니다 .

js 디렉토리가 아닌 작업 디렉토리 의 최상위 레벨에 index.js 라는 새 파일을 작성 하고 다음 코드를 추가하십시오.

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
명령 행 터미널에서 작업 디렉토리 에서 다음 명령을 실행하십시오 .

노드 색인 .js
브라우저에서 localhost : 8080을 엽니 다 .

이 URL을 열 때마다 회의실 이름을 입력하라는 메시지가 표시됩니다. 같은 방에 가려면 'foo'와 같은 방 이름을 매번 선택하십시오.

새 탭 페이지를 열고 localhost : 8080을 다시 엽니 다 . 같은 방 이름을 선택하십시오.

세 번째 탭이나 창에서 localhost : 8080 을 엽니 다 . 같은 방 이름을 다시 선택하십시오.

각 탭의 콘솔을 확인하십시오. 위의 JavaScript에서 로깅을 확인해야합니다.

보너스 포인트
가능한 대체 메시징 메커니즘은 무엇입니까? '순수한'WebSocket을 사용하여 어떤 문제가 발생할 수 있습니까?
이 응용 프로그램을 확장하는 데 어떤 문제가있을 수 있습니까? 수천 또는 수백만 개의 동시 방 요청을 테스트 할 수있는 방법을 개발할 수 있습니까?
이 앱은 JavaScript 프롬프트를 사용하여 회의실 이름을 가져옵니다. URL에서 회의실 이름을 가져 오는 방법을 찾아보십시오. 예를 들어 localhost : 8080 / foo 는 방 이름을 부여합니다 foo.
배운 내용
이 단계에서는 다음을 수행하는 방법을 배웠습니다.

npm을 사용하여 package.json에 지정된대로 프로젝트 종속성을 설치합니다.
서버 정적 파일에 Node.js 서버를 실행하십시오.
socket.io를 사용하여 Node.js에서 메시징 서비스를 설정합니다.
이를 사용하여 '회의실'을 만들고 메시지를 교환하십시오.
이 단계의 전체 버전은 step-04 폴더에 있습니다.

더 찾아 봐
Socket.io chat-example repo
실제 세계의 WebRTC : STUN, TURN 및 신호 처리
WebRTC에서 '신호'라는 용어
다음
시그널링을 사용하여 두 명의 사용자가 피어 연결을 수행하는 방법을 확인하십시오.
