This document provides a summary of new features, improvements, and bug fixes for each release of VideoOne. Refer to these notes to stay informed about the latest product updates.
To ensure a smooth integration and avoid potential dependency conflicts with other BytePlus SDKs, please refer to our detailed list of component versions for each release: [Client SDK components](https://docs.byteplus.com/en/docs/byteplus-vos/docs-version-combination).

## 5.4.0
Release date: 05/03/2026 (iOS), 26/03/2026（Android）

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| New functions | Client-Side Ad Insertion (CSAI) | Added Client-Side Ad Insertion (CSAI) capabilities for short drama solutions, enabling seamless ad integration and monetization on the client side. [Learn more](https://docs.byteplus.com/en/docs/byteplus-vos/csai_integration_overview) |
| (Android only) | Added ABR ability | VideoOne now supports Adaptive Bitrate Streaming (ABR) for video playback. You can enable this feature on the new setting page for all VoD demos to automatically adjust video quality based on network conditions. |
|  | SDK version upgrade | Upgraded the basic media SDK versions to the latest for improved performance and reliability. |
|  | Removed KTV demo | The Online KTV scene has been removed from this version. |
|  | Bug fixes | Fixed several known issues to improve application stability. |

## 5.3.0
Release date: 27/01/2026
This release only includes updates for the **iOS** demo app. You can find the latest code in our [GitHub repository](https://github.com/byteplus-sdk/VideoOneSolutions/commit/bf1667ab02df5bc041f9431919de364a560586a6).

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| New functions | Added ABR ability | VideoOne now supports Adaptive Bitrate Streaming (ABR) for video playback. You can enable this feature on the new setting page for all VoD demos to automatically adjust video quality based on network conditions. |
| Optimization | SDK version upgrade | Upgraded the basic media SDK versions to the latest for improved performance and reliability. |
|  | Removed KTV demo | The Online KTV scene has been removed from this version. |
|  | Bug fixes | Fixed several known issues to improve application stability. |

## 5.2.0
Release date: 02/09/2025

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| General update | Certificate update | The Effect SDK certificate has been updated for both iOS and Android. |
| Platform optimization | iOS - Dependency change | The dependency method for TTSDK has been changed from a static library to a dynamic library. This helps reduce the app's installation package size and may improve performance. |
|  | Android - Compliance updates | To comply with the latest standards, we have made the following adjustments: <br>  <br> * Disabled the collection of non-essential sensor information. <br> * Removed mandatory permissions for MediaLive and unnecessary read/write permissions to better protect user privacy. <br> * Fixed several known issues to improve the overall stability and user experience of the application. |

## 5.0.0
Release date: 17/01/2025

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| New scene with a separate entry point | * Short drama | VideoOne released a new version that offers a comprehensive suite of cloud-based solutions tailored specifically for short dramas.  |

## 4.0.1
Release date: 26/12/2024

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| New scene with a separate entry point | * Media up and down swiping | VideoOne released a new version that supports combined playback of videos and live streams. It also supports playlists that only contain videos or live streams and allows users to switch between media items by swiping up and down. |
| TTSDK version upgrade | * iOS: 1.41.300.203-rtc —> 1.43.300.2-premium <br> * Android: 1.41.300.201-rtc  —> 1.43.300.3 | You can also refer to the [Client SDK components](https://docs.byteplus.com/en/byteplus-vos/docs/version-combination?version=v1.0) for the **latest version of each media SDK** used in VideoOne. |

## 3.3.0
Release date: 26/07/2024

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| New functions with a separate entry point | * Homepage user interface revision <br> * RTC API example <br> * Duet singing | This release adds a Duet Singing scene to the Online KTV solution, provides access guidelines for the RTC API Example, and optimizes the homepage user interface. |
| TTSDK version upgrade | * iOS: 1.40.200.5-premium —> 1.41.300.203-rtc <br> * Android: 1.39.100.9.onekit —> 1.41.300.201-rtc | You can also refer to the [Client SDK components](https://docs.byteplus.com/en/byteplus-vos/docs/version-combination?version=v1.0) for the **latest version of each media SDK** used in VideoOne. |

## 3.1.0
Release date: 23/05/2024

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| New functions with a separate entry point | * Live stream pushing <br> * Live stream pulling | This release introduces a demo that showcases how to initiate live streaming using different sources, including camera-captured streams, screen-sharing streams, local or online video files, and audio streams. It also provides additional settings for stream pushing and pulling, enabling you to quickly implement live broadcasting and viewing features in your project. <br> Refer to [Live streaming](https://docs.byteplus.com/en/docs/byteplus-vos/docs-live-streaming) for implementation. |

## 3.0.0
Release date: 22/03/2024
This release introduces a new solution for an **Online KTV** scenario. You can [install the app](https://docs.byteplus.com/en/byteplus-vos/docs/byteplus-videoone-demo-app_1) to try it out or refer to the [integration guide](https://docs.byteplus.com/en/byteplus-vos/docs/online-ktv-solution-overview?version=v1.0) to implement this scenario in your app.
## 2.4.0
Release date: 31/01/2024

| **Key updates** | **Specifics** | **Description** |
| --- | --- | --- |
| **TTSDK** version upgrade | * iOS: 1.37.200.5-premium —> 1.40.200.5-premium <br> * Android: 1.37.200.6.onekit —> 1.39.100.9.onekit | This upgrade introduces changes to certain APIs, particularly those relevant to the interactive live solution. If you have integrated VideoOne into your project to create interactive live streaming scenarios, it is important to update your code to align with the new version. Please refer to the updated example code provided in the following documents to make the necessary modifications and ensure the proper functioning of your project: <br>  <br> * iOS: [Implementing interactive live for iOS](https://docs.byteplus.com/en/byteplus-vos/docs/implementing-interactive-live-for-ios?version=v1.0) <br> * Android: [Implementing interactive live for Android](https://docs.byteplus.com/en/byteplus-vos/docs/implementing-interactive-live-for-android?version=v1.0) <br>  <br> You can also refer to the [Client SDK components](https://docs.byteplus.com/en/byteplus-vos/docs/version-combination?version=v1.0) for the **latest version of each media SDK** used in VideoOne. |
| **New functions** with a separate entry point | New functions: <br>  <br> * Common player features, including gesture control, picture-in-picture, etc. <br> * Video playlist <br> * [Smart multilingual subtitling](/docs/byteplus-vos/docs-smart-subtitling) <br> * [Preventing screen recording](/docs/byteplus-vos/docs-anti-screen-recording) | In this release, we have curated and showcased a demo of key functions commonly utilized in popular audio and video scenarios. <br> You can experience these functions by clicking on the newly added "**Function**" tab in the bottom navigation bar of the demo app, or explore their implementation in the open-source project. |

## 2.3.0
Release date: 07/12/2023
This version adds the following functions to the **Video Playback & Edit** scene in the demo app.

1. The immersive playback mode for short videos now supports landscape video playback, as well as the ability to switch from landscape to full-screen video playback.
2. Viewer interactions, such as liking videos and adding comments, are supported in the full-screen playback page of all types of video.
3. The medium-form and long-form video half-screen playback pages now include a recommended video list.
4. Playback from custom media sources is supported. For details, refer to the [Custom media source playback](https://docs.byteplus.com/en/docs/byteplus-vos/docs-custom-media-source-playback) document for details.

## 2.0.0
Release date: 10/11/2023

| **Feature** | **Type** | **Description** | **Related Documents** |
| --- | --- | --- | --- |
| Video Playback & Edit | New | The demo app is updated to include the Video Playback & Edit demo. The following features are highlighted in the demo: <br>  <br> * Video playback <br> * Video shooting and multi-track editing <br> * Media upload | * [BytePlus VideoOne demo app](https://docs.byteplus.com/byteplus-vos/docs/byteplus-videoone-demo-app_1) <br> * [Video playback & edit solution overview](/docs/byteplus-vos/docs-video-playback-edit-solution-overview) |
| N/A | Doc update | A new article,  has been added. This article provides a comprehensive list of the SDKs integrated into each release of the VideoOne demo app. Following the version combinations in this article helps prevent dependency conflicts and compatibility issues when integrating different BytePlus SDKs. | * [Client SDK components](https://docs.byteplus.com/en/byteplus-vos/docs/version-combination?version=v1.0) |

## 1.0.0
Release date: 04/08/2023
The first release of BytePlus Video One Solution (VideoOne).

| **Feature** | **Type** | **Description** | **Related Documents** |
| --- | --- | --- | --- |
| BytePlus VideoOne demo app | New | This open-source demo app utilizes multiple BytePlus media SDKs along with BytePlus video cloud services to provide demos for industry-specific scenarios. <br> The following features are highlighted in the demo app: <br>  <br> * Live streaming and playback <br> * Co-hosting, including with audience members or hosts from another live room (host PK battle) <br> * Bullet comments <br> * Gifting <br> * Live room management. | * [BytePlus VideoOne demo app](https://docs.byteplus.com/byteplus-vos/docs/byteplus-videoone-demo-app_1) <br> * [Interactive live streaming solution overview](/docs/byteplus-vos/docs-interactive-live-streaming-solution-overview) |
