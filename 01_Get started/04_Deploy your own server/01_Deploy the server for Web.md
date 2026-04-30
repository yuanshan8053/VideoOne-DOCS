Deploying the app server is optional. If you prefer not to deploy the app server yourself, you can utilize the app server provided by BytePlus as an alternative.

This article provides a step-by-step guide on deploying the backend of the VideoOne sample project. By following these instructions, you will be able to successfully deploy the project and obtain a dedicated server for your own VideoOne app.
## Prerequisites
Install the required components:

* Go 1.18 or higher
* MySQL 5.7 or higher
* Redis 6.2 or higher

## Procedures
### Project setup

1. Contact the technical support team to obtain the Demo project.
2. Within the project folder, navigate to the `/Server/sql/` directory and execute `videoone.sql` to create a MySQL database.
3. Navigate to the `/Server/conf` directory, open the `config.yaml` file, and configure the settings.

```YAML
mysql_dsn: "{user_name}:{password}@tcp([{mysql_address}]:{port})/videoone?parseTime=true&loc=Local"
redis_init: false
port: "{port_number}"
vod_space: "{vod_space}"
vod_play_list_id: "{vod_play_list_id}"
```

| **Parameter** | **Data type** | **Description** | **Example** |
| --- | --- | --- | --- |
| mysql_dsn | String | The DSN of your MySQL server, where: <br>  <br> * `user_name` is the username of your MySQL account. <br> * `password` is the password of your MySQL account. <br> * `mysql_address` is the IP address of your MySQL server. <br> * `port` is the port number used by MySQL. | user1:0EFF9BF*******2240CA35@tcp(192.0.2.1:3306)/videoone?parseTime=true&loc=Local |
| redis_init | Boolean | Whether to connect to Redis. | This parameter must be set to `false`. |
| port | String | The port number used by this app service. In most cases, you can set it to `8080`. | 8080 |
| vod_space | String | The name of the BytePlus VOD space where you upload your media files. | videoone |
| vod_play_list_id | String | ID of the video playlist. <br> This parameter is only required if you need the video playlist function. You should call [CreatePlaylist](https://docs.byteplus.com/en/byteplus-vod/reference/createplaylist?version=v1.0) first to create a video playlist. The ID is returned in the `result` parameter of the response. | pb2d0****** |

### Preparing media files

1. Follow the steps below to prepare some media files. You can refer to [Getting started with BytePlus VOD ](https://docs.byteplus.com/en/byteplus-vod/docs/getting-started?version=v1.0)for more detailed instructions.
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
| video_type | Integer | The video type. You can select the type according to the length of the video. <br>  <br> * `0` : Short video. Videos of this category will be displayed and played on the **Home** tab within the app. <br> * `1` : Medium video. Videos of this category will be displayed and played on the **Feed** tab within the app. <br> * `2` : Long video. Videos of this category will be displayed and played on the **Channel** tab within the app. | 0 |
| anti_screenshot_and_record | Integer | Whether to enable screen recording protection. <br>  <br> * `0` : Disable <br> * `1` : Enable | 0 |
| support_smart_subtitle | Integer | Whether to enable the smart subtitling feature. <br>  <br> * `0` : Disable <br> * `1` : Enable | 0 |

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
