To create a high-performance, swipeable media feed in your iOS app, this guide provides optimization strategies for both VOD and live stream content. You will learn how to use the BytePlus VOD SDK to implement preloading and prerendering for short videos, and the BytePlus MediaLive SDK to pre-stream live content, ensuring a smooth playback experience with minimal buffering as users swipe.
# Development environment

* Xcode 14.0 or later.
* CocoaPods 1.10.0 or higher
* A physical mobile device running iOS 13.0 or later.

Emulators are not yet supported.

# Prerequisites

* A valid [BytePlus account](http://console.byteplus.com/) with [BytePlus VOD](https://console.byteplus.com/vodpaas) and [BytePlus MediaLive](https://console.byteplus.com/live) activated.
* Complete the following on the [SDK management](https://console.byteplus.com/vodpaas/sdk/) page in the [BytePlus VOD console](https://docs.byteplus.com/en/byteplus-vod/docs/sdk-management):
   * Create an app and get the app ID along with the English name of the app.
   * Register for the app, and purchase and download a license for using Player.
   * A Premium Edition SDK & License is required to support preloading and prerendering.
      * [Player SDK features](https://docs.byteplus.com/docs/byteplus-vod/docs-player-features)
      * [iOS Player SDK: Custom preloading](https://docs.byteplus.com/en/docs/byteplus-vod/docs-custom-preloading-ios)
* Completed the [basic setup for Live stream](https://docs.byteplus.com/en/byteplus-media-live/docs/getting-started).
* Obtained a BytePlus MediaLive SDK license.
   * For detailed instructions on how to acquire the license, see [Accessing your SDK license](https://docs.byteplus.com/en/docs/byteplus-media-live/docs-sdk-management#accessing-your-sdk-license).
   * To learn more about choosing the right license type for your needs, please refer to [SDK introduction](https://docs.byteplus.com/en/docs/byteplus-media-live/docs-introduction).

# Version requirements

* To access the **video playback** and **live stream** features, you must use BytePlus VOD and BytePlus MediaLive SDKs.

> For the corresponding versions of the SDKs used in each solution, please refer to the [Client SDK components](https://docs.byteplus.com/en/byteplus-vos/docs/version-combination?version=v1.0#a934220b).

# Integration
## Step 1: Modify the Podfile
Modify the Podfile in your project directory as shown below:
```Ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/byteplus-sdk/byteplus-specs.git'
source 'https://github.com/volcengine/volcengine-specs.git'

$RTC_SDK = 'TTSDK/RTCSDK'
target 'YourProjectTarget' do
  # BytePlus VOD SDK and MediaLive SDK dependencies
  pod 'TTSDK', '1.43.300.2-premium', :subspecs => ['LivePull-RTS', 'Player', 'RTCSDK']
end
```

## Step 2: Install the specified dependencies listed in the Podfile
To complete the SDK installation, run `pod install` in your terminal. Once the installation succeeds, you can open the generated `VideoOne.xcworkspace` file to configure your project.
```Bash
pod install
```

# Implementation
## Short video optimization strategy
### Step 1: Implement Video Playback
To integrate the BytePlus VOD player for video playback capabilities, you can refer to the document [Video playback](https://docs.byteplus.com/en/docs/byteplus-vos/docs-implementing-video-playback-edit-for-ios)
### Step 2: Enable the BytePlus VOD Player's preload and prerender strategies
Call [TTVideoEngine:enableEngineStrategy: scene: ](https://docs.byteplus.com/en/docs/byteplus-vod/docs-ios-player-sdk-api-details#enableenginestrategy-scene)to enable the preload and prerender strategies
```Objective-C
- (void)startVideoStrategy {
    [TTVideoEngine enableEngineStrategy:TTVideoEngineStrategyTypePreload scene:TTVEngineStrategySceneSmallVideo];
    [TTVideoEngine enableEngineStrategy:TTVideoEngineStrategyTypePreRender scene:TTVEngineStrategySceneSmallVideo];
    [TTVideoEngine setPreRenderVideoEngineDelegate:self];
}
```

### Step 3: Set up the preload source
Set the playlist as a preload source at the appropriate time, for example, after the network request for the video playlist returns.

1. When the playback source data is first loaded or when refreshing the page to display new data, call [TTVideoEngine:setStrategyVideoSources:](https://docs.byteplus.com/en/docs/byteplus-vod/docs-ios-player-sdk-api-details#setstrategyvideosources).
2. When loading more data, call [TTVideoEngine:addStrategyVideoSources:](https://docs.byteplus.com/en/docs/byteplus-vod/docs-ios-player-sdk-api-details#addstrategyvideosources).

> In this scenario, the VOD media and live stream media are mixed, so when setting the preload source, be sure to exclude the live stream media. Otherwise, it may lead to unexpected issues.

According to the playlist provided, BytePlus VOD SDK automatically manages the timing and configuration of preloads based on the current playback status, such as the number of concurrent preloads and the size of the data.
```Objective-C
// Set preload sources
- (void)configVideoEngineStrategyMediaSources:(NSArray<VEVideoModel *> *)videoModels refresh:(BOOL)refresh {
    NSMutableArray *sources = [NSMutableArray array];
    [videoModels enumerateObjectsUsingBlock:^(VEVideoModel * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        [sources addObject:[VEVideoModel videoEngineVidSource:obj]];
    }];
    if (refresh) { 
        /// Called when loading for the first time or refreshing the list
        [TTVideoEngine setStrategyVideoSources:sources];
    } else { 
        /// Called when loading more by scrolling up
        [TTVideoEngine addStrategyVideoSources:sources];
    }
}
```

### Step 4: Cover display and prerendering
After turning on prerendering, the SDK will create a player in advance according to the playlist you provided, and decode and render the next video in the playlist when playing the current video. By calling [TTVideoEngine:getPreRenderVideoEngineWithVideoSource: ](https://docs.byteplus.com/en/docs/byteplus-vod/docs-ios-player-sdk-api-details#getprerendervideoenginewithvideosource), you can determine whether the prerendering is complete.
In a sliding video feed scenario, when the next video is about to become visible, we will load its cover image. If the video has been prerendered at this time, we will no longer load the cover image and directly use the first frame of the video as the cover.
```Objective-C
// using the prerendered player.
TTVideoEngine *preRenderVideoEngine = [TTVideoEngine getPreRenderFinishedVideoEngineWithVideoSource:mediaSource];
// set prerendered player scaling mode
[preRenderVideoEngine setOptionForKey:VEKKeyViewScaleMode_ENUM value:@(TTVideoEngineScalingModeAspectFill)];
if (preRenderVideoEngine) {
    preRenderVideoEngine.playerView.hidden = NO;
    preRenderVideoEngine.playerView.backgroundColor = [UIColor clearColor];
    // hide image cover
    self.posterImageView.hidden = YES;
    // attach prerender view
    [self.view insertSubview:preRenderVideoEngine.playerView aboveSubview:self.posterImageView];
    [preRenderVideoEngine.playerView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(self.view);
    }];
    return;
}
// prerender not ready, using image cover
self.posterImageView.hidden = NO;
[self.posterImageView sd_setImageWithURL:[NSURL URLWithString:[self __getBackgroudImageUrl:mediaSource]] completed:nil];
```

### Step 5: Play the video
When playing the next video, if the prerendering is complete, use the prerendered player directly; otherwise, create a new one.
```Objective-C
TTVideoEngine *preRenderVideoEngine = [TTVideoEngine getPreRenderVideoEngineWithVideoSource:mediaSource];
if (preRenderVideoEngine) {
    self.videoEngine = nil;
    self.videoEngine = preRenderVideoEngine;
} else {
    self.videoEngine = [[TTVideoEngine alloc] initWithOwnPlayer:YES];;
}
```

## Live stream optimization strategy
### Step 1: Initialize the SDK
Initialize the SDK with the following code:
```Objective-C
#import <TTSDK/TTSDKManager.h>

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Your code.
    [self configTTSDK];
    // Your code.
    return YES;
}

- (void)configTTSDK {
    // Create TTSDKConfiguration with your App ID.
    TTSDKConfiguration *configuration = [TTSDKConfiguration defaultConfigurationWithAppID:@"Your App ID"];
    // Configure information about your SDK application.
    configuration.appName = @"Your application name";
    configuration.channel = @"Your app channel";
    configuration.bundleID = @"Your bundle ID";
    
    // Configure the URI of your license file.
    configuration.licenseFilePath = [NSBundle.mainBundle pathForResource:@"Your license path" ofType:nil];
    
    // Pass TTSDKConfiguration to TTSDKManager.
    [TTSDKManager startWithConfiguration:configuration];
}
```

### Step 2: Initialize the player
```Objective-C
- (void)setupPlayer {
    _playerManager = [[TVLManager alloc] initWithOwnPlayer:YES];
    if (!_playerManager) {
        return;
    }

    VeLivePlayerConfiguration *config = [[VeLivePlayerConfiguration alloc] init];
    config.enableStatisticsCallback = YES;
    [self.playerManager setConfig:config];
    
    [self.playerManager setPlayerViewRenderType:(TVLPlayerViewRenderTypeMetal)];
    [self.playerManager setObserver:self];
    [self.playerManager setProjectKey:[NSBundle.mainBundle.infoDictionary objectForKey:@"CFBundleName"]];
}
```

###  Step 3: Pre-stream
Mute the stream when the swipe begins, and unmute it after the swipe is complete.
```Objective-C
- (void)partiallyShow {
    [self.view  addSubview:self.playerContainer]
    [self.playerContainer mas_makeConstraints:^(MASConstraintMaker *make) {
        make.edges.equalTo(streamView);
    }];

    [self.playerManager setPlayUrl:url];
    [self.playerManager play];
    [self.player setMute:YES];
}
```

###  Step 4: Unmute
Unmute the stream when the current view is displayed
```Objective-C
- (void)completelyShow {
   [self.player setMute:NO];
}
```

###  Step 5: Player state reset
When the current video is swiped away, reset the playback state and stop the stream.
```Objective-C
- (void)endShow {
    [self.playerManager stop];
}
```


