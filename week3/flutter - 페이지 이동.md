## ๐ ํ์ต ๋ชฉ๋ก ๐
1. ํ์ด์ง ์ด๋
2. Splash ํ๋ฉด ๊ตฌํ

## ๐ฉ๐ฝโ๐ป ํ์ต ๋ด์ฉ
1. ํ์ด์ง ์ด๋
- Navigator <br/>
: Stack์ ์ด์ฉํ์ฌ ํ์ ์์ ฏ(ํ๋ฉด) ์งํฉ์ ๊ด๋ฆฌํ๋ ์์ ฏ์ด๋ค. ๊ฐ์ฅ ์ต๊ทผ์ ๋ฐฉ๋ฌธํ ํ์ด์ง๊ฐ ์ด์  ํ์ด์ง ์์ ์๊ฐ์ ์ผ๋ก ์ค์ฒฉ๋์ด ๋ผ๋ฆฌ ๊ธฐ๋ก์ ํ์ํ๊ธฐ ์ํด ์์ ฏ ๊ณ์ธต ๋งจ ์์ navigator๊ฐ ์์ด ์ด์  ํ์ด์ง๋ก์ ์ด๋์ ์๊ฐ์ ์ผ๋ก ๋ณผ ์ ์๋ค. <br/>
- Navigator.push() <br/>
: push() ๋ฉ์๋๋ Route๋ฅผ Navigator์ ์ํด ๊ด๋ฆฌ๋๋ route ์คํ์ ์ถ๊ฐํ๋ค. <br/>
- Navigator.pop() <br/>
: pop() ๋ฉ์๋๋ Navigator์ ์ํด ๊ด๋ฆฌ๋๋ route ์คํ์์ ํ์ฌ Route๋ฅผ ์ ๊ฑฐํ๋ค. <br/>
*์ฒซ ํ์ด์ง๋ฅผ ์ ์ธํ๊ณ ๋ appBar์ ๋ค๋ก๊ฐ๊ธฐ ๋ฒํผ์ด ์๋์ ์ผ๋ก ์์ฑ๋๊ณ  pop์ด ๋ค์ด๊ฐ๊ฑฐ๋ appBar๋ฅผ ๋ง๋ค์ง ์๊ฒ ๋๋ฉด ๋ค๋ก๊ฐ๊ธฐ ๋ฒํผ์ด ์์ฑ๋์ง ์๋๋ค.
- Navigator.pushNamedAndRemoveUntil(context, '/', (_) => false); <br/>
์ง๊ธ๊น์ง์ route์ ์์ธ ๋ชจ๋  ํ์ด์ง๋ฅผ ์ญ์ ํ๊ณ  ์ถ์ ๊ฒฝ์ฐ ์ฌ์ฉํ๋ค.

- ์ฌ์ฉ ์์ <br/>

`login.dart ์ผ๋ถ`
``` dart
//ํด๋น ๋ถ๋ถ์ ๋ก๊ทธ์ธ ๋ฒํผ์ ๋๋ ์ ๋ ๋์ํ๋ ํจ์๋ก ํด๋น ์๋ ฅ ์์์ด ์ฌ๋ฐ๋ฅธ์ง ํ์ธํ๊ณ  ์๋ ฅ ์์์ด ์ฌ๋ฐ๋ฅผ ๊ฒฝ์ฐ, ๋ชจ๋๋ฅผ ์ ํํ  ์ ์๋ ํ๋ฉด์ผ๋ก ์ ํํด ์ฃผ๋ ์ญํ ์ ํ๋ค.

 void validateAndSave() {
    final form = formKey.currentState;
    if (form!.validate()) {
      form.save();
      print('Form is valid Email: $_email, password: $_password');
      
      // ํ์ฌ ํ๋ฉด์ route ์คํ์์ popํ์ฌ ์ ๊ฑฐํ๋ค.
      Navigator.pop(context);
      // ๋ถ๋ฌ์ค๊ณ  ์ถ์ ํ๋ฉด์ route ์คํ์์ pushํ์ฌ ์ถ๊ฐํ๋ค. ์ฌ๊ธฐ์์๋ selectModeMainPage๋ฅผ ์คํ์ ์ถ๊ฐํ๋ค.
      Navigator.push(context, MaterialPageRoute(builder: (context) => selectModeMainPage()),);
      //์ฌ๊ธฐ์๋ ๋ก๊ทธ์ธ์ด ์๋ฃ๋ ํ์๋ ๋ก๊ทธ์์์ ํ์ง ์๋ ์ด์ ๋ก๊ทธ์ธ ํ์ด์ง๋ก ๋์์ค์ง ์๋๋ก ์ค์ ํด์ผ ํ๋ฏ๋ก ํ์ฌ ํ์ด์ง๋ฅผ popํด์ค๋ค.

    } else {
      print('Form is invalid Email: $_email, password: $_password');
    }
  }

```

