To quickly add a feature-rich short drama video experience to your iOS application, you can integrate the BytePlus VideoOne short drama demo. This guide provides step-by-step instructions to integrate the demo source code, customize the data layer for your business needs, and implement key features like paid content unlocking and screen capture protection.
# Overview of integration solutions
BytePlus VideoOne offers three integration solutions tailored for short drama videos. The table below highlights the distinctions among these three solutions.

| **Solution / Introduction** | **Integrating the short drama demo** | **Integrating the PlayerKit controls** | **Integrating the player SDK** |
| --- | --- | --- | --- |
| Description | Built for short drama videos. This solution supports screen capture protection, episode switching, resuming playback seamlessly when switching pages, unlocking paid content and other short drama-specific features. | Built on the player SDK. This solution provides the ability to play videos on the View level and hides the player's implementation details. | This solution directly integrates the player SDK. |
| Target client | Clients who are developing new apps and are able to replace the business APIs. | Clients who are developing new apps or have existing apps and are able to replace the player architecture. | * Clients with existing apps who want to quickly release short drama features by reusing existing playback code and replacing the player engine.  <br> * Clients with strong development capabilities, extensive player experience, and ample time for development. |
| Required development | Clients only need to replace the business APIs and convert the data structures. | Clients need to implement network connectivity, UI components for the short drama player, playback page switching, payment and other features themselves. | Clients need to implement the player features and short drama specific features themselves. |
| Time to market | As little as one week | Two weeks to one month | Approximately one month |
| Related links | This document | [MDPlayerKit](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/MiniDrama/Classes/Player) | [iOS Player SDK: Quickstart](https://docs.byteplus.com/en/docs/byteplus-vod/docs-getting-started-with-the-ios-player-sdk) |
# Running the demo
Before integrating the short drama demo, we recommend that you refer to the document [Running the demo (iOS)](https://docs.byteplus.com/en/docs/byteplus-vos/docs-running-the-demo-ios-) and try the short drama video features.
# Integrating the short drama demo
The source code of the short drama demo is located in the [MiniDrama](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/MiniDrama) folder under the [VideoOneSolutions](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS) repository. Its directory structure is as follows:
```Bash
.
├── VideoOne               # Project main entry
├── Component              # Component modules
│   ├── App                # App entry
│   ├── AppConfig          # App configs 
│   ├── ToolKit            # Toolkit
│   ├── LoginKit           # Login kit
│   ├── VideoPlaybackEdit  # Video playback scene
│   ├── MiniDrama          # Short drama scene
│   └── ...
```

## Step 1: Download the source code
Run the following commands to download the source code to your local device:
```Bash
git clone https://github.com/byteplus-sdk/VideoOneSolutions
cd Client/iOS
```

## Step 2: Copy the code
Copy the following folders to the root directory of your project：
```Bash
Component/App
Component/AppConfig
Component/ToolKit
Component/MiniDrama
Component/LoginKit
```

## Step 3: Configure the Podfile
Refer to [VideoOneSolutions/Client/iOS/Podfile](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Podfile) and copy the following dependencies to your own Podfile. If a dependency version in your project conflicts with a version required by the demo, your project's existing version takes precedence.
```Ruby
source 'https://cdn.cocoapods.org/'
source 'https://github.com/byteplus-sdk/byteplus-specs.git'
source 'https://github.com/volcengine/volcengine-specs.git'

platform :ios, '13.0'

target 'YourProjectTarget' do
    # use_frameworks!
    use_modular_headers!

    # pod 'App', :path => './Component/App'
    pod 'AppConfig', :path => './Component/AppConfig'
    pod 'ToolKit', :path => './Component/ToolKit'

    pod 'LoginKit', :path => './Component/LoginKit'
    pod 'MiniDrama', :path => './Component/MiniDrama'

    pod 'TTSDKFramework', 'x.x.x-premium', :subspecs => ['Player']

    pod 'AFNetworking', '~> 4.0'
    pod 'SDWebImage', '~> 5.12.0'
end
```

## Step 4: Import the short drama module
The source code for the short drama module is located in the folder [VideoOneSolutions/Client/iOS/Component](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component). If you only need to integrate short drama videos, you only need copy the following folders: [App](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/App), [AppConfig](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/AppConfig), [ToolKit](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/ToolKit), [LoginKit](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/LoginKit)`,` and [MiniDrama](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/MiniDrama).
## Step 5: Initialize the Player SDK
Refer to [MiniDrama.m](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Entry/MiniDrama.m) and initialize the Player SDK.
```Objective-C
+ (void)setupTTSDK {
    #ifdef DEBUG
    [TTVideoEngine setLogFlag:TTVideoEngineLogFlagAll];
    #endif
    TTSDKConfiguration *cfg = [TTSDKConfiguration defaultConfigurationWithAppID:VODAPPID licenseName:VODLicenseName];
    cfg.channel = [NSBundle.mainBundle.infoDictionary objectForKey:@"CHANNEL_NAME"];
    cfg.appName = [NSBundle.mainBundle.infoDictionary objectForKey:@"CFBundleName"];
    cfg.bundleID = NSBundle.mainBundle.bundleIdentifier;
    cfg.appVersion = [NSBundle.mainBundle.infoDictionary objectForKey:@"CFBundleShortVersionString"];
    cfg.shouldInitAppLog = YES;
    cfg.serviceVendor = TTSDKServiceVendorSG;
    TTSDKVodConfiguration *vodConfig = [[TTSDKVodConfiguration alloc] init];
    vodConfig.cacheMaxSize = 300 * 1024 * 1024;
    cfg.vodConfiguration = vodConfig;
    [TTSDKManager setCurrentUserUniqueID:[LocalUserComponent userModel].uid ?: @""];
    [TTSDKManager setShouldReportToAppLog:YES];
    [TTSDKManager startWithConfiguration:cfg];
}
```

## Step 6: Integrate the short drama pages
The [MiniDramaMainViewController](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/MiniDramaMainViewController.m) is the main view controller for short drama videos. It includes two default tabs: "Home" and "For you". You can develop more tabs according to your business needs. You can add the [MiniDramaMainViewController](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/MiniDramaMainViewControll) to the `rootViewController` of the `UINavigationController` for integration.
# Tailoring to your business requirements
To quickly align the short drama demo with your business requirements, follow the steps below:

1. Customize the data layer to efficiently incorporate essential short drama video features. This involves:
   1. Replacing the business APIs: Implement the APIs defined in [MiniDrama/Main/Data/Network/NetworkingManager+MiniDrama.h](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Network/NetworkingManager%2BMiniDrama.h).
   2. Converting the data structure: Adapt the data structure received from your app server to match the structure defined in the short drama demo, enabling utilization of existing business workflows in the demo.
2. Customize short drama-specific functionalities like unlocking paid content.

## Source code structure of the short drama demo
```Bash
├── VideoOne                   # Project main entry
├── Component                  # Component modules
│   ├── App                    # App entry
│   ├── AppConfig              # App configs 
│   ├── ToolKit                # Toolkit
│   ├── LoginKit               # Login kit
│   ├── VideoPlaybackEdit      # Video playback scene
│   ├── MiniDrama              # Short drama scene
        ├──Resources           # Short drama resources
        ├──Classes
            ├── Entry
            │    └── MiniDrama.m    # BytePlus VOD Player initialization config
            ├── Main
            │    ├── MiniDramaMainViewController.m # Short drama main controller
            │    ├── CommonUI   # Short drama common UI
            │    ├── Data
            │    │   ├── Model  # Short drama data model
            │    │   └── Network  # Short drama APIs
            │    ├── MiniDramaChannelPage  # For you page
            │    ├── MiniDramaHomePage   # Home page
            │    └── PlayerModules   # Player UI widgets
            └── Player        # Player kit
```

## Adapting the data layer
### Converting the data structure
The short drama demo layer has defined data structures including [MDDramaInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaInfoModel.h) (short drama information), [MDDramaEpisodeInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaEpisodeInfoModel.h) (short drama episode information), and [MDSimpleEpisodeInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDSimpleEpisodeInfoModel.h) (episode unlocking information). You will be required to convert the data structures received from your app server into these data structures. The table below details these data structures.

| **Class** | **Parameter** | **Type** | **Required** | **Description** |
| --- | --- | --- | --- | --- |
| [MDDramaInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaInfoModel.h) <br> （Short drama info） | dramaId | NSString | Required | Short drama ID |
|  | dramaTitle | NSString | Required | Short drama name |
|  | dramaCoverUrl | NSString | Required | Short drama cover URL |
|  | dramaPlayTimes | NSInteger | Required | Playback count |
|  | dramaDisplayType | MiniDramaVideoDisplayType | Required <br>  | Video orientation <br> 0 : MiniDramaVideoDisplayTypeVertical <br> 1 : MiniDramaVideoDisplayTypeHorizontal |
|  | dramaLength | NSInteger | Required | Total number of episodes |
|  | newReleased | BOOL <br>  | Optional | Whether to display a "New" label (NO by default). <br> YES : Display <br> NO : Hide |
| [MDDramaEpisodeInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaEpisodeInfoModel.h) <br> （Episode info） <br>  | videoId | NSString | Required | Video ID |
|  | dramaTitle | NSString | Optional | Short drama name |
|  | duration | NSString | Required | Video duration (in seconds) |
|  | coverUrl | NSString | Required |  Video cover URL |
|  | playAuthToken | NSString | Optional | The PlayAuthToken for the video, issued by your app server via the BytePlus VOD Server SDK. |
|  | subtitleToken | NSString | Optional | The subtitle token for the video's data source which can be issued by the AppServer through the BytePlus VOD Server SDK. |
|  | playTimes | NSInteger | Optional | Playback count |
|  | videoTitle | NSString | Optional | Video title |
|  | videoDescription | NSString | Optional | Subheading of the video |
|  | createTime | NSString | Optional | Time of uploading in *RFC3339* format |
|  | authorName | NSString | Optional | Uploader name |
|  | authorId | NSString | Optional | Uploader's userId |
|  | likeCount | NSUInteger | Optional | Number of likes |
|  | commentCount | NSUInteger | Optional | Number of comments |
|  | videoHeight | NSString | Required | Video height |
|  | videoWidth | NSString | Required | Video width |
|  | order | NSInteger | Required | Video position in the episode list |
|  | dramaId | NSString | Required | Short drama ID |
|  | vip | BOOL | Required | Whether payment is required to unlock (NO by default). |
|  | displayType | MDDisplayCardType <br>  | Required <br>  | Display type of the short drama card on the "For you" page <br> 0 : MDDisplayCardTypeDefault, without cover image <br> 1 : MDDisplayCardTypeCard, with cover image |
| [MDSimpleEpisodeInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDSimpleEpisodeInfoModel.h) <br> （Episode unlocking info） | subtitleToken | NSString | Required | The subtitle token for the video's data source which can be issued by the AppServer through the BytePlus VOD Server SDK. |
|  | videoId | NSString | Required | Video ID |
|  | order | NSInteger | Optional | Video position in the episode list |
|  | playAuthToken | NSString | Required | The PlayAuthToken for the video, issued by your app server via the BytePlus VOD Server SDK. |
### Replacing the business APIs

1. Update the "Home" page API: Refer to the [getDramaDataForHomePageData](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Network/NetworkingManager%2BMiniDrama.h) API of the [NetworkingManager+MiniDrama](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Network/NetworkingManager%2BMiniDrama.h) object to implement an API that fetches short drama recommendations for the "Home" page. You can change the API request to your own request and encapsulate the requested data as an [MDDramaInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaInfoModel.h) object and return it to the page for display. If the fields in the current [MDDramaInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaInfoModel.h) object do not meet your business needs, you can also modify the source code to add more fields.

```Objective-C
// NetworkingManager+MiniDrama.m

@implementation NetworkingManager (MiniDrama)

+ (void)getDramaDataForHomePageData:(void (^__nullable)(MDHomePageData *))success
                            failure:(void (^__nullable)(NSString *_Nonnull))failure {
    [NetworkingManager postWithPath:@"drama/v1/getDramaChannel"
                         parameters:@{}
                              block:^(NetworkingResponse *_Nonnull response) {
        if (!response.result) {
            if (failure) {
                failure(response.error.localizedDescription);
            }
            return;
        }
        MDHomePageData *pageData = [[MDHomePageData alloc] init];
        if ([response.response isKindOfClass:[NSDictionary class]]) {
            NSDictionary *dic = (NSDictionary *)response.response;
            pageData = [MDHomePageData yy_modelWithDictionary:dic];
        }
        if (success) success(pageData);
        
    }];
}

@end
```


2. Update the "Info" page API: Refer to the [getDramaDataForDetailPage](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Network/NetworkingManager%2BMiniDrama.h) API of the [NetworkingManager+MiniDrama](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Network/NetworkingManager%2BMiniDrama.h) object to implement an API that gets short drama information for the "Info" page. You can change the API request to your own request and encapsulate the requested data as an [MDDramaEpisodeInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaEpisodeInfoModel.h) object and return it to the page for display. If the fields in the current [MDDramaEpisodeInfoModel](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaEpisodeInfoModel.h) object cannot meet your business needs, you can also modify the source code to add more fields.

```Objective-C
// NetworkingManager+MiniDrama.m

@implementation NetworkingManager (MiniDrama)

+ (void)getDramaDataForDetailPage:(NSString *)dramaId 
                          success:(void (^)(NSArray<MDDramaEpisodeInfoModel *> * _Nonnull))success
                          failure:(void (^)(NSString * _Nonnull))failure {
    NSDictionary *param = @{ @"drama_id": dramaId, @"user_id": [LocalUserComponent userModel].uid};
    [NetworkingManager postWithPath:@"drama/v1/getDramaList"
                         parameters:param
                              block:^(NetworkingResponse * _Nonnull response) {
        if (!response.result) {
            if (failure) {
                failure(response.error.localizedDescription);
            }
            return;
        }
        NSMutableArray *dramaDataList = [[NSMutableArray alloc] init];
        if ([response.response isKindOfClass:[NSArray class]]) {
            NSArray *arr = (NSArray *)response.response;
            for (NSDictionary *dic in arr) {
                MDDramaEpisodeInfoModel *infoModel = [MDDramaEpisodeInfoModel yy_modelWithDictionary:dic];
                [dramaDataList addObject:infoModel];
            }
        }
        if (success) success(dramaDataList);
    }];
}
@end
```


3. Update the "For you" page API: Refer to the [getDramaDataForChannelPage](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Network/NetworkingManager%2BMiniDrama.h) API of the [NetworkingManager+MiniDrama](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Network/NetworkingManager%2BMiniDrama.h) object to implement an API that gets short drama lists on the "For you" page. Customize the API request as needed and package the retrieved data into an [MDDramaFeedInfo](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaFeedInfo.h) object to be returned for display on the page. If the existing fields within the [MDDramaFeedInfo](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/Data/Model/MDDramaFeedInfo.h) object do not align with your business requirements, you have the flexibility to enhance the source code by adding additional fields.

```Objective-C
// NetworkingManager+MiniDrama.m
@implementation NetworkingManager (MiniDrama)

+ (void)getDramaDataForChannelPage:(NSRange)range 
                           success:(void (^)(NSArray<MDDramaFeedInfo *> * _Nonnull))success
                           failure:(void (^)(NSString * _Nonnull))failure {
    NSDictionary *param = @{ @"offset": @(range.location), @"page_size": @(range.length)};
    [NetworkingManager postWithPath:@"drama/v1/getDramaFeed"
                         parameters:param
                              block:^(NetworkingResponse * _Nonnull response) {
        if (!response.result) {
            if (failure) {
                failure(response.error.localizedDescription);
            }
            return;
        }
        NSMutableArray *dramaDataList = [[NSMutableArray alloc] init];
        if ([response.response isKindOfClass:[NSArray class]]) {
            NSArray *arr = (NSArray *)response.response;
            for (NSDictionary *dic in arr) {
                MDDramaFeedInfo *infoModel = [MDDramaFeedInfo yy_modelWithDictionary:dic];
                [dramaDataList addObject:infoModel];
            }
        }
        if (success) success(dramaDataList);
    }];
}
@end
```

## Customizing short drama-specific features
### Screen capture protection
Refer to [MDVideoPlayerController+DisRecordScreen.m](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Player/MDPlayerKit/Classes/PlayerCore/MDVideoPlayerController%2BDisRecordScreen.m) to implement anti-screen recording through monitoring system screen capture events.
```Objective-C
- (void)registerScreenCapturedDidChangeNotification {
    if (@available(iOS 11.0, *)) {
        [[NSNotificationCenter defaultCenter] removeObserver:self name:UIScreenCapturedDidChangeNotification object:nil];
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(onScreenCapturedChange:) name:UIScreenCapturedDidChangeNotification object:nil];
    }
}

- (void)onScreenCapturedChange:(NSNotification *)notification {
    if (@available(iOS 11.0, *)) {
        UIScreen *screen = notification.object;
        if (screen) {
            if ([screen isCaptured]) {
                // Screen capture started.
            } else {
                // Screen capture stopped.
            }
        }
    }
}
```

### Unlocking paid content
The process for unlocking and playing paid content in the short drama demo is shown in the image below.

![](../../../img/01_Short_drama_videos_-_iOS_integration_029.png)

The demo implements a complete process for unlocking paid content, but no third-party SDK is integrated. It only has a simulated payment process in [MiniDramaDetailFeedViewController.m](https://github.com/byteplus-sdk/VideoOneSolutions/blob/main/Client/iOS/Component/MiniDrama/Classes/Main/MiniDramaChannelPage/DramaDetailPage/MiniDramaDetailFeedViewController.m), which will need to be replaced with your own code.
```Objective-C
- (void)unlockEpisodes:(NSArray<MDDramaEpisodeInfoModel *> *)needPayArr {
    NSMutableArray  *vidList = [[NSMutableArray alloc] initWithCapacity:needPayArr.count];
    [needPayArr enumerateObjectsUsingBlock:^(MDDramaEpisodeInfoModel * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
        [vidList addObject:obj.videoId];
    }];
    // ...
    [NetworkingManager getDramaDataForPaymentEpisodeInfo:[LocalUserComponent userModel].uid
                                                 dramaId:self.fromDramInfo.dramaId
                                                 vidList:[vidList copy]
                                                 success:^(NSArray<MDSimpleEpisodeInfoModel *> * _Nonnull videoList) {
        // success
    } failure:^(NSString * _Nonnull errMessage) {
        // failure
    }];
}
```

