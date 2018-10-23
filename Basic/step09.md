출처 : https://codelabs.developers.google.com/codelabs/webrtc-web/#8

9 . 사진을 찍어 데이터 채널을 통해 공유하십시오.

배우게 될 것
이 단계에서는 다음과 같은 방법을 배우게됩니다.

canvas 요소를 사용하여 사진을 찍은 다음 그 사진에서 데이터를 가져옵니다.
원격 사용자와 이미지 데이터 교환.
이 단계의 전체 버전은 step-06 폴더에 있습니다.

작동 원리
이전에는 RTCDataChannel을 사용하여 문자 메시지를 교환하는 방법을 배웠습니다.

이 단계에서는 전체 파일을 공유 할 수 있습니다.이 예제에서는을 (를) 통해 캡처 한 사진 getUserMedia()입니다.

이 단계의 핵심 부분은 다음과 같습니다.

데이터 채널을 설정하십시오. 이 단계에서는 피어 연결에 미디어 스트림을 추가하지 않습니다.
다음을 사용하여 사용자의 웹캠 비디오 스트림을 캡처하십시오 getUserMedia().
var video = document.getElementById('video');

function grabWebCamVideo() {
  console.log('Getting user media (video) ...');
  navigator.mediaDevices.getUserMedia({
    video: true
  })
  .then(gotStream)
  .catch(function(e) {
    alert('getUserMedia() error: ' + e.name);
  });
}
사용자가 스냅 버튼을 클릭하면 비디오 스트림에서 스냅 샷 (비디오 프레임)을 가져 와서 canvas요소에 표시합니다 .
var photo = document.getElementById('photo');
var photoContext = photo.getContext('2d');

function snapPhoto() {
  photoContext.drawImage(video, 0, 0, photo.width, photo.height);
  show(photo, sendBtn);
}
사용자가 보내기 버튼을 클릭하면 이미지를 바이트로 변환하고 데이터 채널을 통해 전송합니다.
function sendPhoto() {
  // Split data channel message in chunks of this byte length.
  var CHUNK_LEN = 64000;
  var img = photoContext.getImageData(0, 0, photoContextW, photoContextH),
    len = img.data.byteLength,
    n = len / CHUNK_LEN | 0;

  console.log('Sending a total of ' + len + ' byte(s)');
  dataChannel.send(len);

  // split the photo and send in chunks of about 64KB
  for (var i = 0; i < n; i++) {
    var start = i * CHUNK_LEN,
      end = (i + 1) * CHUNK_LEN;
    console.log(start + ' - ' + (end - 1));
    dataChannel.send(img.data.subarray(start, end));
  }

  // send the reminder, if any
  if (len % CHUNK_LEN) {
    console.log('last ' + len % CHUNK_LEN + ' byte(s)');
    dataChannel.send(img.data.subarray(n * CHUNK_LEN));
  }
}
수신 측은 데이터 채널 메시지 바이트를 이미지로 다시 변환하여 이미지를 사용자에게 표시합니다.
function receiveDataChromeFactory() {
  var buf, count;

  return function onmessage(event) {
    if (typeof event.data === 'string') {
      buf = window.buf = new Uint8ClampedArray(parseInt(event.data));
      count = 0;
      console.log('Expecting a total of ' + buf.byteLength + ' bytes');
      return;
    }

    var data = new Uint8ClampedArray(event.data);
    buf.set(data, count);

    count += data.byteLength;
    console.log('count: ' + count);

    if (count === buf.byteLength) {
      // we're done: all data chunks have been received
      console.log('Done. Rendering photo.');
      renderPhoto(buf);
    }
  };
}

function renderPhoto(data) {
  var canvas = document.createElement('canvas');
  canvas.width = photoContextW;
  canvas.height = photoContextH;
  canvas.classList.add('incomingPhoto');
  // trail is the element holding the incoming images
  trail.insertBefore(canvas, trail.firstChild);

  var context = canvas.getContext('2d');
  var img = context.createImageData(photoContextW, photoContextH);
  img.data.set(data);
  context.putImageData(img, 0, 0);
}
코드 가져 오기
작업 폴더의 내용을 단계 06 의 내용으로 바꿉니다 . 귀하 있는 index.html 에서 파일 작업은 이제 다음과 같아야합니다 :

<!DOCTYPE html>
<html>

<head>

  <title>Realtime communication with WebRTC</title>

  <link rel="stylesheet" href="/css/main.css" />

</head>

<body>

  <h1>Realtime communication with WebRTC</h1>

  <h2>
    <span>Room URL: </span><span id="url">...</span>
  </h2>

  <div id="videoCanvas">
    <video id="camera" autoplay></video>
    <canvas id="photo"></canvas>
  </div>

  <div id="buttons">
    <button id="snap">Snap</button><span> then </span><button id="send">Send</button>
    <span> or </span>
    <button id="snapAndSend">Snap &amp; Send</button>
  </div>

  <div id="incoming">
    <h2>Incoming photos</h2>
    <div id="trail"></div>
  </div>

  <script src="/socket.io/socket.io.js"></script>
  <script src="https://webrtc.github.io/adapter/adapter-latest.js"></script>
  <script src="js/main.js"></script>

</body>

</html>
작업 디렉토리 에서이 코드 랩을 따르지 않을 경우 , step-06 폴더 또는 현재 작업 폴더에 대한 종속성을 설치해야 할 수도 있습니다 . 작업 디렉토리에서 다음 명령을 실행하기 만하면됩니다.

npm install
일단 설치되면 Node.js 서버가 실행되고 있지 않으면 작업 디렉토리 에서 다음 명령을 호출하여 시작하십시오 .

node index.js
Socket.IO를 구현 하는 index.js 버전을 사용하고 있는지 확인 하고 변경 한 경우 Node.js 서버를 다시 시작하십시오. 노드 및 소켓 IO에 대한 자세한 내용은 "메시지 교환을위한 신호 서비스 설정"절을 검토하십시오.

필요한 경우 허용 버튼을 클릭 하면 앱이 웹캠을 사용할 수 있습니다.

앱에서 임의의 회의실 ID를 만들고 해당 ID를 URL에 추가합니다. 새 브라우저 탭 또는 창에서 검색 주소창의 URL을 엽니 다.

클릭 스냅 및 보내기 버튼을 누른 다음 페이지의 맨 아래에있는 다른 탭의 수신 영역을 확인합니다. 앱이 탭간에 사진을 전송합니다.

다음과 같은 내용을보아야합니다.



보너스 포인트
파일 형식을 공유 할 수 있도록 코드를 어떻게 바꿀 수 있습니까?
더 찾아 봐
MediaStream Image Capture API : 사진을 찍고 카메라를 제어하는 ​​API - 가까운 브라우저에서 곧 사용할 수 있습니다!
오디오 및 비디오 녹음을위한 MediaRecorder API : 데모 , 설명서 .
배운 내용
캔버스 요소를 사용하여 사진을 찍어서 데이터를 가져 오는 방법.
해당 데이터를 원격 사용자와 교환하는 방법.
이 단계의 전체 버전은 step-06 폴더에 있습니다.
