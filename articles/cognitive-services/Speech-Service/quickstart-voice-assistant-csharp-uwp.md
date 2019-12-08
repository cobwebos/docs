---
title: 快速入门：自定义语音助手，C# (UWP) - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本文中，你将使用认知服务语音软件开发工具包 (SDK) 创建一个 C# 通用 Windows 平台 (UWP) 应用程序。 你要将客户端应用程序连接到先前创建的、配置为使用 Direct Line 语音通道的 Bot Framework 机器人。 该应用程序是使用语音 SDK NuGet 包和 Microsoft Visual Studio 2019 构建的。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 25d74b640f1bf567792c317626da0178ead7e34f
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816225"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-uwp"></a>快速入门：使用语音 SDK、UWP 创建语音助手

还为[语音识别](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)、[语音合成](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp)和[语音翻译](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)提供了快速入门。

在本文中，将使用[语音 SDK](speech-sdk.md) 开发一个 C# 通用 Windows 平台 (UWP) 应用程序。 该程序将连接到先前创作并配置的机器人，从客户端应用程序启用语音助手体验。 该应用程序是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2019（任何版本）构建的。

> [!NOTE]
> 通用 Windows 平台允许开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。
* 语音服务的 Azure 订阅密钥。 [免费获取一个](get-started.md)或在 [Azure 门户](https://portal.azure.com)上创建它。
* 先前创建并使用 [Direct Line 语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)配置的机器人。

  > [!NOTE]
  > 请参阅[语音助手支持的区域列表](regions.md#voice-assistants)，确保你的资源部署在其中一个区域中。

## <a name="optional-get-started-fast"></a>可选：快速入门

本快速入门会逐步介绍如何将一个客户端应用程序连接到支持语音的机器人。 [语音 SDK 示例](https://aka.ms/csspeech/samples)中的 `quickstart` 文件夹下提供了本快速入门使用的完整可编译源代码，供你直接学习。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>添加示例代码

现在，添加定义应用程序用户界面的 XAML 代码，并添加 C# 代码隐藏实现。

### <a name="xaml-code"></a>XAML 代码

首先，通过添加 XAML 代码创建应用程序的用户界面：

1. 在“解决方案资源管理器”  中打开 `MainPage.xaml`。

1. 在设计器的 XAML 视图中，将整个内容替换为以下代码片段：

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

更新“设计”视图以显示应用程序的用户界面。

### <a name="c-code-behind-source"></a>C# 代码隐藏源

然后，添加代码隐藏源，以便应用程序按预期工作。 代码隐藏源包括：

- `using` 和 `Speech` 命名空间的 `Speech.Dialog` 语句
- 一个绑定到按钮处理程序的简单实现，用于确保麦克风访问
- 基本的 UI 帮助程序，用于在应用程序中提供消息和错误
- 初始化代码路径的登陆点，稍后将填充
- 用于播放文本转语音的帮助程序（没有流式处理支持）
- 一个用于启动侦听的空的按钮处理程序，稍后将填充

若要添加代码隐藏源，请执行以下步骤：

1. 在“解决方案资源管理器”  中，打开代码隐藏源文件 `MainPage.xaml.cs`。 （其分组在 `MainPage.xaml` 下。）

1. 将该文件的内容替换为以下代码片段：

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. 将以下代码片段添加到 `InitializeDialogServiceConnector` 的方法主体。 此代码会使用你的订阅信息创建 `DialogServiceConnector`。

    ```csharp
    // Create a BotFrameworkConfig by providing a Speech service subscription key
    // the RecoLanguage property is optional (default en-US)
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region.

    var botConfig = BotFrameworkConfig.FromSubscription(speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > 请参阅[语音助手支持的区域列表](regions.md#voice-assistants)，确保你的资源部署在其中一个区域中。

   > [!NOTE]
   > 有关配置机器人和检索通道机密的信息，请参阅 [Direct Line 语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)的 Bot Framework 文档。

1. 将字符串 `YourChannelSecret`、`YourSpeechSubscriptionKey` 和 `YourServiceRegion` 分别替换为自己的机器人、语音订阅和[区域](regions.md)值。

1. 将以下代码片段追加到 `InitializeDialogServiceConnector` 的方法主体的末尾。 此代码为 `DialogServiceConnector` 依赖的事件设置处理程序，以传达其机器人活动、语音识别结果和其他信息。

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. 将以下代码片段添加到 `MainPage` 类中 `ListenButton_ButtonClicked` 方法的正文。 此代码将 `DialogServiceConnector` 设置为侦听，因为已建立配置并注册了事件处理程序。

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. 在菜单栏中，选择“文件”   > “全部保存”  以保存所做的更改。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

现已准备好构建并测试应用程序。

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动应用程序。 此时将显示“helloworld”  窗口。

   ![C# 中的示例 UWP 语音助手应用程序 - 快速入门](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. 选择“启用麦克风”  ，并在弹出访问权限请求时选择“是”  。

   ![麦克风访问权限请求](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. 选择“对机器人讲话”，然后对着设备的麦克风讲出一个英文短语或句子  。 你的语音将传输到 Direct Line 语音通道并转录为文本，该文本会显示在窗口中。
<!--
    ![Successful bot response](media/voice-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建和部署基本机器人](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>另请参阅

- [关于语音助手](voice-assistants.md)
- [免费获取语音服务订阅密钥](get-started.md)
- [自定义关键字](speech-devices-sdk-create-kws.md)
- [将 Direct Line 语音连接到机器人](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)
