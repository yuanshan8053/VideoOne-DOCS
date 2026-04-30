This document provides a comprehensive list of the SDKs integrated into each release of the VideoOne demo app. By following the version combinations stated in this article, you can ensure a smooth integration process without encountering any dependency conflicts or compatibility issues that may arise from integrating different BytePlus SDKs.
## Version 5.3.0-5.4.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| TTSDKFramework | 1.48.200.2-premium | LivePull-RTS | Streaming pulling | N/A |
|  |  | LivePush-RTS | Streaming pushing | Dependencies: TTVideoEditor |
|  |  | RTCSDK | Real-time interaction | N/A |
|  |  | ByteAudio | Audio processing | Used together with SDKs such as RTC-Framework or LivePull-RTS |
|  |  | Uploader | VOD upload | N/A |
|  |  | Player | VOD playback | N/A |
| effect-sdk | 4.4.3_standard | N/A | Video effects, such as beauty AR and filters | N/A |
| TTVideoEditor | 11.8.1.83-D | N/A | Video shooting | N/A |
| TTNetworkManager | 4.2.210.20 | N/A | Network | Basic component |
| MediaAdsToB | 2.0.1 | N/A | CSAI in MiniDrama | Adapter between google-ima-sdk and BytePlus VOD |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull_premium | 1.49.300.4 | Streaming pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush | 1.49.300.4 | Stream pushing and real-time audio/video interaction | N/A |
| com.bytedanceapi | ttsdk-ttabr | 1.49.300.4 | ABR settings |  |
| com.bytedanceapi | ttsdk-player_premium | 1.49.300.4 | VOD playback | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttuploader | 1.49.300.4 | VOD upload | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttlicense2 | 1.49.300.4 | N/A | Basic component |
| com.bytedanceapi | ttsdk-ttcommon | 1.49.300.4 | N/A | Basic component |
| com.bytedance.applog | RangersAppLog-Lite-global | 6.14.3 | N/A | Basic component |
| com.squareup.okhttp3 | okhttp | 4.12.0 | N/A | Basic component |
| commons-net | commons-net | 3.9.0 | N/A | Basic component |
| com.byteplus | BytePlusRTC | 3.58.1.53300 | * RTM stream pulling <br> * Interactive Live <br> * Chorus <br> * Meida up and down swiping | N/A |
| com.bytedance | effectsdk | cv_tob4.7.3_202510271120_2e88395de69 | Video effects, such as beauty AR and filters | N/A |
| com.byteplus.mediaplus | mediaads | 2.0.0 | CSAI in MiniDrama | Adapter between google-ima-sdk and BytePlus VOD |

## Version 5.2.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| TTSDKFramework | 1.43.300.2-premium | LivePull-RTS | Streaming pulling | N/A |
|  |  | LivePush-RTS | Streaming pushing | Dependencies: TTVideoEditor |
|  |  | RTCSDK | Real-time interaction | N/A |
|  |  | ByteAudio | Audio processing | Used together with SDKs such as RTC-Framework or LivePull-RTS |
|  |  | Uploader | VOD upload | N/A |
|  |  | Player | VOD playback | N/A |
| effect-sdk | 4.4.3_standard | N/A | Video effects, such as beauty AR and filters | N/A |
| TTVideoEditor | 11.8.1.83-D | N/A | Video shooting | N/A |
| TTNetworkManager | 4.1.127.43 | N/A | Network | Basic component |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull_rtc | 1.43.300.3 | Streaming pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush_rtc | 1.43.300.3 | Stream pushing and real-time audio/video interaction | N/A |
| com.bytedanceapi | ttsdk-player_premium | 1.43.300.3 | VOD playback | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttuploader | 1.43.300.3 | VOD upload | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttlicense2 | 1.43.300.3 | N/A | Basic component |
| com.bytedanceapi | ttsdk-ttcommon | 1.43.300.3 | N/A | Basic component |
| com.bytedance.applog | RangersAppLog-Lite-global | 6.14.3 | N/A | Basic component |
| com.squareup.okhttp3 | okhttp | 4.12.0 | N/A | Basic component |
| commons-net | commons-net | 3.9.0 | N/A | Basic component |
| com.byteplus | BytePlusRTC | 3.58.1.20600 | * RTM stream pulling <br> * Interactive Live <br> * KTV <br> * Chorus <br> * Meida up and down swiping | N/A |

