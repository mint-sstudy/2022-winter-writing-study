# ๐ฅ Flutter
๐ [Flutter ๊ฐ์](https://www.youtube.com/watch?v=Yt-DjG5b4iA&list=PLnIaYcDMsScxP2Nl8pEbmI__wkF0YVu0a&index=2)

1. ํ๋ฌํฐ ์์
2. ํ๋ฌํฐ ์ค์น
3. ์นด์ดํ ์ฑ ๋ง๋ค๊ธฐ
4. ๋ ์ด์์ ํํ ๋ฆฌ์ผ

<br/> <br/>

# ๐ฉ๐ฝโ๐ป ๊ฐ์ ๋ด์ฉ ์ ๋ฆฌ
1. ํ๋ฌํฐ(Flutter)๋? 
- "dart"๋ผ๋ ์ธ์ด๋ฅผ ์ฌ์ฉํ๋ ๋ชจ๋ฐ์ผ์ฉ ๊ฐ๋ฐ ํ๋ซํผ์ด๋ค.
- IOS์ Android๋ฅผ ๋ชจ๋ ํ๋ฒ์ ๊ฐ๋ฐํ  ์ ์๋ ํด๋ก์ค ํ๋ซํผ์ ์ง์ํ๋ค.
- dart๋ผ๋ ์ธ์ด๋ฅผ ์ฌ์ฉํจ์ผ๋ก ๋ค์ดํฐ๋ธ ์ฑ๋ฅ์ ๋ผ ์ ์๋ค. (๋ค์ดํฐ๋ธ ์ฝ๋๋ฅผ ์ฌ์ฉํด ์๋๊ฐ ๋น ๋ฅด๋ค.)
 
  \* flutter ์ฌ์ดํธ: https://flutter.dev <br/>
  \* Cross Platform์ด๋ ์ฌ๋ฌ ์ข๋ฅ์ ์ปดํจํฐ ํ๋ซํผ์์ ๋์ํ  ์ ์๋ค๋ ๊ฒ์ ๋ปํ๋ ์ฉ์ด์ด๋ค. (= ๋ฉํฐํ๋ซํผ ์ํํธ์จ์ด)
![image](https://user-images.githubusercontent.com/61380136/149439313-0165491e-0821-4cfe-a757-a5bf47b9f69f.png) 

<br/> <br/>

2. ์นด์ดํ ์ฑ ๋ง๋ค๋ฉฐ ๊ธฐ๋ณธ ๊ตฌ์กฐ ํ์ํ๊ธฐ
```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

// st => stateless๋ฅผ ์ ํํ๋ฉด stateless ์ฝ๋๊ฐ ์์ฑ๋จ.
// StatelessWidget์ ๋ฐ๋์ง ์๋ ์ ์ ์ธ ๊ฒ์ด๋ค.

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

//option + enter๋ฅผ ํตํด class ์์ฑ ๊ฐ๋ฅ
// StatefulWidget์ ๋ฐ๊ฟ ์ ์๋ ๊ฒ์ด๋ค.
class MyHomePage extends StatefulWidget {
  //๊ฐ์ ์์๊ณผ๋ ๋ค๋ฅด๊ฒ KEY key๋ผ ์ฐ๋ฉด ์ค๋ฅ๊ฐ ๋จ. - Key can't have a value of "null" (๐ฅ trouble shooting)
  MyHomePage({Key? key, required this.title}) : super(key: key);
  final String title;
  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {

  // _๊ฐ ์๋ฏธํ๋ ๊ฒ์ private๋ฅผ ์๋ฏธํจ. - ํด๋์ค ๋ด๋ถ์์๋ง ์ฌ์ฉํ๋ ๊ฒ๋ค (๋ณด์์ ์ธ ์์)
  int _counter = 0;


  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(widget.title),),
      //๊ฐ์ด๋ฐ๋ก ์ ๋ ฌ
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
        // ๋์๋ง
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

2.1. stateless์ stateful์ ์ฐจ์ด
- stateless : ์ด์  ์ํ๋ฅผ ๊ธฐ๋กํ์ง ์์ ์์์ ์งํํ๋ค ์ ์์ด ๋์ด์ง ๊ฒฝ์ฐ, ์์์ ์๋ก์ด ์์ํด์ผ ํ๋ค.
- stateful : ์ฌ์ฉ์์ ์์ฒญ์ ์๋ฒ๊ฐ ๊ธฐ๋กํ๊ณ  ์์ด ์์์ ์งํํ๋ค ์ ์์ด ๋์ด์ง๋๋ผ๋ ์ด์ ์ ๊ธฐ๋ก๋ ๋จ๊ณ์์ ๋ถํฐ ๋ค์ ์์ํ๋ฉด ๋๋ค.

2.2. flutter ์ ๋ ฌํ๊ธฐ - MainAxisAlignment, CrossAxisAlignment
- mainaxis๋ ์ฃผ์ถ, crossaxis๋ ํก์ถ์ ์๋ฏธํ๋ค.
- ํ์ ๊ฒฝ์ฐ main axis๋ ๊ฐ๋ก๋ฅผ ๊ธฐ์ค์ผ๋ก ํ๊ณ  cross axis๋ ์ธ๋ก๋ฅผ ๊ธฐ์ค์ผ๋ก ํ๋ค.
- ์ด์ ๊ฒฝ์ฐ main axis๋ ์ธ๋ก๋ฅผ ๊ธฐ์ค์ผ๋ก ํ๊ณ  cross axis๋ ๊ฐ๋ก๋ฅผ ๊ธฐ์ค์ผ๋ก ํ๋ค. <br/>
![image](https://user-images.githubusercontent.com/61380136/150661275-c401acef-7315-4881-bccc-97612582eae5.png)

2.3. MainAxisAlignment์ CrossAxisAlignment ์์
- MainAxisAlignment
```
1) MainAxisAlignment.center: row์ผ ๊ฒฝ์ฐ, ๊ฐ๋ก์ถ ๊ธฐ์ค์ผ๋ก ๊ฐ์ด๋ฐ ์ ๋ ฌ / column์ ๊ฒฝ์ฐ, ์ธ๋ก์ถ ๊ธฐ์ค์ผ๋ก ๊ฐ์ด๋ฐ ์ ๋ ฌ
2) MainAxisAlignment.start: row์ผ ๊ฒฝ์ฐ, ๊ฐ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์ผ์ชฝ ์ ๋ ฌ / column์ ๊ฒฝ์ฐ, ์ธ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์์ชฝ์ผ๋ก ์ ๋ ฌ
3) MainAxisAlignment.end: row์ผ ๊ฒฝ์ฐ, ๊ฐ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์ค๋ฅธ์ชฝ ์ ๋ ฌ / column์ ๊ฒฝ์ฐ, ์ธ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์๋์ชฝ์ผ๋ก ์ ๋ ฌ
4) MainAxisAlignment.spaceEvently: child widget ์ฌ์ด์ ์ฌ์  ๊ณต๊ฐ์ ๋ชจ๋ ๊ท ๋ฑํ๊ฒ ๋ฐฐ๋ถ
5) MainAxisAlignment.spaceBetween: child widget์ ์์๊ณผ ๋์ ๋ฐฐ์นํ๊ณ  ์์ ฏ ์ฌ์ด์ ์ฌ์  ๊ณต๊ฐ์ ๋ชจ๋ ๊ท ๋ฑํ๊ฒ ๋ฐฐ๋ถ
6) MainAxisAlignment.spaceAround: ์ฒซ๋ฒ์งธ์ ๋ง์ง๋ง child ์ ๋ค์ ์ฌ์ ๊ณต๊ฐ์ ๋๋จธ์ง child์ ๊ณต๊ฐ์ ์ ๋ฐ๋งํผ ๋ฐฐ์น
```
- CrossAxisAlignment
```
1) CrossAxisAlignment.center: row์ผ ๊ฒฝ์ฐ, ๊ฐ๋ก์ถ ๊ธฐ์ค์ผ๋ก ๊ฐ์ด๋ฐ ์ ๋ ฌ / column์ ๊ฒฝ์ฐ, ์ธ๋ก์ถ ๊ธฐ์ค์ผ๋ก ๊ฐ์ด๋ฐ ์ ๋ ฌ
2) CrossAxisAlignment.start: row์ผ ๊ฒฝ์ฐ, ๊ฐ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์์ชฝ ์ ๋ ฌ / column์ ๊ฒฝ์ฐ, ์ธ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์ผ์ชฝ ์ ๋ ฌ
3) CrossAxisAlignment.end: row์ผ ๊ฒฝ์ฐ, ๊ฐ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์๋๋ก ์ ๋ ฌ / column์ ๊ฒฝ์ฐ, ์ธ๋ก์ถ ๊ธฐ์ค์ผ๋ก ์ค๋ฅธ์ชฝ ์ ๋ ฌ
4) CrossAxisAlignment.stretch: ์ข์ฐ๋ฅผ ๊ฝ์ฐจ๊ฒ ๋ฐฐ์น
5) CrossAxisAlignment.baseline: row์ผ ๊ฒฝ์ฐ, ์ํ ์ ๋ ฌ / column์ ๊ฒฝ์ฐ, ์์ง ์ ๋ ฌ
```

<br/> <br/>

2.4. ๋ ์ด์์ ๋ฐฐ์น ์ฐ์ต
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
    //MainAxisAlignment์ CrossAxisAlignment๋ก ์ ๋ ฌํ  ์ ์๋ค.
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
        //padding ์ค์ 
        Padding(padding: EdgeInsets.all(10.0),),
        //Icon์ ํฌ๊ธฐ ์ค์  ๊ฐ๋ฅ
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

    // Container์ padding์ ๋ฃ์ ๊ฒฝ์ฐ, ์ ์๋ ์ ์์ ๋ค ๋ค์ด๊ฐ๋ค.
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
        //์ธ๋ถ ๋งํฌ๋ก๋ถํฐ ์ฌ์ง์ ๋ฐ์์์ ํ์ํ๋ ๊ฒ์ network๋ก ์ํํ๋ค.
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

# ๐ฅ Trouble Shooting ๐ฅ
1. Ctrl + Space ํด๋ ์ด๋ค ์์ฑ์ ์ฌ์ฉํ  ์ ์๋์ง ์๋จ๊ณ  Spot light๊ฐ ๋จ๋ ๋ฌธ์ </br>
=> ํ๊ฒฝ ์ค์ ์์ ํด๋น ๋จ์ถํค๋ฅผ ๋ณ๊ฒฝํ๋ค.

2. Key can't have a value of "null"</br>
=> ์ค๋ฅ ๋ฐ์ ์์ธ: null safety๊ฐ ํ์ฑํ๋์๊ธฐ ๋๋ฌธ์ด๋ค. null์ด ๋  ์ ์๋ ๋งค๊ฐ๋ณ์๋ ํค๊ฐ null ๊ฐ์ ๊ฐ์ง์ง ์๋๋ก ํ๋ค.</br>
=> ํด๊ฒฐ๋ฐฉ๋ฒ: null์ด ๋ค์ด๊ฐ ์ ์์์ ๋ํ๋ด๋๋ก ํ๋ ?์ ๊ฐ์ด ๊ผญ ๋ค์ด๊ฐ์ผ ํ๋ค๊ณ  ํ์ํด์ผ ํ๋ required๋ฅผ ์ฌ์ฉํ์ฌ error๋ฅผ ํด๊ฒฐํ๋ค.</br>
```
# ํด๋น ์ค๋ฅ๋ฅผ ํด๊ฒฐํ  ์ ์๋ ๋ค์ํ ๋ฐฉ๋ฒ
1) required
2) Default value
3) Nullable parameter
4) Positional parameters
```

<br/> <br/>

# ๐ ์ฐธ๊ณ  ์๋ฃ
- [๋จ์ถํค ๋ณ๊ฒฝ](https://velog.io/@juni416/%EC%9E%90%EB%8F%99%EC%99%84%EC%84%B1%EC%9D%B4-%EC%95%88%EB%90%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EB%A7%A5-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%EC%8A%A4%ED%8A%9C%EB%94%94%EC%98%A4
) <br/>
- [stateless์ stateful](https://www.redhat.com/ko/topics/cloud-native-apps/stateful-vs-stateless)
- [MainAxisAlignment, CrossAxisAlignment](https://beomseok95.tistory.com/310)
- [Key can't have a value of "null"](https://stackoverflow.com/questions/64560461/the-parameter-cant-have-a-value-of-null-because-of-its-type-in-dart)
- [ํ๋ฌํฐ ์คํ๋์ค](https://flutterstudio.app/) => ๋ ์ด์์ ์ตํ๊ธฐ

