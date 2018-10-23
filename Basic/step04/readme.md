출처 : https://codelabs.developers.google.com/codelabs/webrtc-web/#3

# 4 . 웹캠에서 비디오 스트리밍

__배우게 될 것__

  이 단계에서는 다음과 같은 방법을 배웁니다.
  웹캠에서 비디오 스트림을 가져오기.
  스트림 재생을 조작하기.
  CSS 및 SVG를 사용하여 비디오를 조작하기.
  
이 단계의 완성 버전은 step-01 폴더에 있습니다.


__HTML작성...__

work 디렉토리의 index.html 에 video요소와 script요소를 추가합니다 .

~~~
<!DOCTYPE html>
<html>
<head>
  <title>Realtime communication with WebRTC</title>
  <link rel="stylesheet" href="css/main.css" />
</head>
<body>
  <h1>Realtime communication with WebRTC</h1>
  <video autoplay playsinline></video>
  <script src="js/main.js"></script>
</body>
</html>
</div>
~~~

__자바 스크립트 삽입__

js 폴더의 main.js 에 다음을 추가하세요.

~~~
'use strict';

// On this codelab, you will be streaming only video (video: true).
const mediaStreamConstraints = {
  video: true,
};

// Video element where stream will be placed.
const localVideo = document.querySelector('video');

// Local stream that will be reproduced on the video.
let localStream;

// Handles success by adding the MediaStream to the video element.
function gotLocalMediaStream(mediaStream) {
  localStream = mediaStream;
  localVideo.srcObject = mediaStream;
}

// Handles error by logging a message to the console with the error message.
function handleLocalMediaStreamError(error) {
  console.log('navigator.getUserMedia error: ', error);
}

// Initializes media stream.
navigator.mediaDevices.getUserMedia(mediaStreamConstraints)
  .then(gotLocalMediaStream).catch(handleLocalMediaStreamError);
~~~

`'use strict';`은 자바스크립트 예제에서 발생할수 있는 일반적인 코딩 문제를 피하기 위해 사용 됩니다.
자세히 알고 싶으면 ECMAScript 5 Strict Mode, JSON 대하여 알아보세요.

<h5>실행해 봅시다</h5>
브라우저에서 index.html 을 열면 다음과 같은 내용을 볼 수 있습니다 (물론 웹캠이 있어야만 합니다!) :
<br>
<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/fe2d10aa2ccc033.png">
<br>

__작동 원리__

`getUserMedia()`호출 후에 브라우저는 사용자에게 카메라에 액세스 할 수있는 권한을 요청합니다(현재 사용자가 처음으로 카메라 액세스가 요청 한 경우).
성공하면, `MediaStream` 이 반환되며, `MediaStream` 은 srcObject속성을 통해 미디어 요소를 사용할 수있습니다.
~~~
navigator.mediaDevices.getUserMedia(mediaStreamConstraints)
  .then(gotLocalMediaStream).catch(handleLocalMediaStreamError);


}
~~~
~~~
function gotLocalMediaStream(mediaStream) {
  localVideo.srcObject = mediaStream;
}
~~~
`constraints`인수는 미디어를 속성을 지정할 수 있습니다.
이 예제에서는 오디오 만 기본적으로 비활성화되어 있기 때문에 비디오에만 해당됩니다.

~~~
const mediaStreamConstraints = {
  video: true,
};
~~~
비디오 해상도와 같은 추가 요구 사항 또는 제약 조건을 사용할 수 있습니다.

~~~
const hdConstraints = {
  video: {
    width: {
      min: 1280
    },
    height: {
      min: 720
    }
  }
}
~~~
`MediaTrackConstraints` 은 모든 옵션은 특정 브라우저에서 지원하며, 모든 잠재적 인 제약 조건 유형을 나열합니다. 
요청한 해상도가 현재 선택된 카메라에서 지원되지 않는 경우 `getUserMedia()an OverconstrainedError`을 사용하여 거부되며
사용자에게 카메라에 액세스 할 수있는 권한을 부여하라는 메시지는 표시되지 않습니다.

---
제약 조건을 사용하여 다른 해상도를 요청하는 <a herf="https://simpl.info/getusermedia/constraints/">화면</a>.
제약 조건을 사용하여 카메라 및 마이크를 선택하는 <a href="https://simpl.info/getusermedia/sources/">화면</a>를 볼 수 있습니다 .

---
만약 `getUserMedia()`가 __성공__ 할 경우, 웹캠에서의 비디오 스트림은 __비디오 요소__ 의 소스로 설정됩니다

~~~
function gotLocalMediaStream(mediaStream) {
  localVideo.srcObject = mediaStream;
}
~~~

__추가사항__

`localStream`객체에 의해 전달 된 'getUserMedia()'는 전역 변수 이므로 브라우저 콘솔에서 해당 객체 를 검사 할 수 있습니다. 
콘솔을 열고 stream을 입력 한 다음 Return 키를 누릅니다. 
Chrome에서 콘솔을 보려면 Mac의 경우 Ctrl-Shift-J 또는 Command-Option-J를 누릅니다.

'localStream.getVideoTracks()'의 반환값을 무엇일까요 ?

'localStream.getVideoTracks()[0].stop()'을 한번 입렵해보세요.

constraints 객체를 살펴보십시오. 객체를 변경할 때 어떻게됩니까 {audio: true, video: true}?

동영상 요소의 크기는 얼마입니까? 디스플레이 크기와 달리 JavaScript에서 비디오의 자연 크기를 어떻게 얻을 수 있습니까? Chrome Dev Tools를 사용하여 확인하십시오.

video 요소에 CSS 필터를 추가해보세요:
~~~
video {
  filter: blur(4px) invert(1) opacity(0.5);
}
~~~

SVG 필터를 추가해보세요:
~~~
video {
   filter: hue-rotate(180deg) saturate(200%);
 }
 ~~~
 
배운 내용
이 단계에서는 다음을 수행하는 방법을 배웠습니다.

  웹캠에서 비디오를 가져오기.
  미디어 제약조건 설정하기.
  다양한 동영상 요소.
  
  이 단계의 완성버전은 __step-01__ 폴더에 있습니다.

__팁__

하나의 프레임만 사용하는 것이 아니라면 비디오 엘리먼트의 의 `autoplay`애트리뷰트를 잊지 마세요. 
`getUserMedia()`제약에 대한 더 많은 옵션이 있습니다. webrtc.github.io/samples/src/content/peerconnection/constraints 에서 데모를 보세요.

__우수 사례__

동영상 요소가 컨테이너보다 더 크진 않았는지 확인합니다. 
동영상의 기본 크기 및 최대 크기를 추가 width및 max-width설정했습니다. 브라우저가 자동으로 높이를 계산합니다.
~~~
video {
  max-width: 100%;
  width: 320px;
}
~~~

