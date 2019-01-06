---
title: 快速入门：翻译语音，C# (UWP) - 语音服务
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将创建一个简单的通用 Windows 平台 (UWP) 应用程序来捕获用户语音，将其翻译为另一种语言，并将文本输出到命令行。 本指南适用于 Windows 用户。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: ca1c26d62d3047ecdd8a29bc97457e7e2baef9a5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729520"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>快速入门：使用适用于 C# 的语音 SDK 翻译语音 (UWP)

在本快速入门中，你将创建一个简单的通用 Windows 平台 (UWP) 应用程序，该应用程序从计算机的麦克风中捕获用户语音，翻译语音，并将翻译后的文本实时转录到命令行。 此应用程序设计为在 64 位 Windows 上运行，它是使用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)和 Microsoft Visual Studio 2017 生成的。

有关可用于语音翻译的语言的完整列表，请参阅[语言支持](language-support.md)。

> [!NOTE]
> 使用 UWP 可以开发在支持 Windows 10 的任何设备上运行的应用，包括电脑、Xbox、Surface Hub 和其他设备。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 语音服务的 Azure 订阅密钥。 [免费获取](get-started.md)。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 应用程序的用户界面使用 XAML 进行定义。 在解决方案资源管理器中打开 `MainPage.xaml`。 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到 `<Grid>` 和 `</Grid>` 之间。

    ```xaml
    <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
        <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" Height="35"/>
        <Button x:Name="SpeechRecognitionButton" Content="Translate speech from the microphone input" Margin="0,10,10,0" Click="SpeechTranslationFromMicrophone_ButtonClicked" Height="35"/>
        <StackPanel x:Name="StatusPanel" Orientation="Vertical" RelativePanel.AlignBottomWithPanel="True" RelativePanel.AlignRightWithPanel="True" RelativePanel.AlignLeftWithPanel="True">
            <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" TextWrapping="Wrap" Text="Status:" FontSize="20"/>
            <Border x:Name="StatusBorder" Margin="0,0,0,0">
                <ScrollViewer VerticalScrollMode="Auto"  VerticalScrollBarVisibility="Auto" MaxHeight="200">
                    <!-- Use LiveSetting to enable screen readers to announce the status update. -->
                    <TextBlock x:Name="StatusBlock" FontWeight="Bold" AutomationProperties.LiveSetting="Assertive"
            MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" Margin="10,10,10,20" TextWrapping="Wrap"  />
                </ScrollViewer>
            </Border>
        </StackPanel>
    </StackPanel>
    ```

1. 打开代码隐藏源文件 `MainPage.xaml.cs`（可以发现它归在 `MainPage.xaml` 下）。 将其中的所有代码替换为以下内容。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Translation;

    namespace helloworld
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private async void SpeechTranslationFromMicrophone_ButtonClicked(object sender, RoutedEventArgs e)
            {
                // Creates an instance of a speech config with specified subscription key and service region.
                // Replace with your own subscription key and service region (e.g., "westus").
                var config = SpeechTranslationConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

                // Sets source and target languages.
                string fromLanguage = "en-US";
                config.SpeechRecognitionLanguage = fromLanguage;
                config.AddTargetLanguage("de");

                try
                {
                    // Creates a speech recognizer using microphone as audio input.
                    using (var recognizer = new TranslationRecognizer(config))
                    {
                        // The TaskCompletionSource to stop recognition.
                        var stopRecognition = new TaskCompletionSource<int>();

                        // Subscribes to events.
                        recognizer.Recognizing += (s, ee) =>
                        {
                            NotifyUser($"RECOGNIZING in '{fromLanguage}': Text={ee.Result.Text}", NotifyType.StatusMessage);
                            foreach (var element in ee.Result.Translations)
                            {
                                NotifyUser($"    TRANSLATING into '{element.Key}': {element.Value}", NotifyType.StatusMessage);
                            }
                        };

                        recognizer.Recognized += (s, ee) =>
                        {
                            if (ee.Result.Reason == ResultReason.TranslatedSpeech)
                            {
                                NotifyUser($"\nFinal result: Reason: {ee.Result.Reason.ToString()}, recognized text in {fromLanguage}: {ee.Result.Text}.", NotifyType.StatusMessage);
                                foreach (var element in ee.Result.Translations)
                                {
                                    NotifyUser($"    TRANSLATING into '{element.Key}': {element.Value}", NotifyType.StatusMessage);
                                }
                            }
                        };

                        recognizer.Canceled += (s, ee) =>
                        {
                            NotifyUser($"\nRecognition canceled. Reason: {ee.Reason}; ErrorDetails: {ee.ErrorDetails}", NotifyType.StatusMessage);
                        };

                        recognizer.SessionStarted += (s, ee) =>
                        {
                            NotifyUser("\nSession started event.", NotifyType.StatusMessage);
                        };

                        recognizer.SessionStopped += (s, ee) =>
                        {
                            NotifyUser("\nSession stopped event.", NotifyType.StatusMessage);
                            stopRecognition.TrySetResult(0);
                        };

                        // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                        await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                        // Waits for completion.
                        // Use Task.WaitAny to keep the task rooted.
                        Task.WaitAny(new[] { stopRecognition.Task });

                        // Stops continuous recognition.
                        await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                    }
                }
                catch (Exception ex)
                {
                    NotifyUser($"{ex.ToString()}", NotifyType.ErrorMessage);
                }
            }

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            private void NotifyUser(string strMessage, NotifyType type)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) ? strMessage : "\n" + strMessage;

                // Collapse the StatusBlock if it has no text to conserve real estate.
                StatusBorder.Visibility = !string.IsNullOrEmpty(StatusBlock.Text) ? Visibility.Visible : Visibility.Collapsed;
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
                // Raise an event if necessary to enable a screen reader to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }
        }
    }
    ```

1. 在此文件的 `SpeechTranslationFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourSubscriptionKey` 替换为订阅密钥。

1. 在 `SpeechTranslationFromMicrophone_ButtonClicked` 处理程序中，将字符串 `YourServiceRegion` 替换为与订阅（例如，`westus` 免费试用版订阅）相关联的[区域](regions.md)。

1. 保存对项目的所有更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 构建应用程序。 从菜单栏中，选择“构建” > “构建解决方案”。 现在，编译代码时应不会提示错误。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-csharp-uwp-08-build.png "成功生成")

1. 启动应用程序。 在菜单栏中，选择“调试” > “开始调试”，或按 F5。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-csharp-uwp-09-start-debugging.png "启动应用进入调试")

1. 弹出一个窗口。 选择“启用麦克风”，然后确认弹出的权限请求。

    ![权限请求的屏幕截图](media/sdk/qs-csharp-uwp-10-access-prompt.png "启动应用进入调试")

1. 选择“使用麦克风输入进行语音识别”，然后对着设备的麦克风说一个英文短语或句子。 你的语音将传输到语音服务并转录为文本，该文本将显示在窗口中。

    ![语音识别用户界面的屏幕截图](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
