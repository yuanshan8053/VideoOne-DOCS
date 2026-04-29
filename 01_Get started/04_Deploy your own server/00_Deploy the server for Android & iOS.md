In order to make it easier for you to compile VideoOne Android & iOS sample code, BytePlus provides you with an application server that can be used in the testing phase. 
Testing App server domain: `https://videocloud.byteplusapi.com/videoone_opensource`
Please note that this App server should only be used during the testing phase. According to your current business needs, you can refer to this document to modify and deploy the open source code on your server.

This article provides a step-by-step guide on deploying the App server of the VideoOne sample project. By following these instructions, you will be able to successfully deploy the project and obtain a dedicated server for your own VideoOne app.
The App server offers a range of services, including:

* managing live rooms, such as creating and ending a live room session.
* managing the media files.
* real-time signaling, such as sending notifications to the host when an audience member applies for co-hosting.

## Prerequisites
Install the required components:

* [Go](https://go.dev/doc/tutorial/getting-started) 1.18 or higher
* [MySQL](https://dev.mysql.com/doc/mysql-getting-started/en/) 5.7 or higher
* [Redis](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/) 6.2 or higher

## Procedures
### Project setup

1. Go to GitHub and clone the [VideoOneSolutions](https://github.com/byteplus-sdk/VideoOneSolutions) repository.
2. Within the project folder, navigate to the `/Server/sql/` directory and execute `videoone.sql` to create a MySQL database.
3. Navigate to the `/Server/conf` directory, open the `config.yaml` file, and configure the settings. Not all parameters in the sample code are mandatory. Please refer to the table below the code for specific usage scenarios of each parameter.
   ```YAML
   # mysql/redis config
   mysql_dsn: "{user_name}:{password}@tcp([{mysql_address}]:{port})/videoone?parseTime=true&loc=Local"
   redis_init: true
   redis_addr: "127.0.0.1:6379"
   redis_password: ""
   
   # common service config
   port: "8080"
   access_key: ""
   secret_access_key: ""
   
   # rtc service config
   rtc_app_id: ""
   rtc_app_key: ""
   
   # live service config
   live_app_name: ""
   live_pull_domain: ""
   live_push_domain: ""
   live_stream_key: ""
   
   # vod service config
   vod_space: ""
   vod_play_list_id: "{your_vod_play_list_id}"
   
   # Interactive Live scene config
   live_timer_enable: true
   live_experience_time: 20
   ```

   | **Parameter** | **Data type** | **Description** | **Parameter-dependent scene/feature** | **Example** |
   | --- | --- | --- | --- | --- |
   | mysql_dsn | String | The DSN of your MySQL server, where: <br>  <br> * `user_name` is the username of your MySQL account. <br> * `password` is the password of your MySQL account. <br> * `mysql_address` is the IP address of your MySQL server. <br> * `port` is the port number used by MySQL. | All | user1:0EFF9BF*******2240CA35@tcp(xxx.xxx.xxx.xxx:3306)/videoone?parseTime=true&loc=Local |
   | redis_init | Boolean | Specifies whether to connect to Redis. | All | Set this to `true` if your application uses the interactive live feature. |
   | redis_addr | String | The IP address and port number of your Redis server. | All |  |
   | redis_password | String | The password for your Redis service. | All | 0EFF9BF*******2240CA35 |
   | port | String | The port number used by this app service. In most cases, you can set it to `8080`. | All | 8080 |
   | access_key | String | [The access key ID (AK) of your BytePlus account](https://docs.byteplus.com/en/byteplus-platform/docs/creating-an-accesskey) | All | AKAPZ7********FLBFK4k9 |
   | secret_access_key | String | [The secret access key (SK) of your BytePlus account](https://docs.byteplus.com/en/byteplus-platform/docs/creating-an-accesskey) | All | 8dk39vK********k7D93K37WODKu3oQ |
   | rtc_app_id | String | The **AppId** of your BytePlus RTC app. | Interactive Live |  |
   | rtc_app_key | String | The **AppKey** of your BytePlus RTC app. | Interactive Live |  |
   | live_app_name | String | The **AppName** in MediaLive for which you have configured a transcoding template. | Interactive Live | videoone |
   | live_pull_domain | String | http://{domain_name}, where "domain_name" represents the **domain name for stream pulling**. | Interactive Live | `http://pull-demo.com` |
   | live_push_domain | String | rtmp://{domain_name}, where "domain_name" represents the **domain name for stream pushing**. | Interactive Live | `rtmp://push-demo.com` |
   | live_stream_key | String | The **Primary key** for URL authentication. | Interactive Live | DLH********KDF |
   | live_timer_enable | Boolean | Specifies whether to automatically end the session after the duration set in the corresponding `live_experience_time` parameter. <br>  <br> * true: Enable automatic ending <br> * false: Disable automatic ending | Interactive Live | true |
   | live_experience_time | Integer | The duration (in minutes) before a session automatically ends. This parameter applies only when the corresponding `live_timer_enable` parameter is set to true. | Interactive Live | 20 |
   | vod_space | String | The name of the BytePlus VOD space where you upload your media files. | For video playback features | videoone |
   | vod_play_list_id | String | ID of the video playlist. <br> This parameter is only required if you need the video playlist function. You should call [CreatePlaylist](https://docs.byteplus.com/en/byteplus-vod/reference/createplaylist?version=v1.0) first to create a video playlist, after which you can retrieve the ID from the result parameter in the response. | Video playlist | pb2d0****** |

### Preparing media files
This task is necessary only if you require the video playback feature.


1. Follow the steps below to prepare some media files. You can refer to [Getting started with BytePlus VOD](https://docs.byteplus.com/en/byteplus-vod/docs/getting-started?version=v1.0) for more detailed instructions.
   1. Create a VOD space.
   2. Upload some video files to the VOD space. Select **Multi-bitrate template for general online videos** as the workflow template.
   3. Add a domain name.
   4. Publish the video files.
2. Enter the information about the media files you have uploaded to BytePlus VOD.
   ```SQL
   INSERT INTO
     video_info(vid, video_type, anti_screenshot_and_record, support_smart_subtitle)
   VALUES
     ('{vid-1}', 0, 0, 0),
     ('{vid-2}', 1, 0, 1);
   ```

   | **Parameter** | **Data type** | **Description** | **Example** |
   | --- | --- | --- | --- |
   | vid | String | The video ID. | v110exxdg |
   | video_type | Integer | The video type. Select the type based on the video's length. <br>  <br> * `0`: Short video. Videos of this category will be displayed and played on the **Home** tab within the app. <br> * `1`: Medium video. Videos of this category will be displayed and played on the **Feed** tab within the app. <br> * `2`: Long video. Videos of this category will be displayed and played on the **Channel** tab within the app. | 0 |
   | anti_screenshot_and_record | Integer | Specifies whether to enable screen recording protection. <br>  <br> * `0`: Disable <br> * `1`: Enable | 0 |
   | support_smart_subtitle | Integer | Specifies whether to enable the smart subtitling feature. <br>  <br> * `0`: Disable <br> * `1`: Enable | 0 |

### Deploying the project
Under the root directory, run the following command to compile and deploy the project:
```Shell
sh startserver.sh
```

## Result
Call the `ping` interface using the following command:
```Shell
curl --location 'http://{your_server_address}:{port_number}/videoone_opensource/ping'
```

The following response indicates that the service is up and running:
```Plain Text
{"message":"pong"} 
```

## Checking logs
To access the service logs, navigate to the `/Server/output/log/app` directory and find the logs in `app.log`. Here is an example of a log entry:
```Plain Text
time="2021-12-31T15:35:14+08:00" level=info msg="get login userID: 123" Location="user.go:49" LogID=75119c42-3a98-4533-a3f7-d2b8468c03f6
```

You can use `LogID` to identify a client request.

