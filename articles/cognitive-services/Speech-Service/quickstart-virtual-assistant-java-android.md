---
title: 快速入门：自定义语音优先虚拟助手（预览版），Java (Android) - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 SDK 在 Java on Android 中创建语音优先的虚拟助手应用程序
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: c62402faa1995e1e992c8251ed87160a8f33d3a7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602747"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>快速入门：使用语音 SDK 在 Java on Android 中创建语音优先的虚拟助手

我们还编写了一篇适用于[语音转文本](quickstart-java-android.md)服务的快速入门。

在本文中，你将使用[语音 SDK](speech-sdk.md) 在 Java for Android 中生成一个语音优先的虚拟助手。 此应用程序将连接到已使用 [Direct Line 语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)编写并配置的机器人。 然后，它将语音请求发送到该机器人，并提供支持语音的响应活动。

此应用程序是使用语音 SDK Maven 包和 Android Studio 3.3 生成的。 语音 SDK 目前与具有 32/64 位 ARM 和 Intel x86/x64 兼容处理器的 Android 设备兼容。

> [!NOTE]
> 对于语音设备 SDK 和 Roobo 设备，请参阅[语音设备 SDK](speech-devices-sdk.md)。

## <a name="prerequisites"></a>先决条件

* 语音服务的 Azure 订阅密钥。 [免费获取一个](get-started.md)或在 [Azure 门户](https://portal.azure.com)上创建它。
* 先前创建的并使用 [Direct Line 语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)配置的机器人
* [Android Studio](https://developer.android.com/studio/) v3.3 或更高版本

    > [!NOTE]
    > Direct Line 语音（预览版）目前在一部分语音服务区域中可用。 请参阅[语音优先虚拟助手支持的区域列表](regions.md#Voice-first virtual assistants)，并确保你的资源部署在其中一个区域中。

## <a name="create-and-configure-a-project"></a>创建并配置项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>创建用户界面

在本部分，我们将创建应用程序的基本用户界面 (UI)。 首先让我们打开主活动：`activity_main.xml`。 基本模板包含一个显示了应用程序名称的标题栏，以及一个显示了消息“Hello world!”的 `TextView`。

接下来，请将 `activity_main.xml` 的内容替换为以下代码：

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

此 XML 定义一个用来与机器人交互的简单 UI。

* 单击 `button` 元素后，它会启动交互，并调用 `onBotButtonClicked` 方法。
* `recoText` 元素将显示你与机器人之间的对话的语音转文本结果。
* `activityText` 元素将显示机器人中最新 Bot Framework 活动的 JSON 有效负载。

现在，UI 的文本和图形表示形式应如下所示：

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>添加示例代码

1. 打开 `MainActivity.java`，将内容替换为以下代码：

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only a subset of regions are currently supported)
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from the channel and speech subscription information
            DialogServiceConfig config = DialogServiceConfig.fromBotSecret(channelSecret, speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * `onCreate` 方法包含的代码可请求麦克风和 Internet 权限。

   * 如前所述，`onBotButtonClicked` 方法是按钮单击处理程序。 按下某个按钮会触发与机器人之间的单一交互（“轮次”）。

   * `registerEventListeners` 方法演示 `DialogServiceConnector` 使用的事件和传入活动的基本处理方式。

1. 在同一文件中，请替换配置字符串，使之与资源相匹配：

    * 将 `YourChannelSecret` 替换为机器人的 Direct Line 语音通道机密。

    * 将 `YourSpeechSubscriptionKey` 替换为订阅密钥。

    * 将 `YourServiceRegion` 替换为与你的订阅关联的[区域](regions.md)。目前，Direct Line 语音仅支持一部分语音服务区域。 有关详细信息，请参阅[区域](regions.md#voice-first-virtual-assistants)。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 将 Android 设备连接到开发电脑。 确保已在设备上启用[开发模式和 USB 调试](https://developer.android.com/studio/debug/dev-options)。

1. 若要生成应用程序，请按 Ctrl+F9，或者从菜单栏中选择“生成” > “生成项目”   。

1. 若要启动应用程序，请按 Shift+F10 或选择“运行” > “运行‘应用’”   。

1. 在出现的部署目标窗口中，选择 Android 设备。

   ![“选择部署目标”窗口的屏幕截图](media/sdk/qs-java-android-12-deploy.png)

启动应用程序及其活动后，请单击该按钮开始与机器人对话。 当你讲话时，会显示听录的文本，并显示从机器人收到的最新活动。 如果机器人配置为提供语音响应，则会自动播放语音转文本结果。

![Android 应用程序的屏幕截图](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建和部署基本机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>另请参阅
- [关于语音优先虚拟助手](voice-first-virtual-assistants.md)
- [免费获取语音服务订阅密钥](get-started.md)
- [自定义唤醒词](speech-devices-sdk-create-kws.md)
- [将 Direct Line 语音连接到机器人](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [浏览 GitHub 上的 Java 示例](https://aka.ms/csspeech/samples)
