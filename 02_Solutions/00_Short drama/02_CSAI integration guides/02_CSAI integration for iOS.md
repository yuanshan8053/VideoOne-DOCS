This guide describes how to integrate the BytePlus MediaAds SDK and Google IMA SDK into your VideoOne-based Short Drama application on iOS.
## Prerequisites
Before you begin, ensure you have the following:

1. **Existing integration**: You have already integrated the [VideoOne Short Drama Solution](https://docs.byteplus.com/en/docs/byteplus-vos/short-drama-videos-ios-integration).
2. **Ad Tag URL**: You need a valid VAST/VMAP Ad Tag URL.
   * For testing: You can use [Google's Sample VAST Tags](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/tags).
   * For production: Obtain your Ad Tag URL from your ad server (e.g., Google Ad Manager).
3. **Minimum SDK requirements**:
   * `iOS Deployment Target`: 14.0 or higher.

## Integrating the SDK
Follow these steps to integrate the MediaAds SDK into your iOS project.

1. **Source the BytePlus CocoaPods specs repo**: Add the BytePlus specs repository to your `Podfile` to access the SDK.
   ```Ruby
   # Source BytePlus's CocoaPods specs repo in the Podfile
   source 'https://github.com/byteplus-sdk/byteplus-specs.git'
   ```

2. **Set iOS minimum version**: Ensure your project targets iOS 14.0 or later, which is the minimum version required by the MediaAds SDK.
   ```Ruby
   # Minimum required version is at least 14.0
   platform :ios, '14.0'
   ```

3. **Add MediaAds SDK dependencies**: Define a new pod set for the Short Drama with In-Stream Ads scene and include it in your target.
   ```Ruby
   # Add new Mini Drama with CSAI scene
   def scene_MiniDramaWithInStreamAds_pods
     pod 'TTSDKFramework', $TT_SDK_VERSION, :subspecs => ['Player']
     pod 'MediaAdsToB', '2.0.1'
     pod 'MiniDramaWithInStreamAds', :path => './Component/MiniDramaWithInStreamAds'
   end
   
   target 'VideoOne' do
     common_pods
     scene_InteractiveLive_pods
     # ... other scenes
     
     scene_MiniDrama_pods
     # Include Mini Drama with CSAI scene in list of targets to build
     scene_MiniDramaWithInStreamAds_pods
     scene_AIChat_pods
   end
   ```


## Quick start
### General guide
The MediaAds SDK is designed to be player agnostic. To adapt to arbitrary players, an entity called `AdPlaybackSession` is provided to wrap both the content player and the ad player, and coordinate the placement of ads throughout the main content.
Follow these steps to create an `AdSchedule` and construct an `AdPlaybackSession`.

1. Create `MediaAdsInitConfig`: Initialize the configuration object with your app ID, name, and channel.
   ```Objective-C
   MediaAdsInitConfig *config = [[MediaAdsInitConfig alloc] init];
   config.appID = VODAPPID;
   config.appName = [NSBundle.mainBundle.infoDictionary objectForKey:@"CFBundleName"];
   config.appChannel = [NSBundle.mainBundle.infoDictionary objectForKey:@"CHANNEL_NAME"];
   ```

2. Create `AdSchedule`: Define the ad breaks for your content. In this example, we configure pre-roll, mid-roll, and post-roll ads using VAST tag URLs.
   ```Objective-C
   AdSchedule *adSchedule = [[AdSchedule alloc] init];
   
   // Add pre-roll ad with VAST tags (offset: "pre")
   [adSchedule addLinearAdWithOffset:@"pre" adTagUrl:@"https://pubads.g.doubleclick.net/gampad/ads?iu=/21775744923/external/single_preroll_skippable&sz=640x480&ciu_szs=300x250%2C728x90&gdfp_req=1&output=vast&unviewed_position_start=1&env=vp&correlator="];
   
   // Add mid-roll ad with VAST tags (offset: "00:00:15")
   [adSchedule addLinearAdWithOffset:@"00:00:15" adTagUrl:@"https://pubads.g.doubleclick.net/gampad/ads?slotname=/21775744923/external/vmap_skip_ad_samples&sz=640x480&ciu_szs=300x250&cust_params=sample_ar%3Dmidskiponly&url=&unviewed_position_start=1&output=xml_vast3&impl=s&env=vp&gdfp_req=1&ad_rule=0&vad_type=linear&vpos=midroll&pod=1&ppos=1&min_ad_duration=0&max_ad_duration=30000&vrid=1445984&cmsid=496&video_doc_id=short_onecue&kfa=0&tfcd=0"];
   
   // Add post-roll ad with VAST tags (offset: "post")
   [adSchedule addLinearAdWithOffset:@"post" adTagUrl:@"https://pubads.g.doubleclick.net/gampad/ads?slotname=/21775744923/external/vmap_ad_samples&sz=640x480&ciu_szs=300x250&cust_params=sample_ar%3Dpremidpost&url=&unviewed_position_start=1&output=xml_vast3&impl=s&env=vp&gdfp_req=1&ad_rule=0&vad_type=linear&vpos=postroll&pod=3&ppos=1&lip=true&min_ad_duration=0&max_ad_duration=30000&vrid=1264775&cmsid=496&video_doc_id=short_onecue&kfa=0&tfcd=0"];
   ```

3. Create `AdPlaybackSession`: Instantiate the session, bind it to your view and view controller, and start it.
   ```Objective-C
   // Instantiate AdPlaybackSession with config
   AdPlaybackSession *adPlaybackSession = [[MediaAds sharedInstance] createAdSession:player Config:config];
   // Set ad UIView and UIViewController to handle rendering and navigation
   [adPlaybackSession setAdView:adContainerView AdViewController:viewController];
   // (optional, recommended) set AdPlaybackSessionDelegate to listen for events
   adPlaybackSession.delegate = adPlaybackSessionDelegate;
   // Set AdSchedule
   [adPlaybackSession setSchedule:adSchedule];
   // Start ad session
   [adPlaybackSession startSession];
   ```


### Integrating with VideoOne
#### Implement `MDVideoWithAdsPlayerController`
The `MDVideoWithAdsPlayerController` class inherits from `MDVideoPlayerController`. It overrides playback methods to delegate control to the `MediaAds` session instead of directly manipulating the video player.
Key responsibilities of this class:

1. **Initialize Ad session**: Creates the `AdPlaybackSession` and binds the ad container view.
2. **Intercept playback commands**: Overrides `play`, `pause`, and `stop` to route these commands through `adPlaybackSession`.
3. **Proxy delegate callbacks**: Uses `VideoEngineDelegateProxy` to ensure both the ad session and the app receive player events.
4. **Schedule Ads dynamically**: Configures the `AdSchedule` in the `videoEnginePrepared` callback when video duration is known.

```Objective-C
@interface MDVideoWithAdsPlayerController () <
TTVideoEngineDelegate
// optionally adopt the AdPlaybackSessionDelegate protocol 
AdPlaybackSessionDelegate
/* other delegate protocols */>

@property (nonatomic, strong) TTVideoEngine *videoEngine;
@property (nonatomic, strong) AdSchedule *adSchedule;
@property (nonatomic, strong) AdPlaybackSession *adPlaybackSession;
@property (nonatomic, strong) VideoEngineDelegateProxy *delegateProxy;
@property (nonatomic, strong) UIView *adContainerView;

// other properties ...

@end

@implementation MDVideoWithAdsPlayerController

// synthesizes and dynamic properties ...

// create ad playback session
- (AdPlaybackSession *)createAdPlaybackSession {
    MediaAdsInitConfig *config = [[MediaAdsInitConfig alloc] init];
    // set config ...

    AdPlaybackSession *session = [[MediaAds sharedInstance] createAdSession:self.videoEngine Config:config];
 
    // set up ad UIView constraints and insert into hierarchy
    [self __setupAdContainerView];
    [self.view addSubview:_adContainerView];

    // set ad UIView and its UIView controller
    [session setAdView:_adContainerView AdViewController:self];

    return session;
}

- (void)createVideoEngine:(id<TTVideoEngineMediaSource> _Nonnull)mediaSource needPrerenderEngine:(BOOL)needPrerender {
    if (super.videoEngine == nil) {
        // create video engine ...
    }

    // video engine configuration ...

    // AdPlaybackSession can be created after the video player
    if (self.adPlaybackSession == nil) {
        self.adPlaybackSession = [self createAdPlaybackSession];

        // CRITICAL: Delegate Proxying
        // MediaAds sets its own player delegate. To also receive callbacks in this controller,
        // we use a proxy that forwards events to both the MediaAds delegate and self.
        id<TTVideoEngineDelegate> mediaAdsDelegate = self.videoEngine.delegate;
        VideoEngineDelegateProxy *proxy = [[VideoEngineDelegateProxy alloc] initWithMediaAdsDelegate:mediaAdsDelegate adsPlayerDelegate:self];
        self.videoEngine.delegate = proxy;
        self.delegateProxy = proxy;
    }
}

// replace calls to [player <playback method>] with [AdPlaybackSession <method>]
- (void)prepareToPlay {
    BTDAssertMainThread();
    MDPlayerContextRunOnMainThread(^{
        // [self.videoEngine prepareToPlay];
        [self.adPlaybackSession prepareAD];
    });
}

- (void)play {
    BTDAssertMainThread();
    MDPlayerContextRunOnMainThread(^{
        // Retain existing anti-recording logic
        if ([DramaDisrecordManager isOpenDisrecord] && @available(iOS 11.0, *)) {
            if ([[[[UIApplication sharedApplication] keyWindow] screen] isCaptured]) {
                [[DramaDisrecordManager sharedInstance] showDisRecordView];
                return;
            }
        }
        [self __handleBeforePlayAction];
        
        // Resume session instead of directly playing video engine
        if (self.adPlaybackSessionStarted == YES) {
            [self.adPlaybackSession resumeSession];
        } else {
            [self.videoEngine play];
        }
        [self _handleAfterPlayAction];
        [self __addPeriodicTimeObserver];
        if (self.playerConfig.enableLoadSpeed) {
            [TTVideoEngine ls_setPreloadDelegate:self];
        }
    });
}

- (void)pause {
    BTDAssertMainThread();
    MDPlayerContextRunOnMainThread(^{
        [self.context post:@(YES) forKey:MDPlayerContextKeyPauseAction];
        // [self.videoEngine pause];
        [self.adPlaybackSession pauseSession];
        });
}

- (void)stop {
    [self unregisterScreenCaptureDidChangeNotification];
    BTDAssertMainThread();
    MDPlayerContextRunOnMainThread(^{
        [self.context post:@(YES) forKey:MDPlayerContextKeyStopAction];
        // [self.videoEngine stop];
        [self.adPlaybackSession stopSession];
        [self __closeVideoPlayer];
    });
}

#pragma mark - TTVideoEngineDelegate
- (void)videoEnginePrepared:(TTVideoEngine *)videoEngine {
    // Video duration is available here. Configure schedule now.
    _adSchedule = [[AdSchedule alloc] init];
    
    NSDictionary *settings = [[MDAdSettingManager universalManager] settings];
    // check settings for which ads to schedule ...

    if (preRollAdEnabled) {
        [_adSchedule addLinearAdWithOffset:@"pre" adTagUrl:@"https://pubads.g.doubleclick.net/gampad/ads?iu=/21775744923/external/single_preroll_skippable&sz=640x480&ciu_szs=300x250%2C728x90&gdfp_req=1&output=vast&unviewed_position_start=1&env=vp&correlator="];
    }

    if (midRollAdEnabled) {
        // Calculate mid-roll timestamp based on actual duration
        NSString *midRollOffset = [self stringFromTimeInterval:[self duration] / 2];
        [_adSchedule addLinearAdWithOffset:midRollOffset adTagUrl:@"https://pubads.g.doubleclick.net/gampad/ads?slotname=/21775744923/external/vmap_skip_ad_samples&sz=640x480&ciu_szs=300x250&cust_params=sample_ar%3Dmidskiponly&url=&unviewed_position_start=1&output=xml_vast3&impl=s&env=vp&gdfp_req=1&ad_rule=0&vad_type=linear&vpos=midroll&pod=1&ppos=1&min_ad_duration=0&max_ad_duration=30000&vrid=1445984&cmsid=496&video_doc_id=short_onecue&kfa=0&tfcd=0"];
    }

    if (postRollAdEnabled) {
        [_adSchedule addLinearAdWithOffset:@"post" adTagUrl:@"https://pubads.g.doubleclick.net/gampad/ads?slotname=/21775744923/external/vmap_ad_samples&sz=640x480&ciu_szs=300x250&cust_params=sample_ar%3Dpremidpost&url=&unviewed_position_start=1&output=xml_vast3&impl=s&env=vp&gdfp_req=1&ad_rule=0&vad_type=linear&vpos=postroll&pod=3&ppos=1&lip=true&min_ad_duration=0&max_ad_duration=30000&vrid=1264775&cmsid=496&video_doc_id=short_onecue&kfa=0&tfcd=0"];
    }
    
    [_adPlaybackSession setSchedule:_adSchedule];
    
    // Start session. Note: The schedule CANNOT be changed after this point.
    [self.adPlaybackSession startSession];
    self.adPlaybackSessionStarted = YES;

    // notify other delegates ...
}

// implement any relevant AdPlaybackSessionDelegate
#pragma mark - AdPlaybackSessionDelegate

- (void)sessionContentPause {
    // handle session pause ...
}

- (void)sessionContentResume {
    // handle session resume ...
}

@end
```

#### Implement `VideoEngineDelegateProxy`
If a `TTVideoEngine` player is used, MediaAds sets its own player delegate to receive callbacks. To prevent overwriting this delegate while still allowing your controller to receive events, you must implement a proxy.
This proxy holds weak references to both the `mediaAdsDelegate` and your own `adsPlayerDelegate`, forwarding every callback to both if they respond to the selector.
```Objective-C
@interface VideoEngineDelegateProxy : NSObject <TTVideoEngineDelegate>

@property (nonatomic, weak) id<TTVideoEngineDelegate> mediaAdsDelegate;
@property (nonatomic, weak) id<TTVideoEngineDelegate> adsPlayerDelegate;

- (instancetype)initWithMediaAdsDelegate:(id<TTVideoEngineDelegate>)mediaAdsDelegate
                       adsPlayerDelegate:(id<TTVideoEngineDelegate>)adsPlayerDelegate;

@end

// proxy class for forwarding callbacks to both video engine delegates
@implementation VideoEngineDelegateProxy

- (instancetype)initWithMediaAdsDelegate:(id<TTVideoEngineDelegate>)mediaAdsDelegate
                       adsPlayerDelegate:(id<TTVideoEngineDelegate>)adsPlayerDelegate {
    self = [super init];
    if (self) {
        // keep a reference to the MediaAds delegate and our own delegate
        _mediaAdsDelegate = mediaAdsDelegate;
        _adsPlayerDelegate = adsPlayerDelegate;
    }
    return self;
}

#pragma mark - TTVideoEngineDelegate forwarding
// example callback forwarding method
- (void)videoEnginePrepared:(TTVideoEngine *)videoEngine {
    if ([self.mediaAdsDelegate respondsToSelector:@selector(videoEnginePrepared:)]) {
        [self.mediaAdsDelegate videoEnginePrepared:videoEngine];
    }
    if ([self.adsPlayerDelegate respondsToSelector:@selector(videoEnginePrepared:)]) {
        [self.adsPlayerDelegate videoEnginePrepared:videoEngine];
    }
}

// implement forwarding method for all other callbacks ...
@end
```

### AdPlaybackSession player
The VideoOne demo uses a `TTVideoEngine` player, but an `AdPlaybackSession` can be created using any video player that conforms to the `PlayerWrapperDelegate` protocol. This allows for flexibility if you choose to use a different player backend.
```Objective-C
@protocol PlayerWrapperDelegate <NSObject, IMAContentPlayhead>

@property (nonatomic, weak) id<PlayerEventListenerDelegate> delegate;

@property (nonatomic, strong) AdSessionLog *mLog;

- (NSTimeInterval)getDuration;

- (nullable UIView *)getPlayerView;

- (NSTimeInterval)getPosition;

- (BOOL)isComplete;

- (BOOL)isPaused;

- (BOOL)isPlaying;

- (BOOL)isLooping;

- (void)mute:(BOOL)isMute;

- (void)pause;

- (void)releasePlayer;

- (void)seekTo:(NSTimeInterval)position;

- (void)play;

- (void)prepareToPlay;

- (void)stop;

- (void)setPlayerEventDelegate:(id<PlayerEventListenerDelegate> _Nonnull)delegate;

- (void)setIsCompletedForLoop:(BOOL)isCompleted;

@end
```

