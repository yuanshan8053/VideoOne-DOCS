To build an interactive video feed with features like vertical scrolling, commenting, and liking, you can start with the provided Demo. This guide explains how to customize the Demo's player, language settings, and deployment configurations to efficiently implement a similar solution in your web project.
# Prerequisites

* A valid BytePlus account with BytePlus VOD activated. Please refer to [Step 1: Enable the BytePlus VOD service](https://docs.byteplus.com/en/docs/byteplus-vod/docs-getting-started#b4e9d150) in Getting started with BytePlus VOD.
* Successfully run the demo project following the procedures in [Running the demo (Web/H5)](https://docs.byteplus.com/en/docs/byteplus-vos/docs-running-the-demo-web).

# Implementation
After successfully running the project, you can customize your own feed stream website by modifying a few key configuration files.
## Player configuration
This section provides an overview of the player configurations in the demo.
> For more detailed configurations, please refer to BytePlus VOD [Web Player SDK API reference](https://docs.byteplus.com/en/docs/byteplus-vod/docs-web-api-reference). 

| Parameter | Data Type | Default Value | Description |
| --- | --- | --- | --- |
| el | HTMLElement | \- | The HTML element where the VePlayer will be inserted for display and interaction. |
| getVideoByToken | [getVideoByToken](https://docs.byteplus.com/en/docs/byteplus-vod/docs-web-api-reference#getvideobytoken) | \- | The configurations for fetching the video that you want to play. Refer to [getVideoByToken](https://docs.byteplus.com/en/docs/byteplus-vod/docs-web-api-reference#getvideobytoken) for detailed instructions. |
| poster | string | \- | The URL of the cover image. |
| mobile | ```JSON <br> { <br>    gradient: string <br> } <br> ``` <br>  | \- <br>  | The interactive plugin for the player on the mobile web. <br> `gradient`: Whether to enable gradient shadows. Supported values: <br>  <br> * normal: normal shadow effect <br> * none: no shadow <br> * top: shadow on top <br> * bottom: shadow on bottom |
| autoplay | boolean | false | Whether to enable automatic playback. <br>  <br> * true: Yes <br> * false: No <br>  <br> > Due to browser policies, autoplay may not always succeed and is dependent on factors such as browser environment, user behavior, and browser configurations. |
| loop | boolean | false | Whether to enable loop playback. <br>  <br> * true: Yes <br> * false: No |
| enableDegradeMuteAutoplay | boolean | false | Whether to fall back to muted autoplay if autoplay with sound fails. Supported values: <br>  <br> * true: Yes <br> * false: No <br>  <br> When both `enableDegradeMuteAutoplay` and `autoplay` are set to `true`, if the current browser does not support autoplay with audio, the player will degrade to muted autoplay. Note that muted autoplay may also fail. |
| closeVideoClick | boolean | false | * On desktop: Whether to toggle play/pause by clicking the player area. <br> * For mobile: Whether to toggle play/pause by closing the click/touchend behavior of the video. |
| closeVideoDblclick | boolean | false | * On desktop: Whether to disable the ability to enter fullscreen mode by double-clicking on the player. <br> * For mobile: Whether to disable the ability to toggle play/pause by double-tapping. |
| controls | boolean | true | Control bar plugin, registered by default. |
| ignores | string[] | [] | Used to disable plugins. VePlayer has built-in plugins for various common functionalities. To disable a specific built-in plugin, you can provide the plugin name (case-insensitive) as a parameter. |
| start | ```JSON <br> { <br>   disableAnimate: boolean, <br>   isShowPause: boolean, <br> } <br> ``` <br>  | false | Button to toggle play/pause in the center of the player. <br> `disableAnimate`: Whether to disable click animation. <br>  <br> * true: Yes <br> * false: No (default) <br>  <br> `isShowPause`: Whether to keep the interface visible when paused. <br>  <br> * true: Yes <br> * false: No (default) |
## Multiple languages configuration
To modify translation or add multiple languages, you can modify the configurations in the `translation.ts` file, or utilize libraries that implement internationalization features, such as [@formatjs/intl](https://www.npmjs.com/package/@formatjs/intl).
## Deployment
To generate the build output, simply run `pnpm run build`. During the deployment phase, note the `base` and `build` configurations in the `vite.config.ts` file. Here are two key points to consider:

* The build output directory is set to "output" by default. If you need to adjust it, you can modify the `outDir` property under the `build` configuration.
* Configure the public base path for static assets: If your assets are hosted directly on the same server, you can keep the default root path (`/`). However, if you are using a CDN, obtain your acceleration domain and replace the *************** placeholder in the configuration with your actual domain.

```JavaScript
export default defineConfig({
  base: isProd ? '***************' : '/',
  server: {
    port: 8000,
  },
  build: {
    outDir: 'output',
  },
});
```


