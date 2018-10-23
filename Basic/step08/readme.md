출처 : https://codelabs.developers.google.com/codelabs/webrtc-web/#7

8 . 피어 연결 및 신호 결합

배우게 될 것
이 단계에서는 다음과 같은 방법을 찾을 수 있습니다.

Node.js에서 실행중인 Socket.IO를 사용하여 WebRTC 신호 서비스 실행
이 서비스를 사용하면 동료간에 WebRTC 메타 데이터를 교환 할 수 있습니다.
이 단계의 전체 버전은 step-05 폴더에 있습니다.

HTML 및 JavaScript 바꾸기
index.html 의 내용을 다음 내용으로 바꿉니다 .

<!DOCTYPE html>
<html>

<head>

  <title>Realtime communication with WebRTC</title>

  <link rel="stylesheet" href="/css/main.css" />

</head>

<body>

  <h1>Realtime communication with WebRTC</h1>

  <div id="videos">
    <video id="localVideo" autoplay muted></video>
    <video id="remoteVideo" autoplay></video>
  </div>

  <script src="/socket.io/socket.io.js"></script>
  <script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>
  <script src="js/main.js"></script>
  
</body>

</html>
js / main.js 를 단계 05 / js / main.js 의 내용으로 바꿉니다 .

Node.js 서버 실행하기
작업 디렉토리 에서이 코드 랩을 따르지 않을 경우 , step-05 폴더 또는 현재 작업중인 폴더에 대한 종속성을 설치해야 합니다. 작업 디렉토리에서 다음 명령을 실행하십시오.

npm 설치
일단 설치되면 Node.js 서버가 실행되고 있지 않으면 작업 디렉토리 에서 다음 명령을 호출하여 시작하십시오 .

노드 색인 .js
Socket.IO를 구현하는 이전 단계 의 index.js 버전을 사용하고 있는지 확인하십시오 . 노드 및 소켓 IO에 대한 자세한 내용은 "메시지 교환을위한 신호 서비스 설정"절을 검토하십시오.

브라우저에서 localhost : 8080을 엽니 다 .

새 탭 또는 창에서 localhost : 8080을 다시 엽니 다 . 한 비디오 요소는 로컬 스트림을 표시하고 getUserMedia()다른 하나는 RTCPeerconnection을 통해 스트리밍 된 '원격'비디오를 표시합니다.

클라이언트 탭이나 창을 닫을 때마다 Node.js 서버를 다시 시작해야합니다.

브라우저 콘솔에서 로깅을 봅니다.

보너스 포인트
이 응용 프로그램은 일대일 화상 채팅 만 지원합니다. 두 명 이상의 사람이 같은 화상 채팅방을 공유 할 수 있도록 디자인을 어떻게 바꿀 수 있습니까?
이 예에서는 방 이름 foo가 하드 코딩되어 있습니다. 다른 방 이름을 사용하는 가장 좋은 방법은 무엇입니까?
사용자가 객실 이름을 어떻게 공유합니까? 방 이름을 공유하는 대안을 만들도록 노력하십시오.
앱을 어떻게 바꿀 수 있습니까?
배운 내용
이 단계에서는 다음을 수행하는 방법을 배웠습니다.

Node.js에서 실행중인 Socket.IO를 사용하여 WebRTC 신호 서비스를 실행합니다.
이 서비스를 사용하면 동료간에 WebRTC 메타 데이터를 교환 할 수 있습니다.
이 단계의 전체 버전은 step-05 폴더에 있습니다.

팁
WebRTC 통계 및 디버그 데이터는 chrome : // webrtc-internals 에서 사용할 수 있습니다 .
test.webrtc.org 는 로컬 환경을 확인하고 카메라와 마이크를 테스트하는 데 사용할 수 있습니다.
캐싱에 이상한 점이 있으면 다음을 시도하십시오.
ctrl 키를 누른 상태에서 새로 고침 버튼을 클릭하면
브라우저를 다시 시작하십시오.
npm cache clean명령 행에서 실행하십시오 .
다음
사진을 찍고, 이미지 데이터를 얻고, 원격 동료간에 공유하는 방법을 알아보십시오.
