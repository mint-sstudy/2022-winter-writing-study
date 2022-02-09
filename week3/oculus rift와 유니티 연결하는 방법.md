우리 팀은 Oculus rift를 활용해 졸업프로젝트를 진행하고 있다. 
rift와 유니티를 연결하는 데에만 꽤 오랜시간이 걸렸어서 우리가 어떻게 연결했는지 알려주려고 한다. 

#### 실패한 방법
https://www.youtube.com/watch?v=NunJ1o6lwBk

전체적으로는 이 영상을 따라했다.(물론 이외에도 여러가지 블로그, 영상들을 참고했음에도 불구하고 실패했다..) 

순서를 보면 <br>
오큘러스 펌웨어 다운 -> 개발자용 오큘러스 유틸리티 다운 -> 오큘러스 펌웨어의 setting>general>unknown sources 체크 -> unity 실행 -> 프로젝트 실행 -> oculus utiliy 파일 import -> 확인, restart 버튼 누르기 -> 프로젝트 창에서 Assets>Oculus>Prefabs>OVRPlayerController를 hierarchy 창에 불러오기 -> Edit 창의 ProjectSettings>XR Settings>Virtual Reality Supported를 체크
순서이다. 

이렇게 했는데도 안돼서 다른 블로그들을 보고 개발자 계정 가입도 해보고 휴대폰에 앱도 깔아보는 등 많은 시도를 해봤지만 다 안됐다.. 그래서 아예 새로 시작해보기로 했다!
<br>
#### 성공한 방법
성공한 방법은 https://youtu.be/JyxbA2bm7os 이 영상을 따라했다! 

여기서는 플러그인과 툴키트를 쓴다.

아래에 쓰는 건 참고용이라 위 영상링크로 들어가 영상을 보는게 더 자세하고 도움이 될 것이다.<br>
오큘러스 펌웨어는 기본으로 다운 받은 상태로 시작하는 것 같다.<br>
먼저 유니티를 깔고,새 3D 프로젝트를 만든 후에 Edit>Project Settings> XR Plugin Management에 가서 install을 한다. 설치가 다 되면 oculus를 체크한다.<br>

다음으로 File>Build Setting에 가서 Android로 switch platform을 한다.(꽤 시간이 오래 걸린다.) <br>
Player setting에 간 후에 minimum api level을 Android 6.0 Marshmallow로 바꾼다.<br>

Window>Package Manager에 가서 XR interaction toolkit를 다운받는다. 이걸 다운 받아야 컨트롤러로 방향 조절이나 시야 조절이 가능하다. toolkit의 move,turn,left controller,right controller기능을 모두 add 한다. 

이제 시뮬레이션을 해보는 건데 우리는 이미 만들어놓은 씬이 있어서 거기서 진행했다. hierarchy창에서 오른쪽 마우스 버튼을 눌러 xr> xr origin을 넣는다. 영상에서는 xr rig로 나오는데 그 사이에 업데이트가 됐나보다. xr origin에 Input Action Manager라는 컴포넌트를 넣고 그 컴포넌트의 action asset에 xri default input actions를 넣는다. 

다음으로는 hierarchy 창에 locomotion system을 넣어야한다. 이동과 관련이 있는 시스템인 것 같다. 기본적으로 snap turn provider가 들어있는데 continuous move provider 컴포넌트도 추가해준다. snap에서는 left와 right 중 right(의 use reference)만 체크하고 move에서는 left만 체크하면 오른쪽 컨트롤러의 조이스틱에서는 snap turn이 가능하고 왼쪽 컨트롤러를 이용하면 move가 가능하다. 이제 게임을 시작해보면 잘 된다!

어떤 블로그에서 자기도 다 실패해서 플러그인을 썼다는 글을 봤었는데 우선 그 글을 무시하고 실패한 방법을 사용해봤었는데 그러지말았어야했다.. 이 영상을 보니 진짜 15분만에 돼서 너무 허무했다.. 하지만 기뻤다 ㅋㅋ ㅜㅜ

이 시행착오를 겪으면서 느꼈던 점은 초기 setup은 블로그보다는 유튜브를 따라하자라고 느꼈다. 또, 자료가 영어라고 피하지말자 라는 교훈을 얻었다.. 

아무튼 우리 팀은 oculus 연결을 마치고 개발을 잘 하고있다!

![image](https://user-images.githubusercontent.com/52192706/152649509-f9bafe65-4177-46f0-8b4e-54ce0090e09e.png)




/+ 추가적으로 vr 장비는 웬만한 노트북으로는 돌아가지않는다.. 
그래픽카드가 매우 좋아야해서 vr을 돌려보고싶다면 미리 사양을 확인해봐야한다!
우리는 학교에서 대여했다😊