2. Splash ํ๋ฉด ๊ตฌํ
- Splash Screen(Launch Screen): ์ฑ์ ๊ตฌ๋ํ  ๋ ๋ณด์ฌ์ฃผ๋ ์์ํ๋ฉด์ ์ด์ผ๊ธฐํ๋ค.
- ๊ตฌํ ๋ฐฉ๋ฒ
```
1) pubspec.yaml์ ์ด๋ฏธ์ง๋ฅผ ์ ์ธํ๋ค.
  - ํ๋ก์ ํธ์ ์ด๋ฏธ์ง๋ฅผ ๋ฃ์ ํด๋๋ฅผ ๋ง๋ค์ด ๋๊ณ  splash ํ๋ฉด์ ์ฌ์ฉํ  ์ด๋ฏธ์ง๋ฅผ ์ด๋ฏธ์ง ํด๋ ์์ ๋ฃ์ด ๋๋๋ค.
  - ์ด๋ฏธ์ง๋ฅผ ์ ์ธํ  ๋, ํด๋ ์ ์ฒด๋ฅผ ์ ์ธํ  ์ ์์ผ๋ฉฐ ๊ทธ๋ด ๊ฒฝ์ฐ ํด๋น ํด๋ ์์ ์ํด ์๋ ์ด๋ฏธ์ง๋ค์ ์ ๋ถ ์ฌ์ฉํ  ์ ์๊ฒ ๋๋ค.
  - ์ ์ธ์ ์๋ฃํ ํ์๋ "Pub get"์ ํด์ฃผ๊ณ  ์ด์ด์ "Pub Upgrade"๋ฅผ ํ๋ค.
  
2) main.dart ์ค์ 
  - ์คํ๋์ ํ๋ฉด ์ดํ์ ๋์ค๊ฒ ํ  ํ๋ฉด์ ๊ฒฝ๋ก๋ฅผ importํ๋ค.
  - _SplashScreenState์์ timer๋ฅผ ์ค์ ํ๋ค. Duration()์ผ๋ก ์๊ฐ ์ค์ ์ด ๊ฐ๋ฅํ๋ค.
  - initState์ build๋ฅผ ํตํด ํ์ด๋จธ ์์์ ์ค์ ํ๋ค.
  - Widget build๋ก pubspec.yaml์์ ์ ์ธํด ๋์๋ ์ด๋ฏธ์ง ๊ฒฝ๋ก๋ฅผ ๊ฐ์ ธ์จ๋ค.
```


- ์ฌ์ฉ ์์ <br/>

`pubspec.yaml ์ผ๋ถ`
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
      // '/login'์ ๊ฒฝ์ฐ LoginPage์ ์ฐ๊ฒฐ๋๋๋ก ์ค์ 
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
    // 2์ด๊ฐ ์ง๋๋ฉด navigationPageํจ์๊ฐ ์๋๋๋ค.
    var _duration = new Duration(seconds: 2);
    return new Timer(_duration, navigationPage);
  }

  void navigationPage() {
    // ์์ ์ค์ ํด ์ค ๊ฒ์ ๋ฐํ์ผ๋ก LoginPage๋ก ์ด๋ํ  ์ ์๊ฒ ๋จ.
    Navigator.of(context).pushReplacementNamed('/login');
  }

  @override
  void initState() {
    // ์ฑ์ด ์์๋๋ฉด ์๊ฐ์ ์ฌ๊ธฐ ์์ํ๋ค.
    super.initState();
    startTime();
  }

  @override
  Widget build(BuildContext context) {
    return new Scaffold(
      body: new Center(
        // ์ฑ์ ๋ก๊ณ ๊ฐ ์คํ๋์ ํ๋ฉด์ผ๋ก ๋จ๊ฒ ๋๋ค.
        child: new Image.asset('images/zerozonelogo.png'),
      ),
    );
  }
}


```

## ๐ฅ Trouble Shooting ๐ฅ
<p>
- "unable to load asset"์๋ฌ <br />
์๋ฎฌ๋ ์ดํฐ๋ ์ ์์ ์ผ๋ก ์คํ์ด ๋์์ง๋ง ์คํ๋์ํ๋ฉด์ ๋ํ๋๋๋ก ์ค์ ํ๋ ์ด๋ฏธ์ง๊ฐ ๋ํ๋์ง ์๊ณ  unable to load asset์ด๋ผ๋ ๋ฉ์์ง๊ฐ ๋ํ๋ฌ๋ค. <br/>
=> ํด๊ฒฐ๋ฐฉ๋ฒ: <br/>
<p>
1) images ํด๋์ ์์น ์ค๋ฅ - lib ํด๋ ๋ฐ์ ์์น ์ํจ๋ค.
<p align="center">
<img src="https://user-images.githubusercontent.com/61380136/152644132-fdcaff3b-d192-4513-853c-fb4f1516bdea.png" width="300" height="300">
</p>
 2) pubspec.yaml ํ์ผ์์ ๋์ด์ฐ๊ธฐ ๊ตฌ๋ถ์ ํด์ฃผ์ด์ผ ํ๋ค. <code>assets</code> ์ ์ ๋์ด์ฐ๊ธฐ 2๋ฒ ํน์ ํญ 1๋ฒ, ์ดํ <code>- ์ด๋ฏธ์ง ๊ฒฝ๋ก</code> ์ ์ ๋์ด์ฐ๊ธฐ 4๋ฒ ํน์ ํญ 2๋ฒ์ ํด์ผ ํ๋ค.
<p align="center">
<img src="https://user-images.githubusercontent.com/61380136/152644428-b2f46056-5ab4-4cb0-8457-ae44e1e0dafb.png" width="600" height="300">
</p>
</p>
</p>

## ๐ ์ฐธ๊ณ  ์๋ฃ
- [ํ๋ฉด ์ด๋](https://flutter-ko.dev/docs/cookbook/navigation/navigation-basics)
- [Navigator](https://api.flutter.dev/flutter/widgets/Navigator-class.html)
- [Splash ํ๋ฉด ๊ตฌํ](https://vignesh-prakash.medium.com/flutter-splash-screen-84fb0307ac55)
- [unable to load asset error](https://security-nanglam.tistory.com/479)
