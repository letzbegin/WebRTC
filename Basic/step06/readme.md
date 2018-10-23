출처 : https://codelabs.developers.google.com/codelabs/webrtc-web/#5

# 6 . RTCDataChannel을 사용하여 데이터 교환

__배우게 될 것__

    WebRTC 엔드 포인트 (피어)간에 데이터를 교환하는 방법.
    이 단계의 완성 버전은 step-03 폴더에 있습니다.

__HTML 업데이트__

이 단계에서는 WebRTC 데이터 채널을 이용하여 같은 페이지에 있는 두개의 `textarea`요소의 text를 서로 전송합니다.
그다지 유용하지는 않지만 WebRTC를 사용하여 스트리밍 비디오와 데이터를 공유하는 방법을 볼수 있습니다.

__index.html__ 에서 비디오 및 버튼 요소를 제거하고 다음 HTML로 대체하십시오.

~~~
<textarea id="dataChannelSend" disabled
    placeholder="Press Start, enter some text, then press Send."></textarea>
<textarea id="dataChannelReceive" disabled></textarea>

<div id="buttons">
  <button id="startButton">Start</button>
  <button id="sendButton">Send</button>
  <button id="closeButton">Stop</button>
</div>
~~~

하나의 텍스트 영역은 텍스트를 입력하기위한 것이며 다른 하나는 동료간에 스트리밍 된 텍스트를 표시합니다.

index.html 은 이제 다음과 같이 변경합니다.

~~~
<!DOCTYPE html>
<html>

<head>

  <title>Realtime communication with WebRTC</title>

  <link rel="stylesheet" href="css/main.css" />

</head>

<body>

  <h1>Realtime communication with WebRTC</h1>

  <textarea id="dataChannelSend" disabled
    placeholder="Press Start, enter some text, then press Send."></textarea>
  <textarea id="dataChannelReceive" disabled></textarea>

  <div id="buttons">
    <button id="startButton">Start</button>
    <button id="sendButton">Send</button>
    <button id="closeButton">Stop</button>
  </div>

  <script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>
  <script src="js/main.js"></script>

</body>

</html>

~~~
__자바 스크립트 업데이트__

__main.js__ 를 __step-03 / js / main.js__ 의 내용으로 변경합니다 .

피어간에 스트리밍 데이터를 시험해봅시다. 
__index.html__ 을 열고 start버튼을 눌러 피어 연결을 설정합니다. 왼쪽에 있는 `textarea`에 텍스트를 입력 한 다음 보내기를 클릭하여,
WebRTC 데이터 채널을 사용하여 텍스트를 전송합니다.

__작동 원리__

아래의 코드는 RTCPeerConnection과 RTCDataChannel을 사용하여 문자 메시지를 교환합니다.

아래 단계의 많은 코드는 RTCPeerConnection 예제와 동일합니다.

`sendData()`및 `createConnection()`함수에는 새로운 코드가 입력되었습니다 :

~~~
function createConnection() {
  dataChannelSend.placeholder = '';
  var servers = null;
  pcConstraint = null;
  dataConstraint = null;
  trace('Using SCTP based data channels');
  // For SCTP, reliable and ordered delivery is true by default.
  // Add localConnection to global scope to make it visible
  // from the browser console.
  window.localConnection = localConnection =
      new RTCPeerConnection(servers, pcConstraint);
  trace('Created local peer connection object localConnection');

  sendChannel = localConnection.createDataChannel('sendDataChannel',
      dataConstraint);
  trace('Created send data channel');

  localConnection.onicecandidate = iceCallback1;
  sendChannel.onopen = onSendChannelStateChange;
  sendChannel.onclose = onSendChannelStateChange;

  // Add remoteConnection to global scope to make it visible
  // from the browser console.
  window.remoteConnection = remoteConnection =
      new RTCPeerConnection(servers, pcConstraint);
  trace('Created remote peer connection object remoteConnection');

  remoteConnection.onicecandidate = iceCallback2;
  remoteConnection.ondatachannel = receiveChannelCallback;

  localConnection.createOffer().then(
    gotDescription1,
    onCreateSessionDescriptionError
  );
  startButton.disabled = true;
  closeButton.disabled = false;
}

function sendData() {
  var data = dataChannelSend.value;
  sendChannel.send(data);
  trace('Sent Data: ' + data);
}
~~~

RTCDataChannel의 구문은 WebSocket과 의도적으로 비슷한 구조로 만드렁 졌으며 `send()`메서드 및 `message`이벤트가 있습니다.

`dataConstraint`의 사용방법. 
데이터 채널은 다양한 유형의 데이터 공유가 가능하도록 구성 할 수 있습니다 (예 : 성능 대비 안정적인 전달의 우선 순위 지정).
Mozilla Developer Network 에서 옵션에 대한 자세한 정보를 찾을 수 있습니다.

---
세 가지 종류의 제약 조건

꽤나 복잡합니다!

다른 유형의 WebRTC 통화 설정 옵션은 모두 '제약 조건'이라고도합니다.

제약 조건 및 옵션에 대해 자세히 알고 싶다면 아래를 통해 확인해 보세요.
<ul>
    <li><a href="https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/RTCPeerConnection">RTCPeerConnection</a></li>
    <li><a href="https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/createDataChannel">RTCDataChannel</a></li>
    <li><a href="https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia">getUserMedia ()</a></li>
</ul>
---

# 보너스 포인트
<ul>
    <li>
    RTCDataChannel이 안정적인 데이터 전달을 제공해야 할 때나 (심지어 데이터가 손실 되더라도) 성능이 더 중요 할 때, 
    WebRTC 데이터 채널에서 사용하는 프로토콜인 SCTP를 사용하면 신뢰할 수 있고 순서가 지정된 데이터 전달이 기본적으로 설정됩니다.
    </li>
    <li>
CSS를 사용하여 페이지 레이아웃을 개선하고 "dataChannelReceive"텍스트 영역에 placeholder속성을 추가해보세요. 
    </li>
    <li>
모바일 장치에서 페이지를 테스트해보세요.
    </li>
</ul>
    
__배운 내용__
이 단계에서는 아래의 방법을 배웠습니다.
<ul>
<li>두 WebRTC 피어 간의 연결을 설정합니다.</li>
<li>동료간에 텍스트 데이터를 교환하십시오.</li>

<ul>
이 단계의 전체 버전은 __step-03__ 폴더에 있습니다.
__더 알아보기__
<ul>
    <li><a href="https://www.html5rocks.com/en/tutorials/webrtc/datachannels/">WebRTC 데이터 채널 (최신은 아니지만 , 읽을 가지는 있습니다)</a></li>
    <li><a href="https://bloggeek.me/sctp-data-channel/">WebRTC의 데이터 채널에 SCTP가 선택된 이유는 무엇일까?</a></li>
</ul>   
    
__다음에서는__

동일한 페이지의 peer간에 데이터를 교환하는 방법을 배웠지만, 다른 컴퓨터간에이 작업을 어떻게 수행할까요?
먼저 메타 데이터 메시지를 교환하기 위해 신호 채널을 설정해야합니다. 다음 단계에서 어떻게되는지 알아보십시오!
