출처 :https://codelabs.developers.google.com/codelabs/webrtc-web/#0


# 1 . 소개

WebRTC는 웹 및 기본 응용 프로그램에서 오디오, 비디오 및 데이터의 실시간 통신을 가능하게하는 오픈 소스 프로젝트입니다.

WebRTC에는 몇 가지 JavaScript API가 있습니다.

getUserMedia(): 오디오 및 비디오 캡처.
MediaRecorder: 오디오 및 비디오를 녹음합니다.
RTCPeerConnection: 사용자 간의 오디오 및 비디오 스트리밍
RTCDataChannel: 사용자간에 데이터 스트리밍.

# WebRTC는 어디에 사용할 수 있습니까?
Firefox, Opera 및 데스크톱 및 Android 용 Chrome WebRTC는 iOS 및 Android의 기본 앱에서도 사용할 수 있습니다.

# 시그날링(Signaling)이란 무엇입니까?
WebRTC는 RTCPeerConnection을 사용하여 브라우저간에 스트리밍 데이터를 전달하지만 통신을 조정하고 제어 메시지를 전송하는 메커니즘도 필요합니다. 
하지만 Signailing 방법 및 프로토콜은 WebRTC에서 지정하지 않습니다. 본 코드 랩에서는 messaging에 Socket.IO를 사용하지만 다른 방법도 있습니다.

# STUN과 TURN은 무엇입니까?
WebRTC는 P2P (peer-to-peer) 방식으로 작동하도록 설계되었으므로 사용자는 가능한 가장 직접적인 경로로 연결할 수 있습니다.
그러나 WebRTC는 실제 네트워킹에 대처할 수 있도록 제작되었습니다. 
클라이언트 응용 프로그램은 NAT 게이트웨이 및 방화벽 을 통과해야하며 직접 연결에 실패 할 경우를 대비해 피어 투 피어 네트워킹 요구 사항을 대체해야합니다. 이 프로세스의 일부로 WebRTC API는 STUN 서버를 사용하여 컴퓨터의 IP 주소를 가져 오며, TURN 서버는 피어 투 피어 통신이 실패 할 경우 릴레이 서버로 작동합니다. ( 실제 세계의 WebRTC가 자세히 설명합니다.)

# WebRTC는 안전?
암호화는 모든 WebRTC 구성 요소에 필수이며 JavaScript API는 보안 원본 (HTTPS 또는 로컬 호스트)에서만 사용할 수 있습니다. 
신호 메커니즘은 WebRTC 표준에 의해 정의되지 않으므로 보안 프로토콜을 사용해야합니다.

더 많은 것을 찾고 계신다면? webrtc.org/start 에서 리소스를 확인해보세요.