## Version 4.0.0–5.0.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| TTSDK | 1.43.300.2-premium | LivePull-RTS | Streaming pulling | N/A |
|  |  | LivePush-RTS | Streaming pushing | Dependencies: TTVideoEditor |
|  |  | RTCSDK | Real-time audio/video interaction | N/A |
|  |  | ByteAudio | Audio processing | Used together with SDKs such as RTC-Framework or LivePull-RTS |
|  |  | Uploader | VOD upload | N/A |
|  |  | Player | VOD playback | N/A |
| effect-sdk | 4.4.3_standard | N/A | Video effects, such as beauty AR and filters | N/A |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull_rtc | 1.43.300.3 | Streaming pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush_rtc | 1.43.300.3 | Streaming pushing and real-time audio/video interaction | N/A |
| com.bytedanceapi | ttsdk-player_premium | 1.43.300.3 | VOD playback | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttuploader | 1.43.300.3 | VOD upload | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttlicense2 | 1.43.300.3 | N/A | Basic component |
| com.bytedanceapi | ttsdk-ttcommon | 1.43.300.3 | N/A | Basic component |
| com.bytedance.applog | RangersAppLog-Lite-global | 6.14.3 | N/A | Basic component |
| com.squareup.okhttp3 | okhttp | 4.12.0 | N/A | Basic component |
| commons-net | commons-net | 3.9.0 | N/A | Basic component |
| com.byteplus | BytePlusRTC | 3.58.1.20600 | * RTM Streaming pulling <br> * Interactive Live <br> * KTV <br> * Chorus <br> * Meida up and down swiping |  |

## Version 3.3.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| TTSDK | 1.41.300.203-rtc | LivePull-RTS | Streaming pulling | N/A |
|  |  | LivePush-RTS | Streaming pushing | Dependencies: TTVideoEditor |
|  |  | RTC-Framework | Real-time audio/video interaction | N/A |
|  |  | ByteAudio | Audio processing | Used together with SDKs such as RTC-Framework or LivePull-RTS |
|  |  | Uploader | VOD upload | N/A |
|  |  | Player | VOD playback | N/A |
| effect-sdk | 4.4.3_standard | N/A | Video effects, such as beauty AR and filters | N/A |
| TTVideoEditor | 11.8.1.83-D | N/A | Video shooting | N/A |
| TTNetworkManager | 5.0.29.2-adu | N/A | N/A | Basic component |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull | 1.41.300.201-rtc | Streaming pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush_rtc | 1.41.300.201-rtc | Streaming pushing and real-time audio/video interaction | N/A |
| com.bytedanceapi | ttsdk-player_premium | 1.41.300.201-rtc | VOD playback | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttuploader | 1.41.300.201-rtc | VOD upload | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttlicense2 | 1.41.300.201-rtc | N/A | Basic component |
| com.bytedanceapi | ttsdk-ttcommon | 1.41.300.201-rtc | N/A | Basic component |
| com.bytedance.applog | RangersAppLog-Lite-global | 6.14.3 | N/A | Basic component |
| com.squareup.okhttp3 | okhttp | 4.12.0 | N/A | Basic component |
| commons-net | commons-net | 3.9.0 | N/A | Basic component |

