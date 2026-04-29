To create a smooth, swipeable video feed in your Android app, this guide shows you how to implement both video-on-demand (VOD) and live stream playback. You will learn key optimization strategies, such as preloading and prerendering for VOD and pre-streaming for live content, to ensure a seamless user experience.
# Environmental requirements

* Android Studio Arctic Fox or higher. We recommend using the latest version of Android Studio for optimal performance.
* A physical mobile device that runs Android 5.0 or higher.
* Ensure the CPU architecture that you use is armv7 or arm64.

Emulators are not yet supported.

# Prerequisites

* A valid [BytePlus account](http://console.byteplus.com/) with [BytePlus VOD](https://console.byteplus.com/vodpaas) and [BytePlus MediaLive](https://console.byteplus.com/live) activated.
* Complete the following on the [SDK management](https://console.byteplus.com/vodpaas/sdk/) page in the [BytePlus VOD console](https://docs.byteplus.com/en/byteplus-vod/docs/sdk-management):
   * Create an app and get its app ID and name.
   * Register for the app, and purchase and download a license for using Player.
   * You need a Premium Edition SDK & License to support Preloading & Prerendering.
      * [Player SDK features](https://docs.byteplus.com/docs/byteplus-vod/docs-player-features)
      * [Android Player SDK: Custom preloading](https://docs.byteplus.com/docs/byteplus-vod/docs-custom-preloading-android)
* Complete the [basic setup for Live stream](https://docs.byteplus.com/en/byteplus-media-live/docs/getting-started).
* Obtain a BytePlus MediaLive SDK license.
   * For detailed instructions on how to acquire the license, see [Accessing your SDK license](https://docs.byteplus.com/en/docs/byteplus-media-live/docs-sdk-management#accessing-your-sdk-license).
   * To learn more about choosing the right license type for your needs, please refer to [SDK introduction](https://docs.byteplus.com/en/docs/byteplus-media-live/docs-introduction).

# Version requirements

* To access the **video playback** and L**ive stream** features, you must use BytePlus VOD and BytePlus MediaLive SDKs. Remember to choose the correct SDKs on the [Client SDK components](https://docs.byteplus.com/en/docs/byteplus-vos/docs-version-combination) page.

# Integrate SDKs
## Step 1: Add the maven repositories
Open your project's `build.gradle` file located in the project's root directory, and add the repository locations inside the `repositories` block, as shown below:
```Groovy
allprojects { 
    repositories { 
        google() 
        mavenCentral() 
        // BytePlus SDK maven repository
        maven { url 'https://artifact.byteplus.com/repository/public/' }
        maven { url 'https://artifact.bytedance.com/repository/Volcengine/' }
    } 
} 
 
// BytePlus VOD SDK maven repository
apply from: 'https://ve-vos.volccdn.com/script/vevos-repo-base.gradle'
```

## Step 2: Integrate the SDK
To integrate the SDK, add the following dependencies to your module's `build.gradle` file:
To get the latest version number, see [Client SDK components](https://docs.byteplus.com/en/docs/byteplus-vos/docs-version-combination)

```Groovy
dependencies {
    def ttsdkVersion = "1.43.300.3"
    // BytePlus VOD SDK dependencies
    implementation "com.bytedanceapi:ttsdk-player_premium:$ttsdkVersion"
    
    // BytePlus MediaLive SDK dependencies
    implementation "com.bytedanceapi:ttsdk-ttlivepull_rtc:$ttsdkVersion"
    implementation 'commons-net:commons-net:3.9.0'
    
    implementation 'com.bytedance.applog:RangersAppLog-Lite-global:6.14.3'
}
```

## Step 3: Declare app permissions
Declare the permissions required by SDKs in `AndroidManifest.xml`.
```XML
<uses-permission android:name="android.permission.INTERNET" /> 
```


# Implementation
## Short video optimization strategy
### Step 1: Implement Video Playback
To learn how to use the BytePlus VOD player for video playback, see [Implementing video playback for Android](https://docs.byteplus.com/en/docs/byteplus-vos/docs-implementing-video-playback-edit-for-android)
### Step 2: Enable the BytePlus VOD Player's preload and prerender strategies
Call [TTVideoEngine.enableEngineStrategy(int, int)](https://docs.byteplus.com/en/docs/byteplus-vod/docs-android-player-sdk-api-details#enableenginestrategy) to enable preload and prerender strategies
```Java
public static void setSceneStrategyEnabled(){
        TTVideoEngine.enableEngineStrategy(
                StrategyManager.STRATEGY_TYPE_PRELOAD,
                StrategyManager.STRATEGY_SCENE_SMALL_VIDEO
        );
        TTVideoEngine.enableEngineStrategy(
                StrategyManager.STRATEGY_TYPE_PRE_RENDER,
                StrategyManager.STRATEGY_SCENE_SMALL_VIDEO
        );
}
```

### Step 3: Set up the preload source
Set the playlist as a preloaded source at the right moment, such as after the video playlist is retrieved.

1. When the playback source data is first loaded or when refreshing the page with new data, call [TTVideoEngine.setStrategySources(List](https://docs.byteplus.com/en/docs/byteplus-vod/docs-android-player-sdk-api-details#TTVideoEngine-setstrategysources).
2. In a scenario of loading more data, call [TTVideoEngine.addStrategySources(List<StrategySource>)](https://docs.byteplus.com/en/docs/byteplus-vod/docs-android-player-sdk-api-details#TTVideoEngine-addStrategySources).

> In this scenario, the playlist contains both VOD and live stream media, so when setting the preload source, be sure to exclude the live stream media. Otherwise, it may lead to unexpected issues.

According to the playlist provided, BytePlus VOD SDK automatically manages the timing and configuration of preloads based on the current playback status, such as the number of concurrent preloads and the size of the data.
```Java
// Set preload sources
if (page == 0) {
    TTVideoEngine.setStrategySources(items.filterMediaSource());
    // ... more codes here
} else { // append items
    TTVideoEngine.addStrategySources(items.filterMediaSource());
    // ... more codes here
}
```

### Step 4: Cover display and prerendering
After enabling prerendering, the SDK will create a player in advance according to the playlist you provided, and decode and render the next video in the playlist when playing the current video.
In a scrolling video feed scenario, when the next video is about to be visible, we will load the cover image of the video. If the video has been prerendered at this time, we will no longer load the cover image and directly use the first frame of the video as the cover.
```Java
// using the prerendered player.
final TTVideoEngine player = TTVideoEngine.getPreRenderEngine(mediaSource.getMediaId());

if (player != null && player != StrategyManager.instance().getPlayEngine()) {
    // call forceDraw to prerender
    player.setSurface(surface);
    player.forceDraw();
} else {
    // prerender not ready, using image cover
    showVideoCover();
}
```

### Step 5: Play the video
When playing the next video, if the prerendering is complete, use the prerendered player if it's available. Otherwise, create a new player.
```Java
TTVideoEngine player = TTVideoEngine.getPreRenderEngine(mediaSource.getMediaId());
if (player == null) {
    player = create(mContext, mediaSource);
}
```

## Live stream optimization strategy
### Step 1: Initializing the SDK
Initialize the SDK with the following code:
```Java
// Initialize the environment.
   Env.init(new Config.Builder()
   .setApplicationContext(sApplicationContext)
   .setAppID("Your App ID")
   .setAppName("Your application name")
   .setAppVersion(BuildConfig.VERSION_NAME) // A valid version number should contain two or more separators, such as "1.3.2".
   .setAppChannel("Your app channel")
   .setLicenseUri("Your license URI")
   .setLicenseCallback(mLicenseCallback) // Set the license callbacks.
   .build());

// Enable logcat. It is recommended to enable logcat during development and disable logcat when compiling the release APK.
// LicenseManager.turnOnLogcat(true);

// License callbacks.
LicenseManager.Callback mLicenseCallback =new LicenseManager.Callback() {
        @Override
        public void onLicenseLoadSuccess(@NonNull String licenseUri, @NonNull String licenseId) {
            licenseID = licenseId; // License ID, through which you can get the license information.
        }

        @Override
        public void onLicenseLoadError(@NonNull String licenseUri, @NonNull Exception e, boolean retryAble) {
           
        }

        @Override
        public void onLicenseLoadRetry(@NonNull String licenseUri) {
           
        }

        @Override
        public void onLicenseUpdateSuccess(@NonNull String licenseUri, @NonNull String licenseId) {
          licenseID = licenseId;
        }

        @Override
        public void onLicenseUpdateError(@NonNull String licenseUri, @NonNull Exception e, boolean retryAble) {
           
        }

        @Override
        public void onLicenseUpdateRetry(@NonNull String licenseUri) {
          
        }
    };
    
// Get the license information.
License license = LicenseManager.getInstance().getLicense(licenseID); // You can get the license ID through mLicenseCallback.
if (license != null) {
    StringBuilder builder = new StringBuilder();
    builder.append("License id:" + license.getId()).append("\n")
           .append("License package:" + license.getPackageName()).append("\n")
           .append("License test:" + license.getType()).append("\n")
           .append("License version:" + license.getVersion()).append("\n");

    if (license.getModules() != null) {
        String names = "";
        for (License.Module module : license.getModules()) {
            names = "module name:" + module.getName() + ", start time:" +
            TimeUtil.format(module.getStartTime(), Times.YYYY_MM_DD_KK_MM_SS)
                    + ", expire time:" + TimeUtil.format(module.getExpireTime(), Times.YYYY_MM_DD_KK_MM_SS) + "\n";
            builder.append("License modules:" + names);
        }
    }
 }
```

### Step 2: Initialize the player
```Java
// Create a player
VeLivePlayer player = new VideoLiveManager(Env.getApplicationContext());

// Set the player observer
player.setObserver(mLivePlayerObserver)；

// Configure the player
VeLivePlayerConfiguration config = new VeLivePlayerConfiguration();
config.enableStatisticsCallback = true;
config.enableLiveDNS = true;
player.setConfig(config);
```

###  Step 3: Pre-stream
Mute the stream when the user begins to swipe, and unmute it after the swipe is complete.
```Java
@Override
public void onPositionVisibilityChanged(int oldPosition, int newPosition) {
    super.onPositionVisibilityChanged(oldPosition, newPosition);

    LiveFeedHolder holder = getHolder(newPosition);
    LiveFeedItem item = (LiveFeedItem) getItem(newPosition);

    VideoLiveManager player = holder.getPlayer();
    if (player == null) {
        player = createLivePlayer(context, item.getRoomId());
        holder.bindPlayer(player);
    }

    player.setPlayUrl(item.getLiveUrl());
    player.setMute(true);
    player.play();
}
```

###  Step 4: Unmute
Unmute the stream when the current view is displayed.
```Java
@Override
public void onPageSelected(int position) {
    super.onPageSelected(position);

    LiveFeedHolder holder = getHolder(position);
    LiveFeedItem item = (LiveFeedItem) getItem(position);

    VideoLiveManager player = holder.getPlayer();
    if (player == null) {
        player = createLivePlayer(context, item.getRoomId());
        holder.bindPlayer(player);
    }

    player.setPlayUrl(item.getLiveUrl());
    player.setMute(false);
    player.play();
}
```

### Step 5: Reset the player state
When the current video is swiped out of view, reset the playback state and stop the stream.
```Java
@Override
public void onPageSelected(int position) {
    super.onPageSelected(position);
    
    if (lastHolder != null) {
        VideoLiveManager player = lastHolder.getPlayer();
        if (player != null) {
            player.stop();
        }
    }

    // more codes here
}
```


