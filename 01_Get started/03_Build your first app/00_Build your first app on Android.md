To build your first video application on Android, follow this step-by-step guide to run the VideoOne demo project. Upon successful compilation, you will have an Android app featuring interactive live streaming and video playback.
This demo project does not include all features available in the demo app, such as beauty AR. Refer to the solution implementation guides for instructions on how to implement these features. If you need further assistance, please contact your [technical support](https://console.byteplus.com/workorder/create).

## Prerequisites
Complete the following tasks before you start. We recommend that you keep a centralized record of the information gained after completing each task for easy reference during project setup.
Some tasks are specific to certain features and can be skipped if you do not need those features. For example, if you want to use video playback only, you do not need to activate BytePlus RTC and BytePlus MediaLive services.
| **Task** | **Relevant feature** | **Task instruction** | **Information to record** |
| --- | --- | --- | --- |
| 1. Register a BytePlus account. <br> 2. Create an access key for the account. | All features | * [Signing up for a BytePlus account](https://docs.byteplus.com/byteplus-platform/docs/signing-up-your-account) <br> * [Creating an access key](https://docs.byteplus.com/byteplus-platform/docs/creating-an-accesskey) | * Access Key ID (AK) <br> * Secret Access Key (SK) |
| 1. Activate the BytePlus RTC service. <br> 2. Create an RTC app. | Interactive live streaming | * [Before Using RTC Service](https://docs.byteplus.com/byteplus-rtc/docs/69865) | * AppId <br> * AppKey |
| 1. Activate the BytePlus MediaLive service. <br> 2. Create a MediaLive app and obtain a license. <br>    If you have already created a BytePlus VOD app, bind the existing app instead of creating a new one. <br>  | Interactive live streaming | * [Activating the MediaLive service](https://docs.byteplus.com/docs/byteplus-media-live/docs-getting-started) <br> * SDK management <br>    * [Creating an SDK application](https://docs.byteplus.com/docs/byteplus-media-live/docs-sdk-management#creating-an-sdk-application) <br>    * [Accessing your SDK license](https://docs.byteplus.com/docs/byteplus-media-live/docs-sdk-management#accessing-your-sdk-license) | * App ID <br> * BytePlus MediaLive SDK license file |
| 1. Activate the BytePlus VOD service. <br> 2. Create a VOD app and obtain a license. <br>    If you have already created a BytePlus MediaLive app, bind the existing app instead of creating a new one. <br>  | Video playback | * [Step 1: Enable the BytePlus VOD service](https://docs.byteplus.com/docs/byteplus-vod/docs-getting-started#step-1-enable-the-byteplus-vod-service) <br> * [Application management](https://docs.byteplus.com/byteplus-vod/docs/sdk-management) <br> * [License management](https://docs.byteplus.com/docs/byteplus-vod/docs-license-management) | * App ID <br> * BytePlus VOD SDK license file |
## System requirements

* Android Studio Arctic Fox or higher. We recommend using the latest version of Android Studio for optimal performance.
* A physical mobile device that runs Android 5.0 or higher.
* Ensure the CPU architecture that you use is armv7 or arm64.

## Procedures
Some tasks are specific to certain features and can be skipped if you do not need to experience those particular features. For example, if you want to experience interactive live streaming only, you do not need to complete the setup for video playback.
### Cloning the project
Follow the steps below to clone the demo project:

1. Go to GitHub and clone the [VideoOneSolutions](https://github.com/byteplus-sdk/VideoOneSolutions) repository.
2. Under the `Client` directory, open the `Android` folder in Android Studio. This is the root directory of the demo project.

### Setting the app server
Under the root directory, open the `gradle.properties` file, and specify the address of your app server:

* To use the app server provided by BytePlus, set the `SERVER_URL` field to `https://videocloud.byteplusapi.com/videoone_opensource`.
   This address is for testing purposes only, and cannot be used in a production environment.

* To deploy your own app server, follow the instructions in [Deploying the app server](https://arcosite.bytedance.net/sites/1710905012654/65fa57210f7bd5030dba458a/editor/66b30efadc2c7802fc594211?docLang=en&bv=66b30ef9dc2c7802fc5941f9), and then set the `SERVER_URL` field to `http://{your_server_address}:{port_number}/videoone_opensource`.

### Setting the package name
Under the `app` directory, open the `build.gradle` file and set `applicationId` to the package name of your Android app. If you have applied for the BytePlus MediaLive or VOD SDK license, the package name must be identical to the name you used during the license application.
### Setting up for RTC
This task is required if you want to experience interactive live streaming.

Under the root directory, open the `gradle.properties` file, and configure the following fields with the values you obtained in the [Prerequisites](#prerequisites) section. The information is used for authentication when the client communicates with the BytePlus RTC service.
| **Field name** | **Description** | **Example** |
| --- | --- | --- |
| APP_ID | The **AppId** of your BytePlus RTC app. | 123456********3deb567a86 |
| APP_KEY | The **AppKey** of your BytePlus RTC app. | 1bfaa8e********fjb6j6hhde68c07d |
| ACCESS_KEY_ID | The **Access Key ID (AK)** of your BytePlus account. | AKAPZ7********FLB0D38CM8DK49SO39D83KD820DFK4k9 |
| SECRET_ACCESS_KEY | The **Secret Access Key (SK)** of your BytePlus account. | 8dk39vK********k7D93KDHS8DJSLud830DJEO37EI3UDK37WODKu3oQ |
### Setting up for MediaLive
#### Preparing domain names
This task is required if you want to experience the interactive live streaming scene.

| **Subtask** | **Task instruction** | **Required information** |
| --- | --- | --- |
| **Add domain names.** <br> You should add at least two domain names, one for stream pushing, and the other for stream pulling. | [Adding domain names](https://docs.byteplus.com/byteplus-media-live/docs/domain) | * domain name for stream pushing <br> * domain name for stream pulling |
| **Add a CNAME record for your domain name.** <br> After you add a domain name, BytePlus MediaLive automatically assigns it a corresponding MediaLive domain. You must then add a CNAME record to point your domain to the assigned MediaLive domain. | [Adding a CNAME record](https://docs.byteplus.com/byteplus-media-live/docs/adding-a-cname-record) | / |
| **Enable URL authentication.** <br> Enable URL authentication to secure your streams and prevent unauthorized domain use. | [URL authentication](https://docs.byteplus.com/en/byteplus-media-live/docs/url-authentication?version=v1.0) | * Primary key |
| **Configure a transcoding template.** <br> You can transcode the original live stream to meet the viewing requirements of different users. | [Configuring a transcoding task](https://docs.byteplus.com/en/byteplus-media-live/docs/configuring-transcoding) | * AppName |
#### Setting stream addresses
This task is required if you want to experience the interactive live streaming scene.

Under the `solutions/interactivelive` directory of the demo project, open the `gradle.properties` file and fill in the corresponding fields with the information obtained in the previous step. This information is used to construct the push and pull stream addresses.
| **Field name** | **Description** | **Example** |
| --- | --- | --- |
| LIVE_PULL_DOMAIN | http://{domain_name}, where "domain_name" represents the **domain name for stream pulling**. | `http://pull-demo.com` |
| LIVE_PUSH_DOMAIN | rtmp://{domain_name}, where "domain_name" represents the **domain name for stream pushing**. | `rtmp://push-demo.com` |
| LIVE_PUSH_KEY | The **Primary key** for URL authentication. | `XED45d5dDLSH********KDFD` |
| LIVE_APP_NAME | The **AppName** for which you have configured a transcoding template. | `videoone_demo` |
#### Integrating the license
This task is required if you want to experience either interactive live streaming scene or live streaming-related functions.

Follow the steps below to integrate the **BytePlus MediaLive SDK license file** you obtained in the [Prerequisites](#prerequisites) section:

1. Rename the license file to `ttsdk.lic`.
2. Move the license file to the target directory:
   1. If you want to experience the interactive live streaming scene, move the license file to `solutions/interactivelive/src/main/assets`.
   2. If you want to experience live streaming-related functions, move the license file to `solutions/media-live/live-entrance/src/main/assets`.
3. Open the `gradle.properties` file and set `LIVE_TTSDK_APP_ID` to the **App ID** of the MediaLive app you created in the [Prerequisites](#prerequisites) section.
4. Refer to [Generating stream addresses](https://docs.byteplus.com/en/docs/byteplus-media-live/docs-address-generator) to prepare a streaming URL for pushing the live stream and a streaming URL for pulling the live stream.

### Setting up for VOD
This task is required if you want to experience the video playback scene and related functions.

Integrate the BytePlus VOD SDK license you obtained in the [Prerequisites](#prerequisites) section:

1. Move the license file to the `solutions/vod/vod-common/src/main/assets/vodlicense` directory.
2. Under the `solutions/vod/vod-common/`directory, open the `gradle.properties` file and configure the following fields:

| **Field name** | **Description** | **Example** |
| --- | --- | --- |
| VOD_APP_ID | The **App ID** of the VOD app you created in the [Prerequisites](#prerequisites) section. | 5****1 |
| VOD_APP_CHANNEL | The distribution channel of your app. | googleplay |
| VOD_LICENSE_URI | The storage path of the **BytePlus VOD SDK license file** you obtained in the [Prerequisites](#prerequisites) section. | assets:///vodlicense/{LICENSE_NAME}.lic |
### Compiling the project

1. Connect the mobile device to your computer, and set it as the target device in Android Studio.
2. Click the **Sync Project with Gradle Files** button to sync the project with the Gradle files.
3. Click the **Run** button to initiate the compilation process.

### Running the app
If the compilation process completes without any errors, the VideoOne app will be installed on the connected mobile device and you will be directed to the login screen automatically. Log in to the app with your email to start exploring its features and functions.
![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/33f9ae7de2ca4e41a5c73e26ae22d630~tplv-goo7wpa0wc-image.image)
## Understanding the code
The directory tree of the demo project is as follows:
```Plain
. 
├── app                    # Entry point of the app 
├── build.gradle 
├── component              # Public components 
│   ├── Avatars                # Avatar resources 
│   ├── LoginKit               # Login 
│   └── SolutionBase           # Base library 
│   └── ToolKit                # Common tools 
├── gradle 
├── gradle.properties           # Common configurations 
├── gradlew 
├── gradlew.bat 
├── local.properties 
├── settings.gradle 
└── solutions                 # Solutions 
    ├── VideoPlaybackEdit     # The video playback and editing demo 
    │   └── Vod
    ├── interactivelive       # The interactive live streaming demo 
    │   ├── build.gradle
    │   ├── consumer-rules.pro
    │   ├── gradle.properties
    │   ├── proguard-rules.pro
    │   └── src
    ├── media-live            # MediaLive SDK configurations 
    │   ├── live-common       # Common tools  
    │   ├── live-entrance     # Entrance
    │   ├── live-player       # Live stream pulling
    │   ├── live-pusher       # Live stream pushing
    └── rtc-api-example       # RTC API Example 
         └── rtc-api-example-entry    # Entrance
```