## Version 3.0.0–3.1.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| TTSDK | 1.40.200.5-premium | LivePull-RTS | Streaming pulling | N/A |
|  |  | LivePush-RTS | Streaming pushing | N/A |
|  |  | RTC-Framework | Real-time audio/video interaction | N/A |
|  |  | ByteAudio | Audio processing | Used together with SDKs such as RTC-Framework or LivePull-RTS |
|  |  | Uploader | VOD upload | N/A |
|  |  | Player | VOD playback | N/A |
| EffectSDK_iOS_TOB | 11.6.0-VE-LENS | N/A | Video effects, such as beauty AR and filters | N/A |
| TTVideoEditor | 11.8.3.1-VE | N/A | Video shooting and editing | Dependencies: <br>  <br> * EffectSDK_iOS_TOB <br> * DVEInject <br> * AudioSdkTob <br> * NLEPlatform |
| NLEPlatform | 0.5.7 | N/A | N/A | Basic component |
| DVEInject | 0.0.5 | N/A | N/A | Basic component |
| AudioSdkTob | 5.0.7-alpha.1-tobonekit | N/A | Audio effects | Basic component |
| TTNetworkManager | 5.0.29.2-adu | N/A | N/A | Basic component |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull | 1.39.100.9.onekit | Streaming pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush_rtc | 1.39.100.9.onekit | Streaming pushing and real-time audio/video interaction | N/A |
| com.bytedanceapi | ttsdk-player_premium | 1.39.100.9.onekit | VOD playback | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttuploader | 1.39.100.9.onekit | VOD upload | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedance.applog | RangersAppLog-Lite-global | 6.14.3 | N/A | Basic component |
| com.squareup.okhttp3 | okhttp | 3.14.9 | N/A | Basic component |
| commons-net | commons-net | 3.9.0 | N/A | Basic component |
| com.bytedance.ugc.framework.libs | vesdk | 11.8.2.15-vevos-tob | Video shooting and editing | Dependencies: <br>  <br> * ck-base <br> * effectsdk <br> * NLEMediaPublic <br> * NLERecorderJava <br> * NLETemplateModel <br> * NLEEditor <br> * NLEProcessor |
| com.bytedance | effectsdk | 202305061135_1_202305061135_870eed96abe | Video effects, such as beauty AR and filter | N/A |
| com.volcengine.ckbase | ck-base | 1.0.0 | N/A | Basic component |
| com.volcengine.ck.nle | NLEMediaPublic | 4.0.2 | N/A | Basic component |
|  | NLERecorderJava | 4.0.2 | N/A | Basic component |
|  | NLETemplateModel | 4.0.2 | N/A | Basic component |
|  | NLEEditor | 4.0.2 | N/A | Basic component |
|  | NLEProcessor | 4.0.2 | N/A | Basic component |

## Version 2.4.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| TTSDK | 1.40.200.5-premium | LivePull-RTS | Streaming pulling | N/A |
|  |  | LivePush-RTS | Streaming pushing | N/A |
|  |  | RTC-Framework | Real-time audio/video interaction | N/A |
|  |  | Uploader | VOD upload | N/A |
|  |  | Player | VOD playback | N/A |
| EffectSDK_iOS_TOB | 11.6.0-VE-LENS | N/A | Video effects, such as beauty AR and filters | N/A |
| TTVideoEditor | 11.8.3.1-VE | N/A | Video shooting and editing | Dependencies: <br>  <br> * EffectSDK_iOS_TOB <br> * DVEInject <br> * AudioSdkTob <br> * NLEPlatform |
| NLEPlatform | 0.5.7 | N/A | N/A | Basic component |
| DVEInject | 0.0.5 | N/A | N/A | Basic component |
| AudioSdkTob | 5.0.7-alpha.1-tobonekit | N/A | Audio effects | Basic component |
| TTNetworkManager | 5.0.29.2-adu | N/A | N/A | Basic component |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull | 1.39.100.9.onekit | Streaming pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush_rtc | 1.39.100.9.onekit | Streaming pushing and real-time audio/video interaction | N/A |
| com.bytedanceapi | ttsdk-player_premium | 1.39.100.9.onekit | VOD playback | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttuploader | 1.39.100.9.onekit | VOD upload | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedance.applog | RangersAppLog-Lite-global | 6.14.3 | N/A | Basic component |
| com.squareup.okhttp3 | okhttp | 3.14.9 | N/A | Basic component |
| commons-net | commons-net | 3.9.0 | N/A | Basic component |
| com.bytedance.ugc.framework.libs | vesdk | 11.8.2.15-vevos-tob | Video shooting and editing | Dependencies: <br>  <br> * ck-base <br> * effectsdk <br> * NLEMediaPublic <br> * NLERecorderJava <br> * NLETemplateModel <br> * NLEEditor <br> * NLEProcessor |
| com.bytedance | effectsdk | 202305061135_1_202305061135_870eed96abe | Video effects, such as beauty AR and filters | N/A |
| com.volcengine.ckbase | ck-base | 1.0.0 | N/A | Basic component |
| com.volcengine.ck.nle | NLEMediaPublic | 4.0.2 | N/A | Basic component |
|  | NLERecorderJava | 4.0.2 | N/A | Basic component |
|  | NLETemplateModel | 4.0.2 | N/A | Basic component |
|  | NLEEditor | 4.0.2 | N/A | Basic component |
|  | NLEProcessor | 4.0.2 | N/A | Basic component |

