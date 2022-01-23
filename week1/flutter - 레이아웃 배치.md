# 🖥 Flutter
🌟 [Flutter 강의](https://www.youtube.com/watch?v=Yt-DjG5b4iA&list=PLnIaYcDMsScxP2Nl8pEbmI__wkF0YVu0a&index=2)

1. 플러터 시작
2. 플러터 설치
3. 카운팅 앱 만들기
4. 레이아웃 튜토리얼

<br/> <br/>

# 👩🏽‍💻 강의 내용 정리
1. 플러터(Flutter)란? 
- "dart"라는 언어를 사용하는 모바일용 개발 플랫폼이다.
- IOS와 Android를 모두 한번에 개발할 수 있는 클로스 플랫폼을 지원한다.
- dart라는 언어를 사용함으로 네이티브 성능을 낼 수 있다. (네이티브 코드를 사용해 속도가 빠르다.)
 
  \* flutter 사이트: https://flutter.dev <br/>
  \* Cross Platform이란 여러 종류의 컴퓨터 플랫폼에서 동작할 수 있다는 것을 뜻하는 용어이다. (= 멀티플랫폼 소프트웨어)
![image](https://user-images.githubusercontent.com/61380136/149439313-0165491e-0821-4cfe-a757-a5bf47b9f69f.png) 

<br/> <br/>

2. 카운팅 앱 만들며 기본 구조 파악하기
```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

// st => stateless를 선택하면 stateless 코드가 생성됨.
// StatelessWidget은 바뀌지 않는 정적인 것이다.

class MyApp extends StatelessWidget {

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

//option + enter를 통해 class 생성 가능
// StatefulWidget은 바꿀 수 있는 것이다.
class MyHomePage extends StatefulWidget {
  //강의 영상과는 다르게 KEY key라 쓰면 오류가 남. - Key can't have a value of "null" (💥 trouble shooting)
  MyHomePage({Key? key, required this.title}) : super(key: key);
  final String title;
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {

  // _가 의미하는 것은 private를 의미함. - 클래스 내부에서만 사용하는 것들 (보안적인 요소)
  int _counter = 0;


  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(widget.title),),
      //가운데로 정렬
      body: Center(
        child: Column(mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          Text('You have pushed the button this many times: '),
          Text('$_counter', style: Theme.of(context).textTheme.headline4)
        ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        // 도움말
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ),
    );
  }

  void _incrementCounter() {
    setState((){
      _counter++;
    });
  }
}
```

2.1. stateless와 stateful의 차이
- stateless : 이전 상태를 기록하지 않아 작업을 진행하다 접속이 끊어질 경우, 작업을 새로이 시작해야 한다.
- stateful : 사용자의 요청을 서버가 기록하고 있어 작업을 진행하다 접속이 끊어지더라도 이전에 기록된 단계에서 부터 다시 시작하면 된다.

2.2. flutter 정렬하기 - MainAxisAlignment, CrossAxisAlignment
- mainaxis는 주축, crossaxis는 횡축을 의미한다.
- 행의 경우 main axis는 가로를 기준으로 하고 cross axis는 세로를 기준으로 한다.
- 열의 경우 main axis는 세로를 기준으로 하고 cross axis는 가로를 기준으로 한다. <br/>
![image](https://user-images.githubusercontent.com/61380136/150661275-c401acef-7315-4881-bccc-97612582eae5.png)

2.3. MainAxisAlignment와 CrossAxisAlignment 예시
- MainAxisAlignment
```
1) MainAxisAlignment.center: row일 경우, 가록축 기준으로 가운데 정렬 / column의 경우, 세로축 기준으로 가운데 정렬
2) MainAxisAlignment.start: row일 경우, 가록축 기준으로 왼쪽 정렬 / column의 경우, 세로축 기준으로 위쪽으로 정렬
3) MainAxisAlignment.end: row일 경우, 가록축 기준으로 오른쪽 정렬 / column의 경우, 세로축 기준으로 아래쪽으로 정렬
4) MainAxisAlignment.spaceEvently: child widget 사이의 여유 공간을 모두 균등하게 배분
5) MainAxisAlignment.spaceBetween: child widget을 시작과 끝에 배치하고 위젯 사이의 여유 공간을 모두 균등하게 배분
6) MainAxisAlignment.spaceAround: 첫번째와 마지막 child 앞 뒤의 여유공간을 나머지 child와 공간의 절반만큼 배치
```
- CrossAxisAlignment
```
1) CrossAxisAlignment.center: row일 경우, 가록축 기준으로 가운데 정렬 / column의 경우, 세로축 기준으로 가운데 정렬
2) CrossAxisAlignment.start: row일 경우, 가록축 기준으로 위쪽 정렬 / column의 경우, 세로축 기준으로 왼쪽 정렬
3) CrossAxisAlignment.end: row일 경우, 가록축 기준으로 아래로 정렬 / column의 경우, 세로축 기준으로 오른쪽 정렬
4) CrossAxisAlignment.stretch: 좌우를 꽉차게 배치
5) CrossAxisAlignment.baseline: row일 경우, 수평 정렬 / column의 경우, 수직 정렬
```

<br/> <br/>

2.4. 레이아웃 배치 연습
```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: MyHomePage(title: 'Flutter Demo Home Page'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key? key, required this.title}) : super(key: key);
  final String title;
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  Widget build(BuildContext context) {
    //MainAxisAlignment와 CrossAxisAlignment로 정렬할 수 있다.
    var titleSection = Row(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[
        Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: <Widget>[
            Text('Cat is so cute.',
                style: TextStyle(fontWeight: FontWeight.bold, fontSize: 26)),
            Text('This is layout practice.',
                style: TextStyle(color: Colors.grey, fontSize: 26)),
          ],
        ),
        //padding 설정
        Padding(padding: EdgeInsets.all(10.0),),
        //Icon의 크기 설정 가능
        Icon(Icons.star, size: 35, color: Colors.deepOrange,),
        Text('41', style: TextStyle(fontSize: 30),)
      ],
    );

    var buttonSection = Row(
      mainAxisAlignment: MainAxisAlignment.center,
      children: <Widget>[

        Column(
          children: <Widget>[
            Icon(Icons.call, size: 30, color: Colors.lightBlue,),
            Text("CALL", style: TextStyle(color: Colors.lightBlue),)],
        ),

        Padding(padding: EdgeInsets.all(40.0),),

        Column(children: <Widget>[
            Icon(Icons.near_me, size: 30, color: Colors.lightBlue,),
            Text("ROUTE", style: TextStyle(color: Colors.lightBlue),)],
        ),

        Padding(padding: EdgeInsets.all(40.0),),

        Column(children: <Widget>[
            Icon(Icons.share, size: 30, color: Colors.lightBlue,),
            Text("SHARE", style: TextStyle(color: Colors.lightBlue),)],
        ),
      ],
    );

    // Container에 padding을 넣을 경우, 위 아래 양 옆에 다 들어간다.
    var textSection = Container(
      child: Text(
        "The cat (Felis catus) is a domestic species of a small carnivorous mammal."
            " It is the only domesticated species in the family Felidae and "
            "is often referred to as the domestic cat to distinguish it from the wild "
            "members of the family. A cat can either be a house cat, a farm cat or"
            " a feral cat; the latter ranges freely and avoids human contact."
            " Domestic cats are valued by humans for companionship and their ability to"
            " kill rodents. About 60 cat breeds are recognized by various cat registries.",
        style: TextStyle(fontSize: 15)),
      padding: EdgeInsets.all(20.0),
    );

    return Scaffold(
      body: Column(
        //외부 링크로부터 사진을 받아와서 표시하는 것은 network로 수행한다.
        children: <Widget>[
          Image.network("https://user-images.githubusercontent.com/61380136/150347579-6cd5462e-0a75-467a-a0f2-2dd9c16d3744.png",
            height: 240, width: 600, fit: BoxFit.cover,),
          Padding(padding: EdgeInsets.all(10.0),),
          titleSection,
          Padding(padding: EdgeInsets.all(1.0),),
          buttonSection,
          //Padding(padding: EdgeInsets.all(1.0),),
          textSection,
        ],
      ),
    );
  }
}
```

<br/> <br/>

# 💥 Trouble Shooting 💥
1. Ctrl + Space 해도 어떤 속성을 사용할 수 있는지 안뜨고 Spot light가 뜨는 문제</br>
=> 환경 설정에서 해당 단축키를 변경한다.

2. Key can't have a value of "null"</br>
=> 오류 발생 원인: null safety가 활성화되었기 때문이다. null이 될 수 없는 매개변수나 키가 null 값을 가지지 않도록 한다.</br>
=> 해결방법: null이 들어갈 수 있음을 나타내도록 하는 ?와 값이 꼭 들어가야 한다고 표시해야 하는 required를 사용하여 error를 해결했다.</br>
```
# 해당 오류를 해결할 수 있는 다양한 방법
1) required
2) Default value
3) Nullable parameter
4) Positional parameters
```

<br/> <br/>

# 📝 참고 자료
- [단축키 변경](https://velog.io/@juni416/%EC%9E%90%EB%8F%99%EC%99%84%EC%84%B1%EC%9D%B4-%EC%95%88%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EB%A7%A5-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%EC%8A%A4%ED%8A%9C%EB%94%94%EC%98%A4
) <br/>
- [stateless와 stateful](https://www.redhat.com/ko/topics/cloud-native-apps/stateful-vs-stateless)
- [MainAxisAlignment, CrossAxisAlignment](https://beomseok95.tistory.com/310)
- [Key can't have a value of "null"](https://stackoverflow.com/questions/64560461/the-parameter-cant-have-a-value-of-null-because-of-its-type-in-dart)
- [플러터 스튜디오](https://flutterstudio.app/) => 레이아웃 익히기

