카카오링크 API (Android) [English](https://github.com/kakao/kakaolink-android/blob/master/README-EN.md)  |  [日本語](https://github.com/kakao/kakaolink-android/blob/master/README-JP.md)
==============

카카오링크 API는 외부 앱이나 모바일 웹페이지에서 카카오톡으로 링크를 보낼 수 있고, 커스텀스킴방식으로 구현되어 어느 앱에서나 간단하게 적용할 수 있습니다.

Android에서는 [Intents and Intent Filters](http://developer.android.com/guide/components/intents-filters.html)를 사용하여 카카오링크를 호출합니다. <br />
전달하려는 링크의 종류에 따라 URL 링크와 App 링크로 구분됩니다.

카카오톡으로 URL 링크 전달
-------------
외부 앱에서 카카오톡 친구들에게 URL 링크 혹은 메세지(TEXT)를 전송할 수 있습니다.

> * 지원 OS: iOS, Android, 모바일웹(Blackberry 추후 지원 예정)

#### Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]

#### 파라미터 설명

파라미터 이름 	| type	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---		| ---				| --- 
msg			| String	| O 		| 유저에게 전달된 메세지 내용 (UTF-8) | 
url 			| String	| O		| 유저에게 전달될 메세지에 포함되는 링크 url(모바일웹) |  
appid		| String 	| O		| App의 bundle id 또는 Package id<br/>정확히 입력하지 않을 경우 이용이 제한될 수 있습니다. | 
appname		| String	| O		| 3rd party app의 정확한 이름 | 
appver		| String 	| O 		| 3rd party app의 버전	| 
type			| String	| X 		| 카카오링크 타입 		| link (고정값)  
apiver		| String 	| X		| 카카오링크 API 버전		| 2.0 (고정값)  

    
#### 사용 예

> * type, apiver 파라미터는 `KakaoLink.java`에서 처리합니다.

```java
// Recommended: Use application context for parameter.
KakaoLink kakaoLink = KakaoLink.getLink(getApplicationContext());

// check, intent is available.
if (!kakaoLink.isAvailableIntent())
  return;

/**
 * @param activity
 * @param url
 * @param message
 * @param appId
 * @param appVer
 * @param appName
 * @param encoding
 */
kakaoLink.openKakaoLink(this, 
		"http://link.kakao.com/?test-android-app", 
		"First KakaoLink Message for send url.", 
		getPackageName(), 
		getPackageManager().getPackageInfo(getPackageName(), 0).versionName, 
		"KakaoLink Test App", 
		"UTF-8");
```

카카오톡으로 APP 링크 전달
-------------
외부 앱, 모바일웹에서 카카오톡 친구들에게 해당 앱으로 바로 연결 할수 있는 링크를 전송할 수 있습니다. 링크를 받는 사람이 해당 앱을 설치하지 않은 경우 설치마켓으로 연결 할 수 있으며, 호환되지 않는 OS의 경우 URL 링크로 대체하여 전달할 수 있습니다.

> * 지원 OS: iOS, Android, 모바일웹 (Blackberry 추후 지원 예정)
> * 지원 설치마켓: Google play(Android market), App store


#### Custom URL Scheme

    kakaolink://sendurl?msg=[message]&url=[url]
    &appid=[appid]&appver=[appver]&type=[type]&appname=[appname]&apiver=[apiver]&metainfo=[metainfo]


#### 파라미터 설명

파라미터 이름 	| type	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---		| ---				| --- 
msg			| String	| O 		| 유저에게 전달된 메세지 내용 (UTF-8) | 
url 			| String	| O		| 유저에게 전달될 메세지에 포함되는 링크 url(모바일웹) |  
appid		| String 	| O		| App의 bundle id 또는 package id<br/>정확히 입력하지 않을 경우 이용이 제한될 수 있습니다. | 
appname		| String	| O		| 3rd party app의 정확한 이름 | 
appver		| String 	| O 		| 3rd party app의 버전	| 
type			| String	| X 		| 카카오링크 타입 		| link (고정값)  
apiver		| String 	| X		| 카카오링크 API 버전		| 2.0 (고정값)  
metainfo		| JSON Object |  O 	| 3rd party app을 구동시키기 위한 meta 정보 <br/>(JSONObject의 String Array 형식으로 지원) | 아래 'metainfo' 참조 


#### metainfo 설명

파라미터 이름 	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---					| --- 
os			| O 		| 3rd party app이 지원하는 OS Platform | ios 또는 android 
devicetype 	| X		| 3rd party app이 지원하는 단말의 종류 | phone 또는 pad  
installurl		| X		| 3rd party app이 설치되지 않은 경우 이동할 Google Play나 iTunes의 설치 url | 
executeurl	| O 		| 3rd party app이 설치된 경우 앱을 구동시키기 위한 url<br/>(custom scheme의 형식만 지원)| 


#### 사용 예

> * APP 링크 전달을 사용할 때에는 meta 정보를 Android, iOS 모두 만들어야 합니다.
> * `AndroidManifest.xml`에 APP 링크를 통해 실행될 수 있도록 custom scheme이 추가되어야 합니다.
> * type, apiver 파라미터는 `KakaoLink.java`에서 처리합니다.

```java
ArrayList<Map<String, String>> metaInfoArray = new ArrayList<Map<String, String>>();

// If application is support Android platform.
Map<String, String> metaInfoAndroid = new Hashtable<String, String>(1);
metaInfoAndroid.put("os", "android");
metaInfoAndroid.put("devicetype", "phone");
metaInfoAndroid.put("installurl", "market://details?id=com.kakao.talk");
metaInfoAndroid.put("executeurl", "kakaoLinkTest://startActivity");

// If application is support ios platform.
Map<String, String> metaInfoIOS = new Hashtable<String, String>(1);
metaInfoIOS.put("os", "ios");
metaInfoIOS.put("devicetype", "phone");
metaInfoIOS.put("installurl", "your iOS app install url");
metaInfoIOS.put("executeurl", "kakaoLinkTest://startActivity");

// add to array
metaInfoArray.add(metaInfoAndroid);
metaInfoArray.add(metaInfoIOS);

// Recommended: Use application context for parameter. 
KakaoLink kakaoLink = KakaoLink.getLink(getApplicationContext());

// check, intent is available.
if(!kakaoLink.isAvailableIntent()) 
  return;

/**
 * @param activity
 * @param url
 * @param message
 * @param appId
 * @param appVer
 * @param appName
 * @param encoding
 * @param metaInfoArray
 */
kakaoLink.openKakaoAppLink(
		this, 
		"http://link.kakao.com/?test-android-app", 
		"First KakaoLink Message for send app data.",  
		getPackageName(), 
		getPackageManager().getPackageInfo(getPackageName(), 0).versionName,
		"KakaoLink Test App",
		"UTF-8", 
		metaInfoArray);
```

아래는 앱을 실행하기 위해 AndroidManifest.xml 파일에 kakaoLinkTest 라는 Custom Scheme을 추가하는 예입니다. 

```xml
<!-- AndroidManifest.xml -->
<activity android:name=".MainActivity" >
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
    <!-- custom scheme(execute url) -->
    <intent-filter>
        <data android:scheme="kakaoLinkTest" android:host="startActivity" />
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.BROWSABLE" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

안드로이드 OS 공유기능을 활용하여 카카오톡으로 메시지 전달하기
---------------------------

위 방법 외에도 Android에서 제공하는 공유 기능을 활용하여 텍스트, 이미지 및 동영상을 전송할 수 있습니다. <br />
구체적인 정의는 [http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)를 참조하세요.

### 사용 예

```java
// Sending Text  
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("text/plain");
intent.putExtra(Intent.EXTRA_SUBJECT, "Subject");
intent.putExtra(Intent.EXTRA_TEXT, "Text");

// Sending Images
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("image/png");
intent.putExtra(Intent.EXTRA_STREAM, Uri.parse(path));

// Sending Video types
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("video/3gpp");
intent.putExtra(Intent.EXTRA_STREAM, Uri.parse(path));

// Add the following code if you wish to send directly to KakaoTalk
intent.setPackage("com.kakao.talk");
```

카카오 스토리로 텍스트/URL 포스팅 전송하기 
-------------
외부 앱에서 텍스트(url 포함 가능)를 카카오스토리로 포스팅할 수 있습니다. 본문에 url이 포함되어 있을 경우 해당 페이지의 썸네일 이미지, 제목, 설명이 자동으로 스크랩되어 포스팅 됩니다. 원하는 정보를 전달하려면 파라미터 중 urlinfo를 활용할 수 있습니다.
> * 지원 OS: iOS, Android, 모바일웹 
> * 카카오스토리에서의 '스크랩 타입'에 대한 정의는 [http://www.kakao.com/link/ko/api_story?tab=android](http://www.kakao.com/link/ko/api_story?tab=android) 의 내용을 참조하십시오. 

#### Custom URL Scheme

    storylink://posting?post=[post]&appid=[appid]&appver=[appver]&apiver=[apiver]&appname=[appname]&urlinfo=[urlinfo]


#### 파라미터 설명

파라미터 이름 	| type	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---		| ---				| --- 
post			| String	| O 		| 사용자 메시지 내용(UTF-8) 또는 URL<br/> * 본문에 url이 포함되어 있을 경우 해당 페이지의 썸네일 이미지, 제목, 설명이 자동으로 스크랩되어 포스팅 됩니다. 원하는 정보를 전달하려면 파라미터 중 `urlinfo`를 활용할 수 있습니다.<br/> * 본문에 url이 없는 경우엔 urlinfo를 보내도 동작하지 않습니다. | 
appid		| String 	| O		| 3rd party app의 bundle id 또는 package id<br/>정확히 입력하지 않을 경우 이용이 제한될 수 있습니다. | 
appname		| String	| O		| 3rd party app의 정확한 이름 | host(출처)로 이용되니 정확히 기재
appver		| String 	| O 		| 3rd party app Version	| 
apiver		| String 	| O		| 카카오링크 API 버전		| 1.0 (고정값)  
urlinfo		| JSON Object | X 	| 스크랩 기능이 동작될 경우 원하는 정보를 보여주기 위한 설정 정보 | 아래 'urlinfo 설명' 참조 


#### urlinfo 설명

파라미터 이름 	| 필수 여부 | 설명 				| 비고 
---			| ---		| ---					| --- 
title			| O 		| 스크랩 형태에 표시되는 제목 | 
desc		 	| X		| 스크랩 형태에 표시되는 설명  | 
imageurl		| X		| 스크랩 형태에 표시되는 대표이미지 url<br/>(JSON Object의 String Array 형식으로 지원) | 
executeurl	| X 		| 스크랩 형태에 사용되는 type | video, music, book, article, profile, website<br/>기본값은 website


#### 사용 예

```java
Map<String, Object> urlInfoAndroid = new Hashtable<String, Object>(1);
urlInfoAndroid.put("title", "(광해) 실제 역사적 진실은?");
urlInfoAndroid.put("desc", "(광해 왕이 된 남자)의 역사성 부족을 논하다.");
urlInfoAndroid.put("imageurl", new String[] {"http://m1.daumcdn.net/photo-media/201209/27/ohmynews/R_430x0_20120927141307222.jpg"});
urlInfoAndroid.put("type", "article");

// Recommended: Use application context for parameter.
StoryLink storyLink = StoryLink.getLink(getApplicationContext());

// check, intent is available.
if (!storyLink.isAvailableIntent()) {
	alert("Not installed KakaoStory.");			
	return;
}

/**
 * @param activity
 * @param post (message or url)
 * @param appId
 * @param appVer
 * @param appName
 * @param encoding
 * @param urlInfoArray
 */
storyLink.openKakaoLink(this, 
		"http://m.media.daum.net/entertain/enews/view?newsid=20120927110708426",
		getPackageName(), 
		getPackageManager().getPackageInfo(getPackageName(), 0).versionName, 
		"미디어다음",
		encoding, 
		urlInfoAndroid);
```


안드로이드 OS 공유기능을 활용하여 카카오 스토리로 포스팅 전송하기 
-------------
Android에서 제공하는 공유 기능을 활용하여 텍스트/url, 이미지를 포스팅할 수 있습니다.  
구체적인 정의는 [http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND](http://developer.android.com/reference/android/content/Intent.html#ACTION_SEND)를 참조하세요.

#### 사용 예

```java
// Image를 전송할 때
Intent intent = new Intent(Intent.ACTION_SEND);
intent.setType("image/png");
intent.putExtra(Intent.EXTRA_STREAM, Uri.parse(path));

// Kakao Story로 바로 보내시려면 아래 코드를 추가합니다.
intent.setPackage("com.kakao.story");
```

다운로드
--------------------------

### [개발자용 카카오톡 B.I 다운로드](http://www.kakao.com/link/images/v2/link/kakaotalk_icon.zip)

*B.I는 카카오링크를 이용하여 카카오톡으로 메시지를 전송하는 기능을 안내하는 버튼/페이지에 한하여 사용하셔야 하며, 다른 페이지나 메뉴 등에서 위 이미지를 그대로 또는 일부 변형하여 사용자가 카카오 제작 앱으로 혼동하게 하시면 안됩니다. 

### [개발자용 카카오스토리 B.I 다운로드](http://www.kakao.com/images/v2/link/kakaostory_icon_guide.zip)

*B.I는 스토리링크를 이용하여 카카오스토리로 포스팅하는 기능을 안내하는 버튼/페이지에 한하여 사용하셔야 하며, 다른 페이지나 메뉴 등에서 위 이미지를 그대로 또는 일부 변형하여 사용자가 카카오 제작 앱으로 혼동하게 하시면 안됩니다. 

