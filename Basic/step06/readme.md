출처 : https://codelabs.developers.google.com/codelabs/webrtc-web/#5

# 6 . RTCDataChannel을 사용하여 데이터 교환

__배우게 될 것__

WebRTC 엔드 포인트 (피어)간에 데이터를 교환하는 방법.
이 단계의 전체 버전은 step-03 폴더에 있습니다.

__HTML 업데이트__

이 단계에서는 WebRTC 데이터 채널을 사용 textarea하여 같은 페이지의 두 요소 간에 텍스트를 보냅니다 . 그다지 유용하지는 않지만 WebRTC를 사용하여 스트리밍 비디오와 데이터를 공유하는 방법을 보여줍니다.

index.html 에서 비디오 및 버튼 요소를 제거하고 다음 HTML로 대체하십시오.

<textarea id="dataChannelSend" disabled
    placeholder="Press Start, enter some text, then press Send."></textarea>
<textarea id="dataChannelReceive" disabled></textarea>

<div id="buttons">
  <button id="startButton">Start</button>
  <button id="sendButton">Send</button>
  <button id="closeButton">Stop</button>
</div>
하나의 텍스트 영역은 텍스트를 입력하기위한 것이며 다른 하나는 동료간에 스트리밍 된 텍스트를 표시합니다.

index.html 은 이제 다음과 같아야합니다.

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
자바 스크립트 업데이트

main.js 를 step-03 / js / main.js 의 내용으로 바꿉니다 .

이전 단계에서와 마찬가지로 코드 랩에서 많은 양의 코드로 잘라 붙이기를하는 것은 이상적이지 않지만 (RTCPeerConnection과 마찬가지로) 대안은 없습니다.

피어간에 스트리밍 데이터를 시험해보십시오. index.html을 열고 시작 을 눌러 피어 연결을 설정 textarea하고 왼쪽에 텍스트를 입력 한 다음 보내기 를 클릭 하여 WebRTC 데이터 채널을 사용하여 텍스트를 전송합니다.

작동 원리
이 코드는 RTCPeerConnection과 RTCDataChannel을 사용하여 문자 메시지를 교환합니다.


이 단계의 많은 코드는 RTCPeerConnection 예제와 동일합니다.


sendData()및 createConnection()기능은 새로운 코드의 대부분을 가지고 :

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
RTCDataChannel의 구문은 WebSocket과 의도적으로 비슷하며 send()메서드 및 message이벤트가 있습니다.

의 사용에 유의하십시오 dataConstraint. 데이터 채널은 다양한 유형의 데이터 공유가 가능하도록 구성 할 수 있습니다 (예 : 성능 대비 안정적인 전달의 우선 순위 지정). Mozilla Developer Network 에서 옵션에 대한 자세한 정보를 찾을 수 있습니다 .

세 가지 제약 조건

혼란스러워!

다른 유형의 WebRTC 통화 설정 옵션은 모두 '제약 조건'이라고도합니다.

제약 조건 및 옵션에 대해 자세히 알아보십시오.

RTCPeerConnection
RTCDataChannel
getUserMedia ()
보너스 포인트
WebRTC 데이터 채널에서 사용하는 프로토콜 인 SCTP를 사용하면 신뢰할 수 있고 순서가 지정된 데이터 전달이 기본적으로 설정됩니다. RTCDataChannel이 안정적인 데이터 전달을 제공해야 할 때와 성능이 더 중요 할 때 (심지어 데이터가 손실 되더라도)?
CSS를 사용하여 페이지 레이아웃을 개선하고 "dataChannelReceive"텍스트 영역에 자리 표시 자 속성을 추가하십시오.
모바일 장치에서 페이지를 테스트하십시오.
배운 내용
이 단계에서는 다음을 수행하는 방법을 배웠습니다.

두 WebRTC 피어 간의 연결을 설정합니다.
동료간에 텍스트 데이터를 교환하십시오.
이 단계의 전체 버전은 step-03 폴더에 있습니다.

더 찾아 봐
WebRTC 데이터 채널 (몇 년 된,하지만 여전히 가치가 독서)
WebRTC의 데이터 채널에 SCTP가 선택된 이유는 무엇입니까?
다음
동일한 페이지의 동료간에 데이터를 교환하는 방법을 배웠지 만, 다른 컴퓨터간에이 작업을 어떻게 수행합니까? 먼저 메타 데이터 메시지를 교환하기 위해 신호 채널을 설정해야합니다. 다음 단계에서 어떻게되는지 알아보십시오!
