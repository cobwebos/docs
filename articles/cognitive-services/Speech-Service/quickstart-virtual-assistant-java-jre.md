---
title: 快速入门：自定义语音优先虚拟助手（预览版），Java（Windows、Linux）- 语音服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何在 Java 控制台应用程序中使用认知服务语音软件开发工具包 (SDK)。 其中将会介绍将客户端应用程序连接到事先创建的某个 Bot Framework 机器人。该机器人配置为使用 Direct Line 语音通道，可以实现语音优先虚拟助手体验。
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025363"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>快速入门：使用语音 SDK 创建语音优先虚拟助手 (Java)

在本文中，你将使用[认知服务语音 SDK](speech-sdk.md) 创建一个 Java 控制台应用程序。 该应用程序将连接到事先创作的某个机器人。该机器人配置为使用 Direct Line 语音通道，发送语音请求，并返回语音响应活动（如果已配置）。 该应用程序是使用语音 SDK Maven 包和 Eclipse Java IDE 在 Windows、Ubuntu Linux 或 macOS 上生成的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* 操作系统：Windows（64 位）、Ubuntu Linux 16.04/18.04（64 位）或 MacOS 10.13 或更高版本
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。
* 使用 Bot Framework 4.2 或更高版本创建的且预先配置的机器人。 该机器人需要订阅新的“Direct Line 语音”通道，以接收语音输入。

    > [!NOTE]
    > 在预览版中，Direct Line 语音通道目前仅支持 **westus2** 区域。

    > [!NOTE]
    > [免费试用语音服务](get-started.md)中所述的适用于标准定价层的 30 天试用版仅限 **westus**（不是 **westus2**）区域，因此与 Direct Line 语音不兼容。 免费和标准层 **westus2** 订阅与此兼容。

如果运行 Ubuntu 16.04/18.04，请确保在启动 Eclipse 之前安装这些依赖项：

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

如果运行 Windows（64 位），请确保已安装适用于平台的 Microsoft Visual C++ Redistributable：
* [下载 Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>可选：快速入门

本快速入门会逐步介绍如何将一个简单的客户端应用程序连接到支持语音的机器人。 [语音 SDK 示例](https://aka.ms/csspeech/samples)中的 `quickstart` 文件夹下提供了本快速入门使用的完整可编译源代码，供你直接学习。

## <a name="create-and-configure-project"></a>创建并配置项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

此外，若要启用日志记录，请更新 **pom.xml** 文件以包含以下依赖项。

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>添加示例代码

1. 若要向 Java 项目添加新的空类，请选择“文件” > “新建” > “类”。

1. 在“新建 Java 类”窗口中，在“包”字段内输入 **speechsdk.quickstart**，在“名称”字段内输入 **Main**。

   ![“新建 Java 类”窗口的屏幕截图](media/sdk/qs-java-jre-06-create-main-java.png)

1. 打开新建的 **Main** 类，将 `Main.java` 文件的内容替换为以下起始代码。

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. 在 **main** 方法中，首先配置 `BotConnectorConfig` 并使用它来创建 `SpeechBotConnector` 实例。 这会连接到 Direct Line 语音通道以便与机器人交互。 `AudioConfig` 实例还用于指定音频输入的源。 在此示例中，对 `AudioConfig.fromDefaultMicrophoneInput()` 使用了默认麦克风。

    * 将字符串 `YourSubscriptionKey` 替换为可从[此处](get-started.md)获取的订阅密钥。
    * 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)。
    * 将字符串 `YourChannelSecret` 替换为 Direct Line 语音通道机密。

    > [!NOTE]
    > 在预览版中，Direct Line 语音通道目前仅支持 **westus2** 区域。

    > [!NOTE]
    > [免费试用语音服务](get-started.md)中所述的适用于标准定价层的 30 天试用版仅限 **westus**（不是 **westus2**）区域，因此与 Direct Line 语音不兼容。 免费和标准层 **westus2** 订阅与此兼容。

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` 依赖于多个事件来传达其机器人活动、语音识别结果和其他信息。 接下来请添加这些事件侦听器。

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. 调用 `connectAsync()` 方法将 `SpeechBotConnector` 连接到 Direct Line 语音。 若要测试机器人，可以调用 `listenOnceAsync` 方法以从麦克风发送音频输入。 此外，还可以使用 `sendActivityAsync` 方法以序列化字符串的形式发送自定义活动。 这些自定义活动可以提供机器人在聊天中使用的其他数据。

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. 保存对 `Main` 文件的更改。

1. 为了支持响应播放，请额外添加一个类并在其中包含用于支持音频的实用工具方法。 若要启用音频，请将另一个新空类添加到 Java 项目，选择“文件” > “新建” > “类”。

1. 在“新建 Java 类”窗口上的“包”字段中输入 **speechsdk.quickstart**，在“名称”字段中输入 **AudioPlayer**。

   ![“新建 Java 类”窗口的屏幕截图](media/sdk/qs-java-jre-06-create-main-java.png)

1. 打开新建的 **AudioPlayer** 类并替换为下面提供的代码。

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. 保存对 `AudioPlayer` 文件的更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

按 F11，或选择“运行” > “调试”。
控制台会显示消息“请讲话”。此时，请讲出机器人理解的英语短语或句子。 你的语音将通过 Direct Line 语音通道传送到机器人，机器人将识别并处理你的语言，然后返回活动形式的响应。 如果机器人返回了语音作为响应，则会使用 `AudioPlayer` 类播放音频。

![成功识别后的控制台输出的屏幕截图](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>后续步骤

GitHub 上提供了其他示例，例如如何从音频文件中读取语音。

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Java 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [快速入门：翻译语音，Java（Windows、Linux）](quickstart-translate-speech-java-jre.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
