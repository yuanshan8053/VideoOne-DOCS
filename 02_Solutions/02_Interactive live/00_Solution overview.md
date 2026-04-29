Interactive live streaming incorporates interactive features into the standard live streaming experience. This includes activities such as host PK battle and co-hosting with audience members, providing a more engaging experience than standard live streaming that only allows interaction through text chat and virtual gifts. Interactive live streaming brings the host and audience closer together, resulting in a more immersive and enjoyable experience for both parties.
<div style="text-align: center"><img src="https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/1be2eb3d58c449b58d67d8b12482f1cc~tplv-goo7wpa0wc-image.image" width="864px" /></div>

## Billing
This solution incurs fees for both live streaming and RTC services. For details, refer to [BytePlus MediaLive pricing overview](https://docs.byteplus.com/en/byteplus-media-live/docs/overview-1) and [RTC service fees](https://docs.byteplus.com/en/byteplus-rtc/docs/69871).
## Highlights

* This solution is optimized for live streaming that involves occasional co-hosting.
* This solution is more cost-effective, as RTC service fees are charged only during co-hosting.

## Architecture
This section describes the technical architecture of the solution.
### Solo hosting
![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/840c48890d564e4a9ae7859fea94f731~tplv-goo7wpa0wc-image.image)
In solo hosting, the RTC engine handles audio and video capture, preview, and beauty AR. The captured audio and video data is then sent to the live pusher for encoding and streaming.
### Co-hosting with audience members
![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/bafd25972b594264811de949414efd32~tplv-goo7wpa0wc-image.image)
When the host begins co-hosting with audience members, the live pusher stops encoding and streaming. The RTC engine then mixes the host's and co-hosts' streams on the server side and pushes the resulting stream to the CDN.
During this process, the host receives the audio and video from the co-hosts, and the audience can watch the mixed stream on their apps.
Once co-hosting stops, solo-hosting mode resumes.
### Host PK battle
![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3f463efdea4e4c20942c340165968c5a~tplv-goo7wpa0wc-image.image)
When the host begins a host PK battle, the live pusher stops encoding and streaming, and the RTC engine begins mixing the streams from host A and host B on the server side and pushing the resulting mixed stream to the CDN.
During this process, host A receives the audio and video from host B, and the audience can watch the mixed stream on their apps.
Once the host PK battle stops, solo-hosting mode resumes.
## Implementation
For implementation details, see the following topics:

* [Implementing interactive live for Android](/1rce8t3b/8557)
* [Implementing interactive live for iOS](/1rce8t3b/8558)
* [Implementing interactive live for Web](/1rce8t3b/s41wz9lu)
* [Implementing interactive live for Server](/1rce8t3b/v1ipuiny)
