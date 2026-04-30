This document provides a brief introduction to how VideoOne uses BytePlus RTC SDK to implement advanced features. It aims to assist users in efficiently setting up advanced features for RTC.
# RTC Integration Guide
## Prerequisites
A valid BytePlus account with [BytePlus RTC ](https://console.byteplus.com/rtc/workplaceRTC)service activated. Refer to [Before Using RTC Service ](https://docs.byteplus.com/en/byteplus-rtc/docs/69865)for detailed instructions.
## Implement features
VideoOne demonstrates how to use the BytePlus RTC SDK to implement advanced features. Currently, it supports these categories: core function, room management, audio&video transmission, audio management, video management, message management and other components. For more details on implementing these features, please refer to the following documentation:

| **Category** | **Function** | **Reference Document** |
| --- | --- | --- |
| Core functions | Quick start | [BytePlus RTC : Quick Start Guide](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-70123) |
| Room management | Muti-room | [ BytePlus RTC : Join Multiple RTC Rooms](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-196844) |
| Audio and video transmission | Cross-room PvP | [ BytePlus RTC : Forward Media Stream across Rooms](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-104398) |
| Audio management | Raw audio data | [BytePlus RTC : Get Raw Audio Data](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-1178324) |
|  | Sound effect mixing | [ BytePlus RTC : Play Sound Files](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-1178326) |
|  | Music mixing | [ BytePlus RTC : Play Music Files](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-70141) |
|  | Sound effects | [ BytePlus RTC : Audio Effect and Denoiser](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-1178327) |
| Video management | Picture-in-Picture | [BytePlus RTC : Support Foreground Multi-tasking on Mobiles](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-1178325) |
|  | Video configuration | [BytePlus RTC : Set Video Encoder Configuration](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-70122) |
|  | Video rotation | [BytePlus RTC : Video Rotation](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-106458) |
| Other components | BytePlus Effects | [BytePlus RTC : Video Effects (paid feature)](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-114717) |
|  | Push to CDN | [BytePlus RTC : Push to CDN](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-69817) |
| Message management | SEI message | [ BytePlus RTC : Supplemental Enhancement Information (SEI)](https://docs.byteplus.com/en/docs/byteplus-rtc/docs-70140) |
|  | Synchronize message with frames |  |

Refer to the open-source code to integrate these features into your app:

* [iOS : RTCAPIExample](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/iOS/Component/RTCAPIExample/RTCAPIExample)
* [Android : RTCAPIExample](https://github.com/byteplus-sdk/VideoOneSolutions/tree/main/Client/Android/solutions/rtc-api-example)