## Version 2.0.0~2.3.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| TTSDK | 1.37.200.5-premium | LivePull-RTS | Streaming pulling | N/A |
|  |  | LivePush-RTS | Streaming pushing | N/A |
|  |  | RTC-Framework | Real-time audio/video interaction | N/A |
|  |  | Uploader | VOD upload | N/A |
|  |  | Player | VOD playback | N/A |
| EffectSDK_iOS_TOB | 11.6.0-VE-LENS | N/A | Video effects, such as beauty AR and filter | N/A |
| TTVideoEditor | 11.8.3.1-VE | N/A | Video shooting and editing | Dependencies: <br>  <br> * EffectSDK_iOS_TOB <br> * DVEInject <br> * AudioSdkTob <br> * NLEPlatform |
| NLEPlatform | 0.5.7 | N/A | N/A | Basic component |
| DVEInject | 0.0.5 | N/A | N/A | Basic component |
| AudioSdkTob | 5.0.7-alpha.1-tobonekit | N/A | Audio effects | Basic component |
| TTNetworkManager | 5.0.29.2-adu | N/A | N/A | Basic component |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull | 1.37.200.6.onekit | Streaming pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush_rtc | 1.37.200.6.onekit | Streaming pushing and real-time audio/video interaction | N/A |
| com.bytedanceapi | ttsdk-player_premium | 1.37.200.6.onekit | VOD playback | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedanceapi | ttsdk-ttuploader | 1.37.200.6.onekit | VOD upload | Dependencies: RangersAppLog-Lite-global, okhttp |
| com.bytedance.applog | RangersAppLog-Lite-global | 6.14.3 | N/A | Basic component |
| com.squareup.okhttp3 | okhttp | 3.14.9 | N/A | Basic component |
| commons-net | commons-net | 3.9.0 | N/A | Basic component |
| com.bytedance.ugc.framework.libs | vesdk | 11.8.2.15-vevos-tob | Video shooting and editing | Dependencies: <br>  <br> * ck-base <br> * effectsdk <br> * NLEMediaPublic <br> * NLERecorderJava <br> * NLETemplateModel <br> * NLEEditor <br> * NLEProcessor |
| com.bytedance | effectsdk | 202305061135_1_202305061135_870eed96abe | Video effects, such as beauty AR and filter | N/A |
| com.volcengine.ckbase | ck-base | 1.0.0 | N/A | Basic component |
| com.volcengine.ck.nle | NLEMediaPublic | 4.0.2 | N/A | Basic component |
|  | NLERecorderJava | 4.0.2 | N/A | Basic component |
|  | NLETemplateModel | 4.0.2 | N/A | Basic component |
|  | NLEEditor | 4.0.2 | N/A | Basic component |
|  | NLEProcessor | 4.0.2 | N/A | Basic component |

## Version 1.0.0
### iOS

| **Pod** | **Version** | **Subspec** | **Related feature** |
| --- | --- | --- | --- |
| TTSDK | 1.37.200.5-premium | LivePull-RTS | Stream pulling |
|  |  | LivePush-RTS | Stream pushing |
|  |  | RTC-Framework | Real-time audio/video interaction |
| effect-sdk | 4.4.3_standard | N/A | Video effects, such as beauty AR and filters. [Contact your sales representative](https://www.byteplus.com/en/contact) to obtain the BytePlus Effects SDK. |

### Android

| **Group ID** | **Artifact ID** | **Version** | **Related feature** | **Note** |
| --- | --- | --- | --- | --- |
| com.bytedanceapi | ttsdk-ttlivepull | 1.37.200.5 | Stream pulling | Dependencies: commons-net, okhttp |
| com.bytedanceapi | ttsdk-ttlivepush_rtc | 1.37.200.5 | Stream pushing and real-time audio/video interaction | N/A |
| com.squareup.okhttp3 | okhttp | 4.2.1 | N/A | Basic component |
| commons-net | commons-net | 3.6 | N/A | Basic component |
| N/A | effectsdk | 4.3.2 | Video effects, such as beauty AR and filters | [Contact your sales representative](https://www.byteplus.com/en/contact) to obtain the BytePlus Effects SDK. |
