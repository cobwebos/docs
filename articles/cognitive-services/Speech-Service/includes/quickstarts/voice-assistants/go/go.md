---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 9e41ed6bdc31ce70b2744fa9a87e10c51aaac15a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673197"
---
## <a name="prerequisites"></a>先决条件

准备工作：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境并创建空项目](../../../../quickstarts/setup-platform.md)
> * 创建连接到 [Direct Line 语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)的机器人
> * 请确保你有权访问麦克风，以便进行音频捕获
>
  > [!NOTE]
  > 请参阅[语音助手支持的区域列表](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)，确保你的资源部署在其中一个区域中。

## <a name="setup-your-environment"></a>设置环境

通过添加以下行，使用最新的 SDK 版本更新 go.mod 文件
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.12.0
)
```

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手
将源文件（例如 `quickstart.go`）的内容替换为以下内容，其中包括：

- “主要”包定义
- 从语音 SDK 导入所需的模块
- 用于存储机器人信息的变量，本快速入门的后面部分会替换该信息
- 使用麦克风进行音频输入的简单实现
- 在语音交互期间发生的各种事件的事件处理程序

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

用语音资源中的实际值替换 `YOUR_SUBSCRIPTION_KEY` 值和 `YOUR_BOT_REGION` 值。

- 导航到 Azure 门户，然后打开语音资源
- 在左侧的“密钥和终结点”下，有两个可用的订阅密钥
    - 使用其中的任一密钥来替换 `YOUR_SUBSCRIPTION_KEY` 值
- 在左侧的“概览”下，记下该区域并将其映射到区域标识符
    - 使用区域标识符来替换 `YOUR_BOT_REGION` 值，例如：使用 `"westus"` 来替换“美国西部”

   > [!NOTE]
   > 请参阅[语音助手支持的区域列表](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)，确保你的资源部署在其中一个区域中。

   > [!NOTE]
   > 有关配置机器人的信息，请参阅 [Direct Line 语音通道](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)的 Bot Framework 文档。

## <a name="code-explanation"></a>代码说明
需要语音订阅密钥和区域才能创建语音配置对象。 实例化语音识别器对象需要配置对象。

识别器实例公开了多种用于识别语音的方法。 在此示例中持续识别语音。 此功能告知语音服务，你要发送许多需识别的短语，并在程序终止时停止识别语音。 生成结果后，代码会将它们写入控制台。

## <a name="build-and-run"></a>生成并运行
现在，可以使用语音服务生成项目并测试自定义语音助手。
1. 生成项目，例如“开始生成”
2. 运行模块并对着设备的麦克风讲出一个短语或句子。 你的语音将传输到 Direct Line 语音通道并转录为文本，该文本会显示为输出。


> [!NOTE]
> 语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
