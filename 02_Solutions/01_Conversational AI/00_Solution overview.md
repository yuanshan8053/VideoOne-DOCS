The BytePlus Video One conversational AI solution allows users to engage in scenario-based, real-time conversations that go beyond the limitations of text. Users can have natural and smooth conversations with AI just as they would with another person. The solution can be used in scenarios such as smart AI assistants, AI customer service, AI smart companions, AI language coaches, AI gaming NPC, and smart hardware.
| English demo | Japanese demo |
| --- | --- |
| <video src=https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/3f0ad882c50f48a3b2ce734466ee75de~tplv-goo7wpa0wc-image.image></video> | <video src=https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/4bdec5a52f3c46ba977ee276b5b1c8cc~tplv-goo7wpa0wc-image.image></video> |
# Technical architecture
The conversational AI solution uses BytePlus RTC for efficient acquisition of audio and video data, customized processing, and ultra-low latency transmission. The cloud-based smart audiovisual processing module uses 3A algorithms, AI noise reduction, and other capabilities to mitigate the impact of background noise and device performance on conversations. In addition, the solution integrates ASR (Automatic Speech Recognition), TTS (Text to Speech), LLM (Large Language Model), knowledge base RAG (Retrieval-Augmented Generation) and other services that simplify the speech-to-text and text-to-speech conversion processes. Its advanced capabilities in conversational intelligence, natural language processing, and multimodal interaction allow app users to interact in real time with the cloud-based large model.
![Image](https://p9-arcosite.byteimg.com/tos-cn-i-goo7wpa0wc/d9cba1e76c9a4ad78c88386bfca9b023~tplv-goo7wpa0wc-image.image)
# Features
## **Basic features**
| **Feature** | **Description** |
| --- | --- |
| Real-time AI conversation | Users can have natural and smooth conversations with AI in real time, just as they would with another person. Users can also interrupt at any time for two-way interaction. |
| Noise suppression | Using advanced 3A audio technology and AI noise suppression, the solution effectively removes background noise while preserving clear human speech. This provides a balance between strong noise suppression and high fidelity in noisy environments. |
## Advanced features
| **Feature** | **Description** |
| --- | --- |
| Real-time subtitles | The conversation is converted to text and displayed on the device screen in real time. |
| Filtering of specified content | During conversations, the system automatically filters out non-essential details, such as end-of-conversation markers and descriptions of actions. These details are not vocalized by the TTS engine; they appear only in subtitles to provide additional context and do not affect the conversation. |
| Function calling | The large model can identify particular requirements from the user's speech and call external functions or APIs to perform tasks that it is unable to complete independently. Examples include real-time data retrieval, file processing, and database searches. This expands the AI agent's capabilities, allowing it to provide accurate answers on topics like weather forecasts, stock prices, and math calculations. |
| Integration with self-developed or third-party large models | Self-developed or third-party large models can be integrated into the conversational AI workflow to meet specific needs. |
| Real-time interaction | When users interact with the AI agent, in addition to speech, they can also provide visual input to help the AI agent understand their surroundings and actions (video interaction). |
# Advantages

* **Natural conversation that can be interrupted at any time**
   * **Smart interruption**: The solution supports full-duplex communication and VAD (Voice Activity Detection) at the audio frame level. Users can interrupt at any time, making the conversation more natural.
   * On-device noise suppression: The RTC SDK is used for noise reduction in complex environments, which effectively reduces background noise, including music, and improves the recognition accuracy of users' speech.
* **Real-time response and smooth conversation**
   * **Ultra-low latency**: The solution is based on end-to-end stream processing. The overall RTC+ASR+LLM+TTS latency is as low as 1 second.
   * **Optimal performance in poor connectivity scenarios**: The solution uses smart connections and cloud-coordinated RTC optimizations to ensure low-latency, reliable transmission even in poor connectivity scenarios. This prevents the large model from misinterpreting speech due to dropped words.
* **Flexible scalability**
   * **Multi-user interaction**: One-on-one interaction can be upgraded to enable conversation with multiple users simultaneously.
   * **Video interaction**: The solution can be upgraded from audio-only conversations to real-time audiovisual AI interactions.
* One-stop integration: Simply call the provided API endpoints to configure the ASR, LLM, and TTS services and implement your real-time AI interaction apps.
* Cross-platform compatibility: The solution supports a wide range of platforms, including iOS, Android, Windows, Linux, macOS, Web, and Flutter.
* **Multi-language support**: The solution supports real-time conversations in multiple languages, including Chinese, English, Japanese, and Spanish.


