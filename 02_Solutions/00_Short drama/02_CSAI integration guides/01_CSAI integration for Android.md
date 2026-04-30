This guide describes how to integrate the BytePlus MediaAds SDK and Google IMA SDK into your VideoOne-based Short Drama application on Android.
## Prerequisites
Before you begin, ensure you have the following:

1. **Existing integration**: You have already integrated the [VideoOne Short Drama Solution](https://docs.byteplus.com/en/docs/byteplus-vos/short-drama-videos-android-integration).
2. **Google Play Services**: Your app must include Google Play Services to support the Google IMA SDK.
3. **Ad Tag URL**: You need a valid VAST/VMAP Ad Tag URL.
   * For testing: You can use [Google's Sample VAST Tags](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/tags).
   * For production: Obtain your Ad Tag URL from your ad server (e.g., Google Ad Manager).
4. **Minimum SDK Requirements**:
   * `minSdkVersion`: 24 or higher (Required for Google IMA).
   * `Android Gradle Plugin (AGP)`: 8.13.0 or higher (Required for Google IMA).

## Integrating the SDK
Follow these steps to integrate the MediaAds SDK into your Android project.

1. **Configure application properties**: Add your app's channel, ID, and name to the `gradle.properties` file in your project's root directory. These properties will be used by the SDK for initialization and analytics.
   ```Bash
   VOD_APP_CHANNEL=overseas
   VOD_APP_ID=522211
   VOD_APP_NAME=VideoOne
   
   # others ...
   ```

2. **Add dependency versions**: Open the `libs.versions.toml` file (Version Catalog) and define the versions for the MediaAds SDK and Google IMA SDK.
   ```Bash
   interactivemedia = "3.38.0"
   mediaads = "2.0.0"
   
   # others ...
   
   google-interactivemedia = { module = "com.google.ads.interactivemedia.v3:interactivemedia", version.ref = "interactivemedia" }
   byteplus-mediaads = { module = "com.byteplus.mediaplus:mediaads", version.ref = "mediaads" }
   
   # others ...
   ```

3. **Configure the library module**: In your library-level `build.gradle` file, import the SDK dependencies and inject the application properties into the `BuildConfig` class. This allows your code to access these values at runtime.
   ```Kotlin
   android {
       defaultConfig {
           // Read properties from the root project
           def VOD_APP_ID = project(':solutions:vod:vod-common').VOD_APP_ID
           def VOD_APP_NAME = project(':solutions:vod:vod-common').VOD_APP_NAME
           def VOD_APP_CHANNEL = project(':solutions:vod:vod-common').VOD_APP_CHANNEL
       
           // Inject properties into BuildConfig
           buildConfigField('String', 'VOD_APP_ID', "\"$VOD_APP_ID\"")
           buildConfigField('String', 'VOD_APP_NAME', "\"$VOD_APP_NAME\"")
           buildConfigField('String', 'VOD_APP_CHANNEL', "\"$VOD_APP_CHANNEL\"")
           
           // others ...
       }
       
       buildFeatures {
           buildConfig true
       }
   
       // others ...
   }
   
   dependencies {
       // BytePlus MediaAds SDK
       implementation libs.byteplus.mediaads
       
       // Google IMA SDK
       implementation libs.google.interactivemedia
   
       // others ...
   }
   ```

4. **Enable Java 8 desugaring**: The Google IMA SDK requires Java 8 language features. Enable core library desugaring in your app-level `build.gradle` file to ensure compatibility across different Android versions.
   ```Java
   android {
       compileOptions {
           isCoreLibraryDesugaringEnabled = true
       }
       
       // others ...
   }
   
   dependencies {
       coreLibraryDesugaring "com.android.tools:desugar_jdk_libs:2.1.5"
       
       // others ...
   }
   ```


## Quick start
### General guide
The MediaAds SDK is designed to be player agnostic. However, if no player is provided, it defaults to the embedded `TTVideoEngine`. To adapt to arbitrary players, an entity called `AdPlaybackSession` is provided to wrap both the content player and the ad player, and coordinate the placement of ads throughout the main content.
Follow these steps to create an `AdSchedule` and construct an `AdPlaybackSession`.

1. Create `AdAppInfo`: Use the `AdAppInfo.Builder` to create an `AdAppInfo` object, which contains application metadata required for logging and identification.
   ```Java
   AdAppInfo.Builder appInfoBuilder = new AdAppInfo.Builder();
   appInfoBuilder.setAppId(BuildConfig.VOD_APP_ID);      // Set the application ID
   appInfoBuilder.setAppName(BuildConfig.VOD_APP_NAME);  // Set the application name
   appInfoBuilder.setAppChannel(BuildConfig.VOD_APP_CHANNEL); // Set the application channel
   AdAppInfo appInfo = appInfoBuilder.build();
   ```

2. Create `AdConfig`: Use the `AdConfig.Builder` to create an `AdConfig` object, enabling features like preloading and pre-rendering to optimize ad playback performance.
   ```Java
   AdConfig.Builder adConfigBuilder = new AdConfig.Builder();
   adConfigBuilder.setAppInfo(appInfo);                  // Associate the app info
   adConfigBuilder.setEnablePreloadVast(true);           // Enable VAST preloading for faster ad response
   adConfigBuilder.setEnablePreloadAdMediaFile(true);    // Enable ad media file preloading to reduce buffering
   adConfigBuilder.setEnablePreRenderAdPlayer(true);     // Enable ad player pre-rendering for smoother transitions
   AdConfig adConfig = adConfigBuilder.build();
   ```

3. Create multi-VAST `AdBreakItem` list: Define the ad breaks for your content. In this example, we configure pre-roll, mid-roll, and post-roll ads using VAST tag URLs.
   ```Java
   List<AdBreakItem> adBreaks = new ArrayList<AdBreakItem>();
   
   // Pre-roll ad
   AdBreakItem.Builder preBrBuilder = new AdBreakItem.Builder();
   preBrBuilder.setVastAdTagUrl(AdSources.prerollUrl);   // Set the VAST ad tag URL
   preBrBuilder.setTimeOffset(AdBreakItem.TIME_OFFSET_START); // Set the offset to start
   adBreaks.add(preBrBuilder.build());
   
   // Mid-roll ad
   AdBreakItem.Builder mid1BrBuilder = new AdBreakItem.Builder();
   mid1BrBuilder.setVastAdTagUrl(AdSources.midrollUrl);
   mid1BrBuilder.setTimeOffset("00:00:10.500");          // Set the offset to 10.5 seconds
   adBreaks.add(mid1BrBuilder.build());
   
   // Post-roll ad
   AdBreakItem.Builder postBrBuilder = new AdBreakItem.Builder();
   postBrBuilder.setVastAdTagUrl(AdSources.postrollUrl);
   postBrBuilder.setTimeOffset(AdBreakItem.TIME_OFFSET_END); // Set the offset to end
   adBreaks.add(postBrBuilder.build());
   ```

4. Create `AdSchedule`: Combine the `AdConfig` and the list of `AdBreakItem`s into an `AdSchedule` object. This schedule defines the complete ad playback behavior for a session.
   ```Java
   AdSchedule.Builder adScheduleBuilder = new AdSchedule.Builder();
   adScheduleBuilder.setAdConfig(adConfig);
   adScheduleBuilder.setAdBreaks(adBreaks);
   AdSchedule adSchedule = adScheduleBuilder.build();
   ```

5. Create `AdPlaybackSession`: Finally, create an `AdPlaybackSession` using the `MediaAds` singleton. Bind it to your view, schedule, and content player to start managing playback.
   ```Java
   // Create a session instance
   AdPlaybackSession adPlaybackSession = MediaAds.instance().createAdSession(view.getContext());
   // Bind the ad container view where ads will be rendered
   adPlaybackSession.setAdViewContainer(view);
   // Set the ad schedule
   adPlaybackSession.setSchedule(adSchedule);
   // Bind the content player (e.g., videoEngine) to allow the SDK to control content playback
   adPlaybackSession.setContentPlayer(videoEngine);
   
   // Tip: You can programmatically skip the current ad using adPlaybackSession.skipAd() 
   // if the ad is skippable (check with adPlaybackSession.isSkippable()).
   ```


### Integrating with VideoOne
#### Modify the `PlaybackController`
The `PlaybackController` class manages the content player state and UI binding. To support ads while preserving VideoOne's existing playlist logic (e.g., auto-playing the next episode), we need to intercept playback events.
Instead of directly modifying the complex `PlaybackController`, we recommend creating a subclass `AdsPlaybackController`. To enable this inheritance, you must first refactor `PlaybackController` to expose key control points.
Refactoring goals:

* **Allow method overriding**: Change `bindPlayer` and `unbindPlayer` visibility to `protected`.
* **Intercept playback control**: Split `startPlayback` and `pausePlayback` to expose `startPlayer` and `pausePlayer` hooks. This allows the subclass to route these commands to the `AdPlaybackSession` instead of the player directly.
* **Prepare for event hijacking**: Add a `stopPlayer` hook. This is crucial for correctly handling the "completion" state—we must ensure the app doesn't think the episode is finished until both the content and the post-roll ad are done.

Apply the following adjustments to your `PlaybackController` class:

```Java
public class PlaybackController {
// Methods...

    // Swap private visibility with protected to enable overriding through inheritance
    protected private void bindPlayer(Player newPlayer) {
        // ...
    }

    // Swap private visibility with protected to enable overriding through inheritance
    protected private void unbindPlayer(boolean recycle) {
        // ...
    }

    // Split the player state switch statement from startPlayback to enable overriding
    // through inheritance
    private void startPlayback(boolean startWhenPrepared, @NonNull Player player, @NonNull VideoView videoView) {
        // Bind surface...
        
        startPlayer(startWhenPrepared, player, viewSource);
    }

    protected void startPlayer(boolean startWhenPrepared, @NonNull Player player, @NonNull MediaSource viewSource) {
        @Player.PlayerState final int playerState = player.getState();

        switch (playerState) {
            // Cases...
        }
    }
    
    // Split the player pause statement from pausePlayback to enable overriding
    // through inheritance
    public void pausePlayback() {
        Asserts.checkMainThread();

        final Player player = mPlayer;
        if (player != null) {
            L.d(this, "pausePlayback");

            if (player.isInPlaybackState()) {
                player.pause();
                pausePlayer();
            } else if (player.isPreparing()) {
                player.setStartWhenPrepared(false);
            }
        }
    }

    protected void pausePlayer() {
        mPlayer.pause();
    }

    // Add an empty stopPlayer method to enable overriding through inheritance
    public void stopPlayback() {
        Asserts.checkMainThread();

        final VideoView attachedView = mVideoView;
        final Player attachedPlayer = mPlayer;
        final MediaSource attachedSource = attachedSource(attachedView, attachedPlayer);

        if (attachedView != null) {
            attachedView.setReuseSurface(false);
        }

        if (mStartOnReadyCommand != null // startPlayback but surface not ready
                || attachedPlayer != null

        ) {
            L.d(this, "stopPlayback");

            mDispatcher.obtain(ActionStopPlayback.class, this).dispatch();

            stopPlayer();
            mStartOnReadyCommand = null;
            unbindPlayer(true);

            L.d(this, "stopPlayback", "end");
        }
    }

    protected void stopPlayer() {
    }
    
// Methods...
}
```

#### Implement the `AdsPlaybackController`
Create the `AdsPlaybackController` class inheriting from `PlaybackController`. This class overrides the player control methods to redirect them to the `AdPlaybackSession`.
Key responsibilities of this class:

1. **Intercept player events**: Replaces the original `PlayerListener` with a custom `AdsPlayerListener` to manage the interaction between content and ads.
2. **Delay completion**: Ensures the `PlayerEvent.State.COMPLETED` event is only dispatched when the entire session (content + ads) is finished, not just the content.
3. **Manage session lifecycle**: Creates, starts, pauses, and stops the `AdPlaybackSession`.
4. **Calculate mid-roll timestamps**: Delays session creation until the video is prepared and its duration is known, as Android currently requires absolute timestamps for mid-roll ads.

```Java
public class AdsPlaybackController extends PlaybackController {
    // AdsPlaybackController must at least hold an AdsPlayerListener and 
    // an AdPlaybackSession
    private final AdsPlayerListener mAdsPlayerListener = new AdsPlayerListener(this);
    private AdPlaybackSession mSession = null;

    // Methods...

    // Call the original bindPlayer and then immediately replace the original event
    // listener with the AdsPlayerListener
    protected void bindPlayer(Player newPlayer) {
        super.bindPlayer(newPlayer);
        if (super.player() != null && newPlayer != null && !newPlayer.isReleased()) {
            super.player().addPlayerListener(mAdsPlayerListener);
            super.player().removePlayerListener(super.mPlayerListener); // Remove base listener to take over events
        }
    }

    // Remove the AdsPlayerListener and delete the current AdPlaybackSession
    protected void unbindPlayer(boolean recycle) {
        if (super.player() != null) {
            super.player().removePlayerListener(mAdsPlayerListener);
        }
        super.unbindPlayer(recycle);
        mSession = null;
    }

    // Prepare the player or start the AdPlaybackSession if it already exists
    protected void startPlayer(boolean startWhenPrepared, @NonNull Player player, @NonNull MediaSource viewSource) {
        player.setStartWhenPrepared(false); // Disable auto-play, let SDK schedule it

        if (mSession == null) {
            player.prepare(viewSource); // Prepare player first if session not ready
        } else {
            mSession.play(); // Start session (plays ad or content)
        }
    }

    // Pause the AdPlaybackSession
    protected void pausePlayer() {
        if (mSession != null) {
            mSession.pause();
        }
    }

    // Stop the AdPlaybackSession
    protected void stopPlayer() {
        if (mSession != null) {
            mSession.stop();
        }
    }
    
    // Methods...
    
    private AdPlaybackSession createAdPlaybackSession(VideoView view, long duration) {
        // Create AdPlaybackSession logic here (omitted for brevity)...
        // ...
        
        session.addListener( (@NonNull AdPlaybackSession s, @NonNull AdPlayEvent e) -> {
                // Other logic...

                // Dispatch the original PlayerEvent.State.COMPLETED event only when the
                // AdPlaybackSession has completed (content + all ads)
                if (e.type == AdPlayEvent.Type.PLAY_COMPLETED) {
                    mDispatcher.obtain(StateCompleted.class, super.player()).dispatch();
                }
            });

        return session;
    }
    
    private static final class AdsPlayerListener implements EventListener {

        private final WeakReference<AdsPlaybackController> controllerRef;

        AdsPlayerListener(AdsPlaybackController controller) {
            controllerRef = new WeakReference<>(controller);
        }

        public void onEvent(Event event) {
            final AdsPlaybackController controller = controllerRef.get();

            // Create the AdPlaybackSession once the player is prepared
            if (controller != null) {
                if (event.code() == PlayerEvent.State.PREPARED) {
                    // Create session when content duration is known (for mid-rolls)
                    if (controller.mSession == null) {
                        long duration = controller.player().getDuration();
                        controller.mSession = controller.createAdPlaybackSession(controller.videoView(), duration);

                        VideoContentPlayer.Factory f = new VideoContentPlayer.Factory();
                        ContentPlayer cp = f.create(controller.player());
                        controller.mSession.setContentPlayer(cp);
                        controller.mSession.setSchedule(controller.creatAdSchedule(duration));
                    }
                    controller.mSession.play();
                }

                // Dispatch all events except for PlayerEvent.State.COMPLETED
                // COMPLETED is handled by the session listener above
                final Dispatcher dispatcher = controller.mDispatcher;
                if (dispatcher != null && event.code() != PlayerEvent.State.COMPLETED) {
                    dispatcher.dispatchEvent(event);
                }
            }
        }
    }
}
```

#### Implement the `VideoContentPlayer`
The `ContentPlayer` interface from the MediaAds SDK enables the `AdPlaybackSession` to control any underlying player. You need to implement an adapter class `VideoContentPlayer` that wraps your specific player (e.g., `TTVideoEngine`) and implements the `ContentPlayer` interface.
```Java
public class VideoContentPlayer implements ContentPlayer {
    // Member variables and other methods...

    public static class Factory implements ContentPlayer.Factory {

        @Override
        public ContentPlayer create(Object player) {
            // Create and return VideoContentPlayer...
        }
    }

    private static final class PlayerListener implements EventListener {
        @Override
        public void onEvent(Event event) {
            // Event callback logic...
        }
    }

    public VideoContentPlayer(Player player) {
        // Constructor...
    }

    @Override
    public void setListener(Listener listener) {
        // Set player event listener...
    }

    @Override
    public void play() {
        // Start player...
        // e.g., mRealPlayer.play();
    }

    @Override
    public void pause() {
        // Pause player...
        // e.g., mRealPlayer.pause();
    }

    @Override
    public void release() {
        // Release player...
    }

    @Override
    public long getCurrentPosition() {
        // Return current player position...
    }

    @Override
    public long getDuration() {
        // Return content duration...
    }

    @Override
    public boolean isCompleted() {
        // Return whether the player has finished...
    }

    @Override
    public boolean isLooping() {
        // Return whether the player is looping...
    }

    @Override
    public void recordLog(String log) {
        // Record logs...
    }
}
```

