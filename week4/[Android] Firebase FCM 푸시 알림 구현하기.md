안드로이드 Firebase FCM 푸시 알림을 하는 방법에 대해 공부했습니다.

<hr>
안드로이드 스튜디오는 가장 최근에 나온 범블비 버전을 사용하였다. 

- Android Studio Bumblebee (2021.1.1) 🐝

![Bumblebee_Splash](https://user-images.githubusercontent.com/70934572/153735665-d6686022-683b-4a66-b972-e7902d9cee76.png)


이것이 나중에 개발을 하며 약간의 어려움이 있었는데 기존의 다른 버전에서는 문제가 되지 않았던 것들이 범블비에서만 실행하면 문제가 발생하였다. 
[Android Developers Blog](https://android-developers.googleblog.com/2022/01/android-studio-bumblebee-202111-stable.html) 
를 보면 어떤 것이 바뀌었는지 알 수 있었지만 자잘하게 바뀐 것도 많아 [Firebase 공식 문서](https://firebase.google.com/docs/android/setup?hl=ko) 도 꼭 참고하였다.



<h3> 1. Android Studio 프로젝트와 Firebase 연결 </h3>

(1) 파이어베이스의 콘솔로 이동하여 프로젝트를 만들어준다.

![firebase_android](https://user-images.githubusercontent.com/70934572/153735888-8e4f972f-6348-42ed-8fd0-83b01365ffb4.PNG)

(2) 프로젝트가 만들어지면 안드로이드 버튼을 눌러 Android 앱에 Firebase를 추가해준다.


![앱 추가](https://user-images.githubusercontent.com/70934572/153735901-e8440311-d8d6-41c9-80e2-9f2adcd74342.PNG)


(3) Adroid 패키지 이름은 'AndoidManifest.xml'에 가면 패키지명을 확인할 수 있다.

![AndroidManifest](https://user-images.githubusercontent.com/70934572/153735849-20fc58dd-fbd3-4d30-81c4-b6935abcdc53.PNG)

(4) SHA-1은 안드로이드 스튜디오 화면 우측에 Gradle - Tasks - android - signing Report 를 눌러 확인할 수 있다.

![gradle](https://user-images.githubusercontent.com/70934572/153735947-85281d80-517c-4f33-a335-0040044322a4.PNG)
![sha](https://user-images.githubusercontent.com/70934572/153735980-4ac7691e-bc43-42fa-93d5-7dfc52a26082.PNG)

(5) 구성 파일 다운로드를 받아 안드로이드 스튜디오 app으로 이동시킨다. app으로 이동시키면 자동으로 src 폴더 밑으로 이동한다.

![json 이동](https://user-images.githubusercontent.com/70934572/153736020-080725e4-294f-49bc-9ffd-7b44bb8a46f0.PNG)

(6) Firebase SDK 를 추가한다. (이때 Firebase에서 알려준대로 하면 범블비 버전에서 문제가 발생한다.)

![gradle](https://user-images.githubusercontent.com/70934572/153736071-58e62577-ed69-4d78-8d9a-d24700c07383.PNG)


- 모듈 수준의 gradle에는 그대로 복사를 한다. 이때 apply plugin은 가장 아래에 넣는다.

![모듈](https://user-images.githubusercontent.com/70934572/153736143-e7dc34d8-5f99-403f-8e5d-0019bddef5e4.PNG)

- 프로젝트 수준의 gradle에는 다음과 같이 변경한다.

![프로젝트 수준](https://user-images.githubusercontent.com/70934572/153736160-816cf5f2-d6e4-4e2b-bd10-0359fec9caa6.PNG)


Firebase에서 작성하라는 부분은 범블비 버전에서는 settings.gradle에 있다. 

![목차](https://user-images.githubusercontent.com/70934572/153736161-e779ce5b-5af4-49da-a4b5-5b874550618c.PNG)
![settings](https://user-images.githubusercontent.com/70934572/153736162-fb080a57-ac0d-4524-95a3-a0be18cf6984.PNG)

(7) gradle을 변경하면 동기화를 해주어야 하기 때문에 상단의 코끼리🐘를 눌러 Sync Project with Gradle Files를 한다.

![sync](https://user-images.githubusercontent.com/70934572/153736297-061ce421-c65c-4fa2-b51d-3ec2155026c9.PNG)

(8) 여기까지 한 후, 빌드가 정상적으로 수행되는지 확인한다.

![build](https://user-images.githubusercontent.com/70934572/153736340-0f4b1cd7-5f12-4b54-bb4e-a6fbca2d9d3f.PNG)


(9) 정상적으로 Firebase와 연동이 되는지 확인하기 위해 안드로이드 스마트폰과 컴퓨터를 유선 연결하여 앱을 실행한다.

![핸드폰](https://user-images.githubusercontent.com/70934572/153736367-b5eed202-1e8e-4355-8e7f-6f8d0801fee9.PNG)


<hr>
<h3> 2. FCM 푸시 알림 </h3>

(1) AndroidManifest.xml에  MyFireBaseMessagingService라는 클래스를 서비스로 등록하겠다는 코드를 작성한다.

```
        <service android:name=".MyFireBaseMessagingService">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```
위치는 application 안에 넣으면 된다.

(2) AndroidManifest.xml에서 인터넷 권한을 설정해준다. 
```
<uses-permission android:name="android.permission.INTERNET"></uses-permission>
```

(3) MyFireBaseMessagingService.java라는 소스 코드를 생성하여 작성한다.
```
package com.example.dingdone;

import android.app.NotificationChannel;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Context;
import android.content.Intent;
import android.media.RingtoneManager;
import android.net.Uri;
import android.os.Build;
import android.util.Log;

import androidx.core.app.NotificationCompat;

import com.google.firebase.messaging.FirebaseMessagingService;
import com.google.firebase.messaging.RemoteMessage;

public class MyFireBaseMessagingService extends FirebaseMessagingService {
    @Override
    public void onNewToken(String token) {
        Log.d("FCM Log", "Refreshed token: " + token);
        sendRegistrationToServer(token);
    }
    private void sendRegistrationToServer(String token) {
        Log.e("FCM Log", "here ! sendRegistrationToServer! token is " + token);
    }
    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        if (remoteMessage.getNotification() != null) {
            Log.d("FCM Log", "알림 메시지: " + remoteMessage.getNotification().getBody());
            String messageBody = remoteMessage.getNotification().getBody();
            String messageTitle = remoteMessage.getNotification().getTitle();
            Intent intent = new Intent(this, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
            PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_ONE_SHOT);
            String channelId = "Channel ID";
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder =
                    new NotificationCompat.Builder(this, channelId)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle(messageTitle)
                            .setContentText(messageBody)
                            .setAutoCancel(true)
                            .setSound(defaultSoundUri)
                            .setContentIntent(pendingIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                String channelName = "Channel Name";
                NotificationChannel channel = new NotificationChannel(channelId, channelName, NotificationManager.IMPORTANCE_HIGH);
                notificationManager.createNotificationChannel(channel);
            }
            notificationManager.notify(0, notificationBuilder.build());
        }
    }
}
```

(4) FCM 알림을 눌렀을 때 열릴 화면의 java 소스코드를 수정한다. (보통 앱을 열었을 때 보이는 화면인 MainActivity.java에 수정을 한다.)

```
        FirebaseMessaging.getInstance().getToken()
                .addOnCompleteListener(new OnCompleteListener<String>() {
                    @Override
                    public void onComplete(@NonNull Task<String> task) {
                        if (!task.isSuccessful()) {
                            Log.w("FCM Log", "Fetching FCM registration token failed", task.getException());
                            return;
                        }

                        // Get new FCM registration token
                        String token = task.getResult();

                        // Log and toast

                        Log.d("FCM Log", "FCM 토큰: " + token);
                        // Toast.makeText(AlarmActivity.this, token, Toast.LENGTH_SHORT).show(); // token 화면 출력
                    }
                });
```
(5) 위 코드의 위치는 onCreate의 setContentView 아래에 넣는다. 
```
 protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 }
 ```
 
(6) Firebase의 CloudMessaging으로 이동한다.
 
![clod](https://user-images.githubusercontent.com/70934572/153736632-451fda8a-da0b-41e9-922e-257c3a8c0d30.PNG)

(7) 새 알림을 눌러 알림을 작성한다.

![알림1](https://user-images.githubusercontent.com/70934572/153736696-b3cf7d33-74da-41de-ad29-b79a25bcd01f.PNG)

(8) 알림을 보낼 타겟을 설정한다. 이때 타겟을 특정 앱으로 지정하면 해당 앱을 가지고 있는 핸드폰이라면 background 상태에서 알림을 받을 수 있다. 

![타겟](https://user-images.githubusercontent.com/70934572/153736703-52f41d93-a4e1-4cde-94c1-56ed20e79344.PNG)

(9) 알림을 보낼 시간을 설정하여 검토를 누르면 알림이 전송된다.

![예약](https://user-images.githubusercontent.com/70934572/153736728-3eaff10f-d274-499c-8d58-a59d74845463.PNG)

(10) 전송된 알림이 잘 전송되었는지 확인한다.

![알림 화면](https://user-images.githubusercontent.com/70934572/153736780-eb418d7e-420e-48b5-a8a1-699d42eaa94d.jpg)

- Firebase에서 알림에 관련한 그래프도 확인할 수 있다. 

![통계](https://user-images.githubusercontent.com/70934572/153736814-7b947e74-9312-439d-b615-9ac5762ab9af.PNG)

<hr>

추후에는 서버에서 프로그래밍적으로 푸시 알림 메시지를 보내기 위해 php를 사용할 예정이다. 
