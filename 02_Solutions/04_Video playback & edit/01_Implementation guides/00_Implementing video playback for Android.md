This article introduces how to implement video playback features in your Android app.
# Development environment

* Android Studio Arctic Fox or higher version. We highly recommend using the latest version of Android Studio to ensure optimal performance.
* A physical mobile device running Android 5.0 or above. Building on emulators is not currently supported.
* Ensure that the CPU architecture you are using is either armv7 or arm64.

# Prerequisites

* A valid [BytePlus account](http://console.byteplus.com/) with [BytePlus VOD](https://console.byteplus.com/vodpaas) activated.
* Complete the following steps on the [SDK management](https://console.byteplus.com/vodpaas/sdk/) page within the BytePlus VOD console:
   * Create an app and obtain the app ID along with the English name of the app.
   * Register for the app, then proceed to purchase and download a license for using the Player SDK and Upload SDK. For the Player SDK, we recommend using the Premium Edition.

# Version requirements

* To access the **video playback,** and **media upload** features, you must use BytePlus VOD SDK. Please refer to the [Integration](#integration) section for the correct version number of each SDK you may use.

> For the corresponding versions of the SDKs used in each solution, please refer to the [Client SDK components](https://docs.byteplus.com/en/byteplus-vos/docs/version-combination?version=v1.0#a934220b).

# Integration
## Step 1: Add the Maven repositories
Open your project's `build.gradle` file located in the project's root directory, and add the repository locations inside the `repositories` block, as shown below:
```Groovy
allprojects { 
    repositories { 
        google() 
        mavenCentral() 
        // BytePlus VOD SDK maven repository
        maven { url 'https://artifact.byteplus.com/repository/public/' }
        maven { url 'https://artifact.bytedance.com/repository/Volcengine/' }
    } 
} 

// BytePlus VOD SDK maven repository
apply from: 'https://ve-vos.volccdn.com/script/vevos-repo-base.gradle'
```

## Step 2: Integrate the SDKs
To integrate the SDKs into your project, add the dependency for the desired SDK to the `dependencies` block in your module-level `build.gradle` file, as shown below:
```Groovy
dependencies { 
    // BytePlus VOD SDK dependencies
    ttsdkVersion = '1.43.300.3'
    implementation "com.bytedanceapi:ttsdk-player_premium:$ttsdkVersion"
    implementation "com.bytedanceapi:ttsdk-ttuploader:$ttsdkVersion"
    implementation 'com.bytedance.applog:RangersAppLog-Lite-global:6.14.3' 
    implementation 'com.squareup.okhttp3:okhttp:4.12.0'
}
```

## Step 3: Declare app permissions
Declare the permissions required by SDKs in `AndroidManifest.xml` located in the "app" module of your project, as shown below:
```XML
<!-- BytePlus VOD SDK -->
<uses-permission android:name="android.permission.INTERNET" /> 
<uses-permission android:name="android.permission.WAKE_LOCK" /> 
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" /> 
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /> 
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
```

In addition, permission to write to storage must be requested at runtime. For details, see the [Request runtime permissions](https://developer.android.com/training/permissions/requesting) in Android developer guides.
If your app targets Android 10 (API level 29) and does not use Scoped Storage, you must include the attribute `android:requestLegacyExternalStorage="true"` in your app's `AndroidManifest.xml` file:
```XML
<manifest> 
  <application android:requestLegacyExternalStorage="true">         
  </application> 
</manifest>
```

If your app targets Android 11 (API level 30) or higher, your app must support Scoped Storage. Please refer to [Temporarily opt-out of scoped storage](https://developer.android.com/training/data-storage/use-cases#opt-out-scoped-storage) for more details.
## Step 4: Add obfuscation rules
To prevent SDK-related classes from being obfuscated, add the following rules to the `proguard-rules.pro` file.
```Plain
# For BytePlus VOD Player
-keep class com.ss.ttm.** {*;}  
-keep class com.ss.ttvideoengine.** {*;}  
-keep class com.ss.mediakit.** {*;}  
-keep class com.ss.texturerender.** {*;} 
-keep class com.bytedance.**{*;} 
-keep class com.pandora.ttlicense2.**{*;} 
-keep class com.pandora.common.applog.**{*;} 
-keep class com.pandora.vod.VodSDK {*;} 

# For BytePlus VOD Upload
-keep class com.pandora.common.applog.**{*;} 
-keep class com.pandora.ttuploader2.** {*;} 
-keep class com.ss.bduploader.** {*;}
```

# Implementation
## Video Playback
 
### Sequence diagram
<img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB2ZXJzaW9uPSIxLjEiIHdpZHRoPSI1NjVweCIgaGVpZ2h0PSIzMjVweCIgdmlld0JveD0iLTAuNSAtMC41IDU2NSAzMjUiPjxkZWZzLz48Zz48cmVjdCB4PSIxNjIiIHk9IjIiIHdpZHRoPSI4MCIgaGVpZ2h0PSI0MCIgZmlsbD0iI2NjZmY5OSIgc3Ryb2tlPSIjMDAwMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggZD0iTSAyMDIgNDIgTCAyMDIgMzIyIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgc3Ryb2tlLWRhc2hhcnJheT0iMyAzIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNzhweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAyMnB4OyBtYXJnaW4tbGVmdDogMTYzcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IGZvbnQtd2VpZ2h0OiBib2xkOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj5BcHAvV2ViIFNlcnZlcjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHJlY3QgeD0iMTk3IiB5PSI4MiIgd2lkdGg9IjEwIiBoZWlnaHQ9IjkwIiBmaWxsPSIjZmZmZmZmIiBzdHJva2U9IiMwMDAwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCB4PSIzMjIiIHk9IjIiIHdpZHRoPSI4MCIgaGVpZ2h0PSI0MCIgZmlsbD0iIzk5ZmY5OSIgc3Ryb2tlPSIjMDAwMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggZD0iTSAzNjIgNDIgTCAzNjIgMzIyIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgc3Ryb2tlLWRhc2hhcnJheT0iMyAzIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNzhweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAyMnB4OyBtYXJnaW4tbGVmdDogMzIzcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IGZvbnQtd2VpZ2h0OiBib2xkOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj5WT0QgUGxheWVyIFNESzwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHJlY3QgeD0iNDgyIiB5PSIyIiB3aWR0aD0iODAiIGhlaWdodD0iNDAiIGZpbGw9IiM5OWZmOTkiIHN0cm9rZT0iIzAwMDAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gNTIyIDQyIEwgNTIyIDMyMiIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHN0cm9rZS1kYXNoYXJyYXk9IjMgMyIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDc4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMjJweDsgbWFyZ2luLWxlZnQ6IDQ4M3B4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDEycHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyBmb250LXdlaWdodDogYm9sZDsgd2hpdGUtc3BhY2U6IG5vcm1hbDsgd29yZC13cmFwOiBub3JtYWw7ICI+TWVkaWEgU2VydmVyPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48cmVjdCB4PSI1MTciIHk9IjE5MiIgd2lkdGg9IjEwIiBoZWlnaHQ9IjgwIiBmaWxsPSIjZmZmZmZmIiBzdHJva2U9IiMwMDAwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBkPSJNIDQyIDkyIEwgMTg5LjM4IDkyIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGQ9Ik0gMTk2LjM4IDkyIEwgMTg5LjM4IDk1LjUgTCAxODkuMzggODguNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBmbGV4LWVuZDsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogMXB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDg5cHg7IG1hcmdpbi1sZWZ0OiAxMjBweDsiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6IGJvcmRlci1ib3g7IGZvbnQtc2l6ZTogMDsgdGV4dC1hbGlnbjogY2VudGVyOyAiPjxkaXYgc3R5bGU9ImRpc3BsYXk6IGlubGluZS1ibG9jazsgZm9udC1zaXplOiAxMnB4OyBmb250LWZhbWlseTogSGVsdmV0aWNhOyBjb2xvcjogIzAwMDAwMDsgbGluZS1oZWlnaHQ6IDEuMjsgcG9pbnRlci1ldmVudHM6IGFsbDsgYmFja2dyb3VuZC1jb2xvcjogI2ZmZmZmZjsgd2hpdGUtc3BhY2U6IG5vd3JhcDsgIj5SZXF1ZXN0IGEgUGxheUF1dGggVG9rZW48L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PC9nPjxyZWN0IHg9IjIiIHk9IjIiIHdpZHRoPSI4MCIgaGVpZ2h0PSI0MCIgZmlsbD0iIzY2ZmZmZiIgc3Ryb2tlPSIjMDAwMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggZD0iTSA0MiA0MiBMIDQyIDMyMiIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHN0cm9rZS1kYXNoYXJyYXk9IjMgMyIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDc4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMjJweDsgbWFyZ2luLWxlZnQ6IDNweDsiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6IGJvcmRlci1ib3g7IGZvbnQtc2l6ZTogMDsgdGV4dC1hbGlnbjogY2VudGVyOyAiPjxkaXYgc3R5bGU9ImRpc3BsYXk6IGlubGluZS1ibG9jazsgZm9udC1zaXplOiAxMnB4OyBmb250LWZhbWlseTogSGVsdmV0aWNhOyBjb2xvcjogIzAwMDAwMDsgbGluZS1oZWlnaHQ6IDEuMjsgcG9pbnRlci1ldmVudHM6IGFsbDsgZm9udC13ZWlnaHQ6IGJvbGQ7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPkFwcC9XZWIgQ2xpZW50PC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48cmVjdCB4PSIzNyIgeT0iODIiIHdpZHRoPSIxMCIgaGVpZ2h0PSIxNTAiIGZpbGw9IiNmZmZmZmYiIHN0cm9rZT0iIzAwMDAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gNDcgMjAyLjAyIEwgMzUzLjg4IDIwMS4wMyIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDM2MC44OCAyMDEgTCAzNTMuODkgMjA0LjUzIEwgMzUzLjg3IDE5Ny41MyBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBmbGV4LWVuZDsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogMXB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDE5OXB4OyBtYXJnaW4tbGVmdDogMjA1cHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IGJhY2tncm91bmQtY29sb3I6ICNmZmZmZmY7IHdoaXRlLXNwYWNlOiBub3dyYXA7ICI+PGRpdiBzdHlsZT0idGV4dC1hbGlnbjpsZWZ0Ij48c3BhbiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjpyZ2IoMjQ4LCAyNDksIDI1MCkiPlBhc3MgdGhlIFBsYXlBdXRoIFRva2VuPC9zcGFuPjwvZGl2PjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHBhdGggZD0iTSA0OC40OCAxNjEuNDYgTCAxOTcuNSAxNjIuNTYiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDU0LjM4IDE1OCBMIDQ3LjM2IDE2MS40NSBMIDU0LjMzIDE2NSIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgZmxleC1lbmQ7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDFweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxNTlweDsgbWFyZ2luLWxlZnQ6IDEyMnB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDEycHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyBiYWNrZ3JvdW5kLWNvbG9yOiAjZmZmZmZmOyB3aGl0ZS1zcGFjZTogbm93cmFwOyAiPjxmb250IHN0eWxlPSJmb250LXNpemU6MTJweCI+UmV0dXJuIHRoZSBQbGF5QXV0aCBUb2tlbjxiciBzdHlsZT0iZm9udC1zaXplOjEycHgiIC8+PC9mb250PjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHJlY3QgeD0iMjEyIiB5PSI5OCIgd2lkdGg9IjExMCIgaGVpZ2h0PSI2MCIgZmlsbD0iI2ZmZmZmZiIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgY2VudGVyOyBqdXN0aWZ5LWNvbnRlbnQ6IHVuc2FmZSBjZW50ZXI7IHdpZHRoOiAxMDhweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxMjhweDsgbWFyZ2luLWxlZnQ6IDIxM3B4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDEycHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj5Mb2NhbGx5IGNoZWNrIG91dCB0aGUgUGxheUF1dGggVG9rZW48L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PC9nPjxwYXRoIGQ9Ik0gMzY0LjE4IDIwMSBMIDUwNy42MyAyMDEiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggZD0iTSA1MTIuODggMjAxIEwgNTA1Ljg4IDIwNC41IEwgNTA3LjYzIDIwMSBMIDUwNS44OCAxOTcuNSBaIiBmaWxsPSIjMDAwMDAwIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IHg9IjM1OSIgeT0iMTgyIiB3aWR0aD0iMTYwIiBoZWlnaHQ9IjIwIiBmaWxsPSJub25lIiBzdHJva2U9Im5vbmUiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgY2VudGVyOyBqdXN0aWZ5LWNvbnRlbnQ6IHVuc2FmZSBjZW50ZXI7IHdpZHRoOiAxNThweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxOTJweDsgbWFyZ2luLWxlZnQ6IDM2MHB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDEycHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj5PYnRhaW4gcGxheWJhY2sgaW5mb3JtYXRpb248L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PC9nPjxwYXRoIGQ9Ik0gNTE0IDI2MiBMIDM3MC4zNyAyNjIiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDM2NS4xMiAyNjIgTCAzNzIuMTIgMjU4LjUgTCAzNzAuMzcgMjYyIEwgMzcyLjEyIDI2NS41IFoiIGZpbGw9IiMwMDAwMDAiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHJlY3QgeD0iMzU5IiB5PSIyNDIiIHdpZHRoPSIxNjAiIGhlaWdodD0iMjAiIGZpbGw9Im5vbmUiIHN0cm9rZT0ibm9uZSIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDE1OHB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDI1MnB4OyBtYXJnaW4tbGVmdDogMzYwcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPlJldHVybiBwbGF5YmFjayBpbmZvcm1hdGlvbjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PC9nPjwvc3ZnPg==" width="565px" />

### Step 1: Initialize the SDK
We recommend that you initialize the SDK in the `onCreate` method of the `Application` class. See the following sample code:
```Java
File videoCacheDir = new File(context.getCacheDir(), "video_cache"); 
if (!videoCacheDir.exists()) videoCacheDir.mkdirs(); 
VodConfig.Builder vodBuilder = new VodConfig.Builder(context) 
        .setCacheDirPath(videoCacheDir.getAbsolutePath()) 
        .setMaxCacheSize(300 * 1024 * 1024); 
         
Env.init(new Config.Builder() 
        .setApplicationContext(context) 
        .setAppID("Your app ID") 
        .setAppName("Your app English name") 
        .setAppVersion(BuildConfig.VERSION_NAME) 
        .setAppChannel("channel name") 
        .setLicenseUri("assets:///license/vod.lic") 
        .setVodConfig(vodBuilder.build()) 
        .build());
```

When initializing the SDK, set the following parameters:
| **Name** | **Type** | **Required** | **Default Value** | **Description** |
| --- | --- | --- | --- | --- |
| AppID | String | Yes | None | The app ID. You can get the app ID on the **SDK management** > **Applications** page in the [BytePlus VOD console](https://console.byteplus.com/vodpaas/). |
| AppName | String | Yes | None | The name of the app. You can get the app name on the **SDK management** > **Applications** page in the [BytePlus VOD console](https://console.byteplus.com/vodpaas/). |
| AppVersion | String | Yes | None | The version number of the app. A valid version number should contain two or more delimiters, such as "1.3.2". |
| AppChannel | String | Yes | None | The name of the channel where your app is distributed, such as Google Play. |
| LicenseUri | String | Yes | None | The path to your license. You need to put your license in the app/assets folder, and then set the LicenseUri parameter as the path to your license, such as assets/license/vod.lic. <br> For more information on the license, see [Android Player SDK: License guide](https://docs.byteplus.com/en/docs/byteplus-vod/docs-license-guide-android). |
| CacheDirPath | String | No | /data/user/0/<package_name>/cache/video_cache | The path for caching videos. |
| MaxCacheSize | Int | No | 300 * 1024 * 1024 (300 MB) | The maximum size in bytes of the folder for caching videos. |
### Step 2: Create a player instance
Create a player instance with the `TTVideoEngine` class, as follows:
```Java
// When creating the instance, pass in the app context 
TTVideoEngine ttVideoEngine = new TTVideoEngine(context, TTVideoEngine.PLAYER_TYPE_OWN);
```

### Step 3: Set up a view for displaying the video
To display the video, bind the player with either a texture view or a surface view.
#### Bind the player with a texture view

1. Declare a texture view in XML, as follows:
   ```XML
   <TextureView
       android:id="@+id/textureView"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:background="@null" />
   ```

   If your app targets Android API level 24 or higher, when declaring the texture view, you should not set `background` or must set `background` as `null`. Otherwise, the app will crash.
2. Get the `SurfaceTexture` instance of the texture view that you declare and pass it to `TTVideoEngine`. The `SurfaceTexture` can then be used to render the video from `TTVideoEngine`. See the following sample code:
   ```Java
   TextureView textureView = findViewById(R.id.textureView);
   // Set the listener for the TextureView's SurfaceTexture
   textureView.setSurfaceTextureListener(new TextureView.SurfaceTextureListener() {
       @Override
       // Called when the TextureView's SurfaceTexture is ready for use
       public void onSurfaceTextureAvailable(SurfaceTexture surfaceTexture, int width, int height) {
           // Pass the TextureView's SurfaceTexture to the player
           ttVideoEngine.setSurface(new Surface(surfaceTexture));
       }
       @Override
       public void onSurfaceTextureSizeChanged(SurfaceTexture surfaceTexture, int width, int height) { 
       }
       @Override
       public boolean onSurfaceTextureDestroyed(SurfaceTexture surfaceTexture) {
           return true;
       }
       @Override
       public void onSurfaceTextureUpdated(SurfaceTexture surfaceTexture) {
       }
   });
   ```


#### Bind the player with a surface view

1. Declare a surface view in XML, as follows:
   ```XML
   <SurfaceView
       android:id="@+id/surfaceView"
       android:layout_width="match_parent"
       android:layout_height="match_parent" />
   ```

2. The `SurfaceView` provides a dedicated drawing surface. You can get the underlying surface via the `SurfaceHolder` interface, which can be retrieved by calling `getHolder()`. Pass the `SurfaceHolder` interface to `TTVideoEngine`, and then the `SurfaceView` can display the video from `TTVideoEngine`. See the following sample code:
   ```Java
   SurfaceView surfaceView = findViewById(R.id.surfaceView); 
   // Pass the SurfaceHolder to TTVideoEngine 
   ttVideoEngine.setSurfaceHolder(surfaceView.getHolder());
   ```


> Notes:

> * The `ttVideoEngine.setSurface` and `ttVideoEngine.setSurfaceHolder` methods must be called before `ttVideoEngine.play`.
> * Starting from the Android API level 24, SurfaceView supports synchronizing the rendering position in the window with other views in the view tree. If your app targets Android API level 24 or lower, view hierarchy issues and out-of-sync animations may occur. For details, see [SurfaceView](https://developer.android.com/reference/android/view/SurfaceView.html) in the Android API reference.

### Step 4: Set the video source
The SDK supports playing both local and online videos. This section introduces how to play videos from different sources.
#### Play a video with the video ID
If you upload your videos to the BytePlus VOD service, you can play them using the associated video IDs. You need to set the `vid` and `playAuthToken` parameters, which can be retrieved from your app server.
See the following sample code:
```Java
// Enable caching the video model
ttVideoEngine.setIntOption(PLAYER_OPTION_USE_VIDEOMODEL_CACHE, 1);

// Get the video ID from your app server
final String vid = "your video ID";
// Get the playback token from your app server
final String playAuthToken = "your playback token";

final int encodeType = TTVideoEngine.CODEC_TYPE_H264;
// final int encodeType = TTVideoEngine.CODEC_TYPE_H265;

// 1. Assemble the video source
StrategySource vidSource = new VidPlayAuthTokenSource.Builder()
        .setVid(vid)
        .setPlayAuthToken(playAuthToken)
        // Set the codec type. The default value is TTVideoEngine.CODEC_TYPE_H264
        .setEncodeType(encodeType)
        // Set the resolution when the video playback initiates. The default resolution is 360P
        // Here we set the resolution to 480p
        .setResolution(Resolution.High)
        .build();
// 2. Set the video source
ttVideoEngine.setStrategySource(vidSource);
// 3. Play the video
ttVideoEngine.play()
```

When assembling the video source, set the following parameters:
| **Parameter** | **Type** | **Required** | **Default Value** | **Description** |
| --- | --- | --- | --- | --- |
| Vid | String | Yes | None | The video ID. |
| PlayAuthToken | String | Yes | None | The playback token. |
| EncodeType | Int | No | CODEC_TYPE_H264 | The codec type: <br>  <br> * H.264 (Default) <br> * ByteVC1 (Only available in the Premium Edition of the SDK) |
| Resolution | Resolution | No | Standard | The video resolution. See the table below. |
Resolution
| Member | Video quality | Audio quality |
| --- | --- | --- |
| Standard | 360p, SD (Standard Definition) | Normal |
| High | 480p, HD (High Definition) | High |
| SuperHigh | 720p, FHD (Full HD) | Very high |
| ExtremelyHigh | 1080p | Original |
| TwoK | 2K | This parameter does not affect audio quality. |
| FourK | 4K | This parameter does not affect audio quality. |
| Auto | For DASH videos, the player automatically decides the best resolution based on the viewer's network connection and adjusts the quality of the video throughout playback to minimize the risk of buffering. | This parameter does not affect audio quality. |
#### Play a video with the HTTP URL
If you upload your videos to the BytePlus VOD service or a third-party storage service, you can play them using the HTTP URL. You need to set the `url` parameter.
See the following sample code:
```Java
// The unique identifier of the video.
final String vid = "video id";
// The HTTP URL of the video.
final String url = "http://www.example.com/h264.mp4";
final String cacheKey = TTVideoEngine.computeMD5(url);

// 1. Assemble the video source
StrategySource directUrlSource = new DirectUrlSource.Builder()
        .setVid(vid)
        .addItem(new DirectUrlSource.UrlItem.Builder()
                .setUrl(url)
                .setCacheKey(cacheKey)
                .build())
        .build();
       
// 2. Set the video source
ttVideoEngine.setStrategySource(directUrlSource);
// 3. Play the video
ttVideoEngine.play()
```

When assembling the video source, set the following parameters:
| **Parameter** | **Type** | **Required** | **Default Value** | **Description** |
| --- | --- | --- | --- | --- |
| Vid | String | Yes | None | The video ID. |
| Url | String | Yes | None | The HTTP URL of the video. |
| CacheKey | String | Yes | None | The cache key, which must meet the following requirements: <br>  <br> * It should not contain any special characters and can be used as a file name. <br> * Every video has a unique cache key. <br>  <br> It is recommended to use the MD5 of the URL as the cache key. |
#### Play a local video
If you play a local video, assign the local address to the `videoFile` parameter. See the following sample code:
```Java
// Replace "/sdcard/Download/video.MP4" with the local video file path 
File videoFile = new File("/sdcard/Download/video.MP4"); 
ttVideoEngine.setLocalURL(videoFile.getAbsolutePath()); 
ttVideoEngine.play() 
```

### Step 5: Control video playback
The `TTVideoEngine` class provides the following methods for controlling video playback:

* Call `play` to start or resume playback.
   ```Java
   ttVideoEngine.play();
   ```

* Call `pause` to pause playback. Call `play` to resume playback.
   ```Java
   ttVideoEngine.pause();
   ttVideoEngine.play();
   ```

* Call `seekTo` to navigate to a specific time position. This functionality allows users to manually adjust the video playback by dragging the progress bar to fast-forward or rewind the video.
   ```Java
   // The offset from the start to seek to is 1000 milliseconds
   ttVideoEngine.seekTo(1000, new SeekCompletionListener() {
       @Override
       // Occurs when a seek operation completes
       // The success parameter indicates whether the seek operation succeeds
       public void onCompletion(boolean success) {
       }
   });
   
   ttVideoEngine.setVideoEngineInfoListener(new VideoEngineInfoListener() {
       @Override
       // The SDK starts rendering after the seek operation
       // When the rendering completes, the SDK triggers this callback
       public void onVideoEngineInfos(VideoEngineInfos videoEngineInfos) {
           if (TextUtils.equals(videoEngineInfos.getKey(), VideoEngineInfos.USING_RENDER_SEEK_COMPLETE)) {
           }
       }
   });
   ```

* Call `setStartTime` before `play` to specify the time position at which the playback starts. This method can be used for the "Skip Intro" feature.
   ```Java
   int startPlayPositionMS = 1000;
   // Set the start time as one second
   ttVideoEngine.setStartTime(startPlayPositionMS);
   ttVideoEngine.play();
   ```

* Call `stop` to stop the playback.
   ```Java
   ttVideoEngine.stop();
   ```


### Step 6: Release the player instance
When the video ends, or the user navigates away from the page where the video is being played, you can stop playback and release the player instance by calling `releaseAsync`. This releases the hardware decoder, memory, and network resources used by `ttVideoEngine` to conserve power.
After the player instance is released, you can no longer call methods in the `ttVideoEngine` class. It is also recommended to set `ttVideoEngine` as `null` to ensure that there are no references to the released player instance. This helps prevent any potential issues related to accessing a released player instance or consuming unnecessary memory resources.
```Java
ttVideoEngine.releaseAsync(); 
ttVideoEngine = null;
```

VOD Player SDK also provides a wide range of advanced features. For further details, please refer to the following topics:

* [Android Player SDK: Basic features](https://docs.byteplus.com/en/docs/byteplus-vod/docs-implementing-basic-features-android)
* [Android Player SDK: Advanced features](https://docs.byteplus.com/en/docs/byteplus-vod/docs-advanced-features-android)

## Media Upload
This section introduces how to use the VOD Upload SDK.
### Sequence diagram
<img src="data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB2ZXJzaW9uPSIxLjEiIHdpZHRoPSI1NjVweCIgaGVpZ2h0PSI0MDVweCIgdmlld0JveD0iLTAuNSAtMC41IDU2NSA0MDUiPjxkZWZzLz48Zz48cmVjdCB4PSIxNjIiIHk9IjIiIHdpZHRoPSI4MCIgaGVpZ2h0PSI0MCIgZmlsbD0iI2NjZmY5OSIgc3Ryb2tlPSIjMDAwMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggZD0iTSAyMDIgNDIgTCAyMDIgNDAyIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgc3Ryb2tlLWRhc2hhcnJheT0iMyAzIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNzhweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAyMnB4OyBtYXJnaW4tbGVmdDogMTYzcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IGZvbnQtd2VpZ2h0OiBib2xkOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj5BcHAvV2ViIFNlcnZlcjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHJlY3QgeD0iMTk3IiB5PSI4MiIgd2lkdGg9IjEwIiBoZWlnaHQ9IjkwIiBmaWxsPSIjZmZmZmZmIiBzdHJva2U9IiMwMDAwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCB4PSIzMjIiIHk9IjIiIHdpZHRoPSI4MCIgaGVpZ2h0PSI0MCIgZmlsbD0iIzk5ZmY5OSIgc3Ryb2tlPSIjMDAwMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggZD0iTSAzNjIgNDIgTCAzNjIgNDAyIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgc3Ryb2tlLWRhc2hhcnJheT0iMyAzIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogNzhweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAyMnB4OyBtYXJnaW4tbGVmdDogMzIzcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IGZvbnQtd2VpZ2h0OiBib2xkOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj5WT0QgVXBsb2FkIFNESzwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHJlY3QgeD0iNDgyIiB5PSIyIiB3aWR0aD0iODAiIGhlaWdodD0iNDAiIGZpbGw9IiM5OWZmOTkiIHN0cm9rZT0iIzAwMDAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gNTIyIDQyIEwgNTIyIDQwMiIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHN0cm9rZS1kYXNoYXJyYXk9IjMgMyIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDc4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMjJweDsgbWFyZ2luLWxlZnQ6IDQ4M3B4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDEycHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyBmb250LXdlaWdodDogYm9sZDsgd2hpdGUtc3BhY2U6IG5vcm1hbDsgd29yZC13cmFwOiBub3JtYWw7ICI+TWVkaWEgU2VydmVyPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48cmVjdCB4PSI1MTciIHk9IjE5MiIgd2lkdGg9IjEwIiBoZWlnaHQ9IjE5MCIgZmlsbD0iI2ZmZmZmZiIgc3Ryb2tlPSIjMDAwMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggZD0iTSA0MiA5MiBMIDE4OS4zOCA5MiIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDE5Ni4zOCA5MiBMIDE4OS4zOCA5NS41IEwgMTg5LjM4IDg4LjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgZmxleC1lbmQ7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDFweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiA4OXB4OyBtYXJnaW4tbGVmdDogMTIwcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IGJhY2tncm91bmQtY29sb3I6ICNmZmZmZmY7IHdoaXRlLXNwYWNlOiBub3dyYXA7ICI+UmVxdWVzdCBhbiA8YnIgLz5VcGxvYWRBdXRoIFRva2VuPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48cmVjdCB4PSIyIiB5PSIyIiB3aWR0aD0iODAiIGhlaWdodD0iNDAiIGZpbGw9IiM2NmZmZmYiIHN0cm9rZT0iIzAwMDAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGQ9Ik0gNDIgNDIgTCA0MiA0MDIiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgY2VudGVyOyBqdXN0aWZ5LWNvbnRlbnQ6IHVuc2FmZSBjZW50ZXI7IHdpZHRoOiA3OHB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDIycHg7IG1hcmdpbi1sZWZ0OiAzcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IGZvbnQtd2VpZ2h0OiBib2xkOyB3aGl0ZS1zcGFjZTogbm9ybWFsOyB3b3JkLXdyYXA6IG5vcm1hbDsgIj5BcHAvV2ViIENsaWVudDwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHJlY3QgeD0iMzciIHk9IjgyIiB3aWR0aD0iMTAiIGhlaWdodD0iMTUwIiBmaWxsPSIjZmZmZmZmIiBzdHJva2U9IiMwMDAwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBkPSJNIDQ3IDIwMi4wMiBMIDM1My44OCAyMDEuMDMiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggZD0iTSAzNjAuODggMjAxIEwgMzUzLjg5IDIwNC41MyBMIDM1My44NyAxOTcuNTMgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgZmxleC1lbmQ7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDFweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxOTlweDsgbWFyZ2luLWxlZnQ6IDIwNXB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDEycHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyBiYWNrZ3JvdW5kLWNvbG9yOiAjZmZmZmZmOyB3aGl0ZS1zcGFjZTogbm93cmFwOyAiPjxkaXYgc3R5bGU9InRleHQtYWxpZ246bGVmdCI+PHNwYW4gc3R5bGU9ImJhY2tncm91bmQtY29sb3I6cmdiKDI0OCwgMjQ5LCAyNTApIj5QYXNzIHRoZSBVcGxvYWRBdXRoIFRva2VuPC9zcGFuPjwvZGl2PjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHBhdGggZD0iTSA0OC40OCAxNjEuNDYgTCAxOTcuNSAxNjIuNTYiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDU0LjM4IDE1OCBMIDQ3LjM2IDE2MS40NSBMIDU0LjMzIDE2NSIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6IHZpc2libGU7IHRleHQtYWxpZ246IGxlZnQ7IiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6IGZsZXg7IGFsaWduLWl0ZW1zOiB1bnNhZmUgZmxleC1lbmQ7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDFweDsgaGVpZ2h0OiAxcHg7IHBhZGRpbmctdG9wOiAxNTlweDsgbWFyZ2luLWxlZnQ6IDEyMnB4OyI+PGRpdiBzdHlsZT0iYm94LXNpemluZzogYm9yZGVyLWJveDsgZm9udC1zaXplOiAwOyB0ZXh0LWFsaWduOiBjZW50ZXI7ICI+PGRpdiBzdHlsZT0iZGlzcGxheTogaW5saW5lLWJsb2NrOyBmb250LXNpemU6IDEycHg7IGZvbnQtZmFtaWx5OiBIZWx2ZXRpY2E7IGNvbG9yOiAjMDAwMDAwOyBsaW5lLWhlaWdodDogMS4yOyBwb2ludGVyLWV2ZW50czogYWxsOyBiYWNrZ3JvdW5kLWNvbG9yOiAjZmZmZmZmOyB3aGl0ZS1zcGFjZTogbm93cmFwOyAiPjxmb250IHN0eWxlPSJmb250LXNpemU6MTJweCI+UmV0dXJuIHRoZSA8YnIgLz5VcGxvYWRBdXRoIFRva2VuPGJyIHN0eWxlPSJmb250LXNpemU6MTJweCIgLz48L2ZvbnQ+PC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48cmVjdCB4PSIyMTIiIHk9Ijk4IiB3aWR0aD0iMTIwIiBoZWlnaHQ9IjYwIiBmaWxsPSIjZmZmZmZmIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1kYXNoYXJyYXk9IjMgMyIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDExOHB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDEyOHB4OyBtYXJnaW4tbGVmdDogMjEzcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPkxvY2FsbHkgY2hlY2sgb3V0IHRoZSBVcGxvYWRBdXRoIFRva2VuPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48cGF0aCBkPSJNIDM2NC4xOCAyMDEgTCA1MDcuNjMgMjAxIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGQ9Ik0gNTEyLjg4IDIwMSBMIDUwNS44OCAyMDQuNSBMIDUwNy42MyAyMDEgTCA1MDUuODggMTk3LjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCB4PSIzNTkiIHk9IjE3NSIgd2lkdGg9IjE2MCIgaGVpZ2h0PSIyMCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogMTU4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMTg1cHg7IG1hcmdpbi1sZWZ0OiAzNjBweDsiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6IGJvcmRlci1ib3g7IGZvbnQtc2l6ZTogMDsgdGV4dC1hbGlnbjogY2VudGVyOyAiPjxkaXYgc3R5bGU9ImRpc3BsYXk6IGlubGluZS1ibG9jazsgZm9udC1zaXplOiAxMnB4OyBmb250LWZhbWlseTogSGVsdmV0aWNhOyBjb2xvcjogIzAwMDAwMDsgbGluZS1oZWlnaHQ6IDEuMjsgcG9pbnRlci1ldmVudHM6IGFsbDsgd2hpdGUtc3BhY2U6IG5vcm1hbDsgd29yZC13cmFwOiBub3JtYWw7ICI+T2J0YWluIHRoZSBzdG9yYWdlIHVybCBhbmQgY3JlZGVudGlhbHM8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PC9nPjxwYXRoIGQ9Ik0gNTE0IDI2MiBMIDM3MC4zNyAyNjIiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBzdHJva2UtZGFzaGFycmF5PSIzIDMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBkPSJNIDM2NS4xMiAyNjIgTCAzNzIuMTIgMjU4LjUgTCAzNzAuMzcgMjYyIEwgMzcyLjEyIDI2NS41IFoiIGZpbGw9IiMwMDAwMDAiIHN0cm9rZT0iIzAwMDAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHJlY3QgeD0iMzU5IiB5PSIyMzYiIHdpZHRoPSIxNjAiIGhlaWdodD0iMjAiIGZpbGw9Im5vbmUiIHN0cm9rZT0ibm9uZSIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzogdmlzaWJsZTsgdGV4dC1hbGlnbjogbGVmdDsiIHBvaW50ZXItZXZlbnRzPSJub25lIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIj48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTogZmxleDsgYWxpZ24taXRlbXM6IHVuc2FmZSBjZW50ZXI7IGp1c3RpZnktY29udGVudDogdW5zYWZlIGNlbnRlcjsgd2lkdGg6IDE1OHB4OyBoZWlnaHQ6IDFweDsgcGFkZGluZy10b3A6IDI0NnB4OyBtYXJnaW4tbGVmdDogMzYwcHg7Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOiBib3JkZXItYm94OyBmb250LXNpemU6IDA7IHRleHQtYWxpZ246IGNlbnRlcjsgIj48ZGl2IHN0eWxlPSJkaXNwbGF5OiBpbmxpbmUtYmxvY2s7IGZvbnQtc2l6ZTogMTJweDsgZm9udC1mYW1pbHk6IEhlbHZldGljYTsgY29sb3I6ICMwMDAwMDA7IGxpbmUtaGVpZ2h0OiAxLjI7IHBvaW50ZXItZXZlbnRzOiBhbGw7IHdoaXRlLXNwYWNlOiBub3JtYWw7IHdvcmQtd3JhcDogbm9ybWFsOyAiPlJldHVybiB0aGUgc3RvcmFnZSB1cmwgYW5kIGNyZWRlbnRpYWxzPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48cGF0aCBkPSJNIDM2NC4xOCAzMDggTCA1MDcuNjMgMzA4IiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGQ9Ik0gNTEyLjg4IDMwOCBMIDUwNS44OCAzMTEuNSBMIDUwNy42MyAzMDggTCA1MDUuODggMzA0LjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCB4PSIzNTkiIHk9IjI5MCIgd2lkdGg9IjE2MCIgaGVpZ2h0PSIyMCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogMTU4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMzAwcHg7IG1hcmdpbi1sZWZ0OiAzNjBweDsiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6IGJvcmRlci1ib3g7IGZvbnQtc2l6ZTogMDsgdGV4dC1hbGlnbjogY2VudGVyOyAiPjxkaXYgc3R5bGU9ImRpc3BsYXk6IGlubGluZS1ibG9jazsgZm9udC1zaXplOiAxMnB4OyBmb250LWZhbWlseTogSGVsdmV0aWNhOyBjb2xvcjogIzAwMDAwMDsgbGluZS1oZWlnaHQ6IDEuMjsgcG9pbnRlci1ldmVudHM6IGFsbDsgd2hpdGUtc3BhY2U6IG5vcm1hbDsgd29yZC13cmFwOiBub3JtYWw7ICI+VXBsb2FkIHRoZSBtZWRpYTwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48L2c+PHBhdGggZD0iTSA1MTQgMzY4IEwgMzcwLjM3IDM2OCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHN0cm9rZS1kYXNoYXJyYXk9IjMgMyIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGQ9Ik0gMzY1LjEyIDM2OCBMIDM3Mi4xMiAzNjQuNSBMIDM3MC4zNyAzNjggTCAzNzIuMTIgMzcxLjUgWiIgZmlsbD0iIzAwMDAwMCIgc3Ryb2tlPSIjMDAwMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCB4PSIzNTkiIHk9IjM0OCIgd2lkdGg9IjE2MCIgaGVpZ2h0PSIyMCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSJub25lIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OiB2aXNpYmxlOyB0ZXh0LWFsaWduOiBsZWZ0OyIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OiBmbGV4OyBhbGlnbi1pdGVtczogdW5zYWZlIGNlbnRlcjsganVzdGlmeS1jb250ZW50OiB1bnNhZmUgY2VudGVyOyB3aWR0aDogMTU4cHg7IGhlaWdodDogMXB4OyBwYWRkaW5nLXRvcDogMzU4cHg7IG1hcmdpbi1sZWZ0OiAzNjBweDsiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6IGJvcmRlci1ib3g7IGZvbnQtc2l6ZTogMDsgdGV4dC1hbGlnbjogY2VudGVyOyAiPjxkaXYgc3R5bGU9ImRpc3BsYXk6IGlubGluZS1ibG9jazsgZm9udC1zaXplOiAxMnB4OyBmb250LWZhbWlseTogSGVsdmV0aWNhOyBjb2xvcjogIzAwMDAwMDsgbGluZS1oZWlnaHQ6IDEuMjsgcG9pbnRlci1ldmVudHM6IGFsbDsgd2hpdGUtc3BhY2U6IG5vcm1hbDsgd29yZC13cmFwOiBub3JtYWw7ICI+UmV0dXJuIHRoZSB1cGxvYWQgcmVzdWx0PC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0PjwvZz48L2c+PC9zdmc+" width="565px" />

### Step 1: Initialize the SDK
We recommend that you initialize the SDK in the `onCreate` method of the `Application` class. See the following sample code:
```Java
Env.init(new Config.Builder() 
        .setApplicationContext(context) 
        .setAppID("your app id") 
        .setAppName("your app English name") 
        .setAppVersion(BuildConfig.VERSION_NAME) 
        .setAppChannel("channel name") 
        .build());
```

When initializing the SDK, set the following parameters:
| **Parameter** | **Type** | **Description** |
| --- | --- | --- |
| AppID | String | The app ID. You can get the app ID on the **SDK management** > **Applications** page in the [BytePlus VOD console](https://console.byteplus.com/vodpaas/). |
| AppName | String | The name of the app. You can get the app name on the **SDK management** > **Applications** page in the [BytePlus VOD console](https://console.byteplus.com/vodpaas/). |
| AppVersion | String | The version number of the app. A valid version number should contain two or more delimiters, such as "`1.3.2`". |
|  |  |  |
| AppChannel | String | The name of the channel where your app is distributed, such as Google Play. |
|  |  |  |

### Step 2: Create an uploader instance
Create an uploader instance with the `BDVideoUploader` class, as follows:
```Java
BDVideoUploader mUploader = new BDVideoUploader();
```

### Step 3: Specify the path of the file to be uploaded
Call `setPathName` to specify the absolute path of the file to be uploaded:
```Java
mUploader.setPathName("/data/user/0/xxx/files/test.mp4");
```

### Step 4: Set authentication parameters
Get the authentication parameters from your app server and pass them to the SDK:
```Java
mUploader.setTopAccessKey("xxx"); 
mUploader.setTopSecretKey("xxx"); 
mUploader.setTopSessionToken("xxx");
```

### Step 5: Specify the space to which the file will be uploaded
Call `setSpaceName` to set the space to which the file will be uploaded. To learn about space, see [Space management](https://docs.byteplus.com/en/byteplus-vod/docs/space-management).
```Java
mUploader.setSpaceName("xxx");
```

### Step 6: Specify the file path in cloud storage
After the upload is complete, the file path in cloud storage is as follows:
`StoreUri={{BucketName}}/{{FilePrefix}}{{FileTitle}}{{FileExtension}}`
| **Parameter** | **Description** |
| --- | --- |
| BucketName | The bucket name is automatically generated. |
| FilePrefix | The file prefix must end with `/`, such as `"path/to/foo/bar/"`. |
| FileTitle | The file name. If not set, the SDK will automatically generate a 32-bit string as the file name. |
| FileExtension | The file suffix must start with `.`, such as `".mp4"`. |
When setting the storage path, you can specify either the full path or just the file prefix and suffix.

* Call `setFileName` to set the full path. Please ensure that the full path must contain a suffix, such as `.mp4` or`.mp3`, as an error will occur otherwise.
   ```Java
   // The file path is tos-pathxxx/volc/test.mp4
   mUploader.setFileName("volc/test.mp4"); 
   // The file path is tos-pathxxx/test.mp4
   mUploader.setFileName("test.mp4");
   ```

* Call `setFilePrefix` to set the prefix and `setFileExtension` to set the suffix:
   ```Java
   // The file path is tos-pathxxx/volc/b8bb180086f945709dfda76bc9c6133d.mp4 
   mUploader.setFileExtension(".mp4");  
   mUploader.setFilePrefix("volc/");  
   // The file path is tos-pathxxx/b8bb180086f945709dfda76bc9c6133d.mp4 
   mUploader.setFileExtension(".mp4");
   ```


### Step 7: Control the upload process
The Upload SDK provides the following methods for controlling the upload process:

* Call `start` to start uploading:
   ```Java
   mUploader.start();
   ```

* Call `stop` to pause uploading:
   ```Java
   mUploader.stop();
   ```

* After the upload is complete, call `close` to terminate uploading. Otherwise, it will cause memory leaks.
   ```Java
   mUploader.close()
   ```


VOD Upload SDK also provides a wide range of advanced features. For further details, please refer to the following topics:

* [BytePlus VOD Upload SDK: Uploading audios and videos](https://docs.byteplus.com/en/byteplus-vod/docs/uploading-audios-and-videos)
* [BytePlus VOD Upload SDK: Uploading materials](https://docs.byteplus.com/en/byteplus-vod/docs/uploading-materials?version=v1.0)

