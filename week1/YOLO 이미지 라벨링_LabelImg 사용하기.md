졸업프로젝트에서 사물인식 파트를 맡아 라벨링을 했는데 라벨링을 한 과정을 정리하고자 한다.

---
# YOLO 이미지 라벨링

일반적으로 Object Detection은 바운딩 박스(Bounding Box)를 찾는 것을 의미하는데 미리 만들어진 라벨링 이미지를 사용하거나 자신이 직접 라벨링을 할 수도 있다. 
<br>이때 자신만의 custom dataset을 만드는 경우 LabelImg를 이용할 수 있다. 

## LabelImg
사진을 한 장씩 올려서 직접 사각형 바운딩 박스를 그리는 방식으로 특히 PascalVoc나 Yolo와 같은 dataset의 형식에 맞게 dataset을 만들 수 있다.
* PascalVoc로 라벨링 > .json 파일 생성
* Yolo로 라벨링 > .txt 파일 생성


### LabelImg 설치 방법

#### 1. 아래 링크에서 자신의 환경에 맞는 프로그램을 설치한다.
[https://tzutalin.github.io/labelImg/](https://tzutalin.github.io/labelImg/)
<br>![설치](https://user-images.githubusercontent.com/70934572/150662152-2865911a-84ec-4157-9e6b-ab77a86139c6.PNG)


#### 2. 설치를 하면 아래과 같은 아이콘의 프로그램이 생기는데 바로 실행하면 사용가능하다.
![icon](https://user-images.githubusercontent.com/70934572/150662218-28d3238c-b283-45ef-81fa-a7168d68dde8.PNG)


#### 3. 프로그램을 실행하면 아래와 같은 화면이 나온다. 
![시작화면](https://user-images.githubusercontent.com/70934572/150662422-b1a18b4e-3b39-4d61-9bdb-dfe7e7016a69.PNG)


## Image Labelling

#### 1. LabelImg 설정을 변경한다.
![1](https://user-images.githubusercontent.com/70934572/150662441-3f1a6f26-8e14-4a9e-a007-283a15c6308d.PNG)

* Open : 라벨링할 이미지 열기 (한 장)
* Open Dir : 라벨링할 이미지들이 모여있는 폴더 열기
* Change Sav Dir : 라벨링한 결과들이 저장될 폴더 설정
* Next Image / Prev Image : 이미지 순서 이동
* Save : 저장 (한 장씩 라벨링을 할 때마다 반드시 저장하기)
* YOLO / Pascal Voc : 라벨링할 형식 지정 

#### 2. CreateRectBox로 라벨링한다.
![2](https://user-images.githubusercontent.com/70934572/150662562-ca4dbd2a-a717-4859-a080-c8e11450c21c.PNG)

위와 같이 생긴 것을 누르거나, 단축키 'w'를 눌러 라벨링 가능하다.

먼저 불을 fire 라는 클래스명으로 지정하여 라벨링하였다.

<br>![fire](https://user-images.githubusercontent.com/70934572/150662686-93880edc-4667-486d-bf25-33a2ef310717.gif)

라벨링을 하면 아래와 같이 폴더에 저장이 된다. (여기서는 라벨링한 결과들을 라벨링한 이미지들과 같은 폴더에 지정을 했다.)
![3](https://user-images.githubusercontent.com/70934572/150662822-4bf166c8-9ba4-42c0-a582-196c1c770646.PNG)

<br>라벨링을 하면 다음과 같은 파일이 생긴다. (여기서는 YOLO로 하였기에 .txt 파일에 저장이 되었다.)
<br>![라벨링](https://user-images.githubusercontent.com/70934572/150662864-db496ad8-ee72-4d19-9cf4-bc5e49fddadb.PNG)
<br>맨 앞의 숫자인 0은 fire 클래스를 나타내고 그 뒤는 이미지에서의 위치를 나타낸다.

같은 방법으로 연기도 라벨링을 한다.

<br>![smoke](https://user-images.githubusercontent.com/70934572/150662743-0c207c1e-c139-4663-8c09-77b7ad15af39.gif)

라벨링을 할 때 주의할 점은 클래스를 생성하는 순서인데 순서를 다르게 하면 이후에 라벨링이 저장된 파일에서 클래스의 순서가 기존에 라벨링한 것들과 달라질 수 있다.

여기서는 fire를 먼저 라벨링하고, smoke를 라벨링 했기 때문에 classes.txt 파일에 fire가 먼저 클래스로 지정되어 있는 것을 볼 수 있다.
![4](https://user-images.githubusercontent.com/70934572/150662806-3cb07c80-7014-47bc-9d3f-60198fa88804.PNG)

예를 들어, 위에서 fire라는 클래스 보다 smoke 라는 클래스를 먼저 라벨링하면 smoke가 0, fire가 1이 되어버린다. 

<br>🚨 또한, 약간의 번거로움이 있지만 라벨링을 한 장 한 장 할 때마다 반드시 저장을 해야한다. 🚨
<br>처음에 이 사실을 모르고 열심히 라벨링을 하다가 2-30장을 날려버렸다. 
