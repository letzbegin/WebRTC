출처 : https://codelabs.developers.google.com/codelabs/webrtc-web/#2

# 3 . 샘플 코드 얻기

__코드 다운로드하기__

git에 익숙하다면 GitHub에서 복제하여 코드 테이블의 코드를 다운로드 할 수 있습니다.

<div height="50px" background-color="black" color="white">
git clone https://github.com/googlecodelabs/webrtc-web
</div>

또는 "소스코드 다운로드"을 클릭하여 코드의 .zip 파일을 다운로드하십시오.

<p>
<a href="https://github.com/googlecodelabs/webrtc-web/archive/master.zip">소스 코드 다운로드</a>
<p>

다운로드 한 zip 파일을 엽니다. 
그러면 필요한 모든 리소스와 함께이 코드 랩의 각 단계에 대해 하나의 폴더가 포함 된 프로젝트 폴더 ( 적응 형 웹 미디어 )의 압축이 풀 립니다.

work라는 디렉토리에서 모든 코딩 작업을 수행할 것입니다.

step-** 폴더는이 코드 랩의 각 단계에 대한 완성 된 버전이 포함되어 있습니다. 참조용으로 사용하시면 됩니다.

__웹 서버 설치 및 확인__

자신 만의 웹 서버를 자유롭게 사용할 수 있지만이 본 코드 랩은 Chrome 웹 서버를 이용하여 작동하도록 설계되었습니다.
해당 앱을 아직 설치하지 않은 경우 Chrome 웹 스토어에서 설치할 수 있습니다.


<br/>
<a href="https://chrome.google.com/webstore/detail/web-server-for-chrome/ofhbbkphhbklhfoeikjpcbhemlocgigb?hl=en">Chrome 용 웹 서버 설치</a>
<br/>

<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/6ddeb4aee53c0f0e.png">



Chrome 용 웹 서버 앱 을 설치 한 후 북마크 바, 새 탭 페이지 또는 앱 실행기에서 Chrome 앱 바로 가기를 클릭합니다.
<br/>
<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/bab91398f0bf59f5.png">
<br/>
웹 서버 아이콘을 클릭하십시오.
<br/>
<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/60da10ee57cbb190.png">
<br/>

다음으로 로컬 웹 서버를 구성 할 수있는이 대화 상자가 표시됩니다.
<br/>
<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/433870360ad308d4.png">
<br/>


[ 폴더 선택] 단추를 클릭하고 방금 만든 작업 폴더를 선택하세요. 
그러면 웹 서버 창에 있는 웹 서버 URL 섹션에서 강조 표시된 URL을 통해 Chrome에서 진행중인 작업을 볼 수 있습니다 .

아래 옵션을 통해, index.html에 자동으로 상태를 표시할 수 있습니다 :

<br>
<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/8937a38abc57e3.png">
<br>
그런 다음 Web Server 레이블이 표시된 토글을 오른쪽에서 왼쪽으로 슬라이딩하여 서버를 중지했다가 왼쪽에서 오른쪽으로 슬라이딩 하여 다시 시작하십시오. 

<br>
<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/daefd30e8a290df5.png">
<br>


이제 강조 표시된 웹 서버 URL을 클릭하여 웹 브라우저에서 작업 사이트를 방문해 보세요.
work/index.html에 다음과 같은 페이지가 표시되어야합니다.

<br>
<img src="https://codelabs.developers.google.com/codelabs/webrtc-web/img/a803d28bc7109d5c.png">
<br>

분명히이 앱은 아직 흥미로운 일을하지 못하고 있습니다. 
지금까지 웹 서버가 제대로 작동하는지 확인하기 위해 사용하는 최소한의 구조일뿐입니다. 
이후 단계에서 기능 및 레이아웃 기능을 추가 할 것입니다.

앞으로 이 웹 서버 설정을 사용하여 모든 테스트 및 확인을 수행해야합니다.
대개 테스트 브라우저 탭을 새로 고침하는 것만으로 수행 .
