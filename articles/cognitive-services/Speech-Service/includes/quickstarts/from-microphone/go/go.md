---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: a18ce7b7c28b99967668bc33c5a94cbb58bfbc34
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376631"
---
## <a name="prerequisites"></a>先决条件

准备工作：

> [!div class="checklist"]
> * [创建 Azure 语音资源](../../../../overview.md#try-the-speech-service-for-free)
> * [设置开发环境并创建空项目](../../../../quickstarts/setup-platform.md)
> * 请确保你有权访问麦克风，以便进行音频捕获

## <a name="setup-your-environment"></a>设置环境

通过添加以下行，使用最新的 SDK 版本更新 go.mod 文件
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>从一些样本代码入手
1. 将源文件（例如 `sr-quickstart.go`）的内容替换为以下内容，其中包括：

- “主要”包定义
- 从语音 SDK 导入所需的模块
- 用于存储订阅信息的变量，本快速入门的后面部分会替换该信息
- 使用麦克风进行音频输入的简单实现
- 在语音识别期间发生的各种事件的事件处理程序

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

用语音资源中的实际值替换 `YOUR_SUBSCRIPTION_KEY` 值和 `YOUR_SUBSCRIPTIONKEY_REGION` 值。

- 导航到 Azure 门户，然后打开语音资源
- 在左侧的“密钥”下，有两个可用的订阅密钥
    - 使用其中的任一密钥来替换 `YOUR_SUBSCRIPTION_KEY` 值
- 在左侧的“概览”下，记下该区域并将其映射到区域标识符
- 使用区域标识符来替换 `YOUR_SUBSCRIPTIONKEY_REGION` 值，例如：使用 `"westus"` 来替换“美国西部”

## <a name="code-explanation"></a>代码说明
需要语音订阅密钥和区域才能创建语音配置对象。 实例化语音识别器对象需要配置对象。

识别器实例公开了多种用于识别语音的方法。 在此示例中持续识别语音。 此功能告知语音服务，你要发送许多需识别的短语，并在程序终止时停止识别语音。 生成结果后，代码会将它们写入控制台。

## <a name="build-and-run"></a>生成并运行
现在，可以使用语音服务生成项目并测试语音识别。
1. 生成项目，例如“开始生成”
2. 运行模块并对着设备的麦克风讲出一个短语或句子。 你的语音将传输到语音服务并转录为文本，该文本将显示在输出中。


> [!NOTE]
> 语音 SDK 将默认使用 en-us 作为语言进行识别。若要了解如何选择源语言，请参阅[指定语音转文本的源语言](../../../../how-to-specify-source-language.md)。


## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
