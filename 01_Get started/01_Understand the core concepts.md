BytePlus Video One Solution provides a comprehensive architecture for building video applications, organized into high-level Solutions and foundational Product capabilities. This guide explains these core concepts, including our foundational products (MediaLive, VOD, and RTC) and how your application interacts with them through SDKs and APIs. Understanding this structure is the first step to building your application efficiently.
## Solutions and product capabilities
BytePlus VideoOne provides two distinct levels of abstraction for building your application: **Solutions** and **Product capabilities**. The choice between them depends on your desired development speed and level of customization.

* **Solutions**: These are high-level, end-to-end packages engineered for specific business scenarios, such as Short Drama or Interactive Live Streaming. A Solution bundles all the necessary client-side and server-side components required for a particular use case, designed to significantly accelerate your development and go-to-market timeline.
* **Product capabilities**: These are the foundational, modular services that provide the core functionalities of the VideoOne platform. They offer granular control and maximum flexibility, allowing you to combine them as needed to build highly customized applications that are not covered by a pre-packaged Solution.

## Our foundational products
Our product capabilities are organized into three foundational products. Understanding their distinct responsibilities is key to using BytePlus Video One Solution effectively.

* **BytePlus MediaLive**: This product manages all functionalities related to **live video streaming**. It is used for scenarios where content is created and broadcast in real-time to a one-to-many audience, such as a live event, a sports broadcast, or a standard host-led stream.
* **BytePlus Video On Demand (VOD)**: This product handles all workflows related to **pre-recorded video files**. It is used for scenarios where media is uploaded, stored, processed, and played back by users at any time, such as in short drama apps, film streaming services, or online course libraries.
* **BytePlus Real-Time Communication (RTC)**: This product enables **interactive, multi-directional communication** with a primary focus on ultra-low latency. It is used for scenarios like one-to-one video calls, multi-person audio chats, or live sessions where hosts and audience members interact directly via video (co-hosting).

## How you interact with VideoOne: SDKs and APIs
Your application will interface with our products through two primary methods:

* **Client-side SDKs**: These are software development kits that you integrate directly into your client application (Android, iOS, or Web). The SDKs provide the necessary interfaces to implement all client-facing features, such as initiating a live stream, playing a video, enabling beauty effects, or joining a video call.
* **Server-side APIs**: These are OpenAPIs that you call from your own backend server. They are used for administrative tasks, security operations (such as generating authentication tokens for your clients), and receiving event notifications from the VideoOne platform via webhooks. A typical integration requires coordination between your client application using our SDKs and your server using our APIs.

## Putting it all together: A typical workflow
The following example illustrates how these concepts apply to a practical use case: building a short drama application.

1. **Business requirement**: To create a short video application where users can swipe through a vertical feed of video episodes.
2. **Solution selection**: After reviewing the documentation, the short drama solution is identified as the most suitable package, as it is pre-configured for this specific use case.
3. **Component analysis**: An analysis of the Solution reveals that it is primarily built using the BytePlus Video On Demand (VOD) product to store, manage, and deliver the video files.
4. **Client-side implementation**: Within the client application, the developer integrates the VideoOne Client-side SDK. The player component from the SDK is then used to implement the user-facing features: playing, pausing, and swiping between videos in the feed.
5. **Server-side integration**: On the backend server, the developer uses the Server-side APIs to manage the video content library and to generate secure playback credentials that are passed to the client application.

With this foundational knowledge, you are now prepared to explore our offerings in greater detail.

**Next step**: [Try the demo app](https://docs.byteplus.com/en/docs/byteplus-vos/docs-byteplus-videoone-demo-app_1)

