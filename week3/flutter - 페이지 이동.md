## 📌 학습 목록 📌
1. 페이지 이동
2. Splash 화면 구현

## 👩🏽‍💻 학습 내용
1. 페이지 이동
- Navigator <br/>
: Stack을 이용하여 하위 위젯(화면) 집합을 관리하는 위젯이다. 가장 최근에 방문한 페이지가 이전 페이지 위에 시각적으로 중첩되어 논리 기록을 표시하기 위해 위젯 계층 맨 위에 navigator가 있어 이전 페이지로의 이동을 시각적으로 볼 수 있다. <br/>
- Navigator.push() <br/>
: push() 메서드는 Route를 Navigator에 의해 관리되는 route 스택에 추가한다. <br/>
- Navigator.pop() <br/>
: pop() 메서드는 Navigator에 의해 관리되는 route 스택에서 현재 Route를 제거한다. <br/>
*첫 페이지를 제외하고는 appBar의 뒤로가기 버튼이 자동적으로 생성되고 pop이 들어가거나 appBar를 만들지 않게 되면 뒤로가기 버튼이 생성되지 않는다.
- Navigator.pushNamedAndRemoveUntil(context, '/', (_) => false); <br/>
지금까지의 route에 쌓인 모든 페이지를 삭제하고 싶을 경우 사용한다.

- 사용 예시 <br/>

`login.dart 일부`
``` dart
//해당 부분은 로그인 버튼을 눌렀을 때 동작하는 함수로 해당 입력 양식이 올바른지 확인하고 입력 양식이 올바를 경우, 모드를 선택할 수 있는 화면으로 전환해 주는 역할을 한다.

 void validateAndSave() {
    final form = formKey.currentState;
    if (form!.validate()) {
      form.save();
      print('Form is valid Email: $_email, password: $_password');
      
      // 현재 화면을 route 스택에서 pop하여 제거한다.
      Navigator.pop(context);
      // 불러오고 싶은 화면을 route 스택에서 push하여 추가한다. 여기에서는 selectModeMainPage를 스택에 추가한다.
      Navigator.push(context, MaterialPageRoute(builder: (context) => selectModeMainPage()),);
      //여기서는 로그인이 완료된 후에는 로그아웃을 하지 않는 이상 로그인 페이지로 돌아오지 않도록 설정해야 하므로 현재 페이지를 pop해준다.

    } else {
      print('Form is invalid Email: $_email, password: $_password');
    }
  }

```

2. Splash 화면 구현
- Splash Screen(Launch Screen): 앱을 구동할 때 보여주는 시작화면을 이야기한다.
- 구현 방법
```
1) pubspec.yaml에 이미지를 선언한다.
  - 프로젝트에 이미지를 넣을 폴더를 만들어 놓고 splash 화면에 사용할 이미지를 이미지 폴더 안에 넣어 놓는다.
  - 이미지를 선언할 때, 폴더 전체를 선언할 수 있으며 그럴 경우 해당 폴더 안에 속해 있는 이미지들을 전부 사용할 수 있게 된다.
  - 선언을 완료한 후에는 "Pub get"을 해주고 이어서 "Pub Upgrade"를 한다.
  
2) main.dart 설정
  - 스플래시 화면 이후에 나오게 할 화면의 경로를 import한다.
  - _SplashScreenState에서 timer를 설정한다. Duration()으로 시간 설정이 가능하다.
  - initState와 build를 통해 타이머 시작을 설정한다.
  - Widget build로 pubspec.yaml에서 선언해 두었던 이미지 경로를 가져온다.
```


- 사용 예시 <br/>

`pubspec.yaml 일부`
``` dart
flutter:
  uses-material-design: true
  assets:
    - images/
```

`main.dart`
``` dart

import 'dart:async';

import 'package:flutter/material.dart';
import 'Login/login.dart';

void main() {
  runApp(new MaterialApp(
    home: new SplashScreen(),
    routes: <String, WidgetBuilder>{
      // '/login'의 경우 LoginPage와 연결되도록 설정
      '/login': (BuildContext context) => new LoginPage()
    },
  ));
}

class SplashScreen extends StatefulWidget {
  @override
  _SplashScreenState createState() => new _SplashScreenState();
}

class _SplashScreenState extends State<SplashScreen> {

  startTime() async {
    // 2초가 지나면 navigationPage함수가 작동된다.
    var _duration = new Duration(seconds: 2);
    return new Timer(_duration, navigationPage);
  }

  void navigationPage() {
    // 위에 설정해 준 것을 바탕으로 LoginPage로 이동할 수 있게 됨.
    Navigator.of(context).pushReplacementNamed('/login');
  }

  @override
  void initState() {
    // 앱이 시작되면 시간을 재기 시작한다.
    super.initState();
    startTime();
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      body: new Center(
        // 앱의 로고가 스플래시 화면으로 뜨게 된다.
        child: new Image.asset('images/zerozonelogo.png'),
      ),
    );
  }
}


```

## 💥 Trouble Shooting 💥
<p>
- "unable to load asset"에러 <br />
시뮬레이터는 정상적으로 실행이 되었지만 스플래시화면에 나타나도록 설정했던 이미지가 나타나지 않고 unable to load asset이라는 메시지가 나타났다. <br/>
=> 해결방법: <br/>
<p>
1) images 폴더의 위치 오류 - lib 폴더 밖에 위치 시킨다.
<p align="center">
<img src="https://user-images.githubusercontent.com/61380136/152644132-fdcaff3b-d192-4513-853c-fb4f1516bdea.png" width="300" height="300">
</p>
 2) pubspec.yaml 파일에서 띄어쓰기 구분을 해주어야 한다. <code>assets</code> 전에 띄어쓰기 2번 혹은 탭 1번, 이후 <code>- 이미지 경로</code> 전에 띄어쓰기 4번 혹은 탭 2번을 해야 한다.
<p align="center">
<img src="https://user-images.githubusercontent.com/61380136/152644428-b2f46056-5ab4-4cb0-8457-ae44e1e0dafb.png" width="600" height="300">
</p>
</p>
</p>

## 📝 참고 자료
- [화면 이동](https://flutter-ko.dev/docs/cookbook/navigation/navigation-basics)
- [Navigator](https://api.flutter.dev/flutter/widgets/Navigator-class.html)
- [Splash 화면 구현](https://vignesh-prakash.medium.com/flutter-splash-screen-84fb0307ac55)
- [unable to load asset error](https://security-nanglam.tistory.com/479)
