---
title: 快速入门：将语音转换为文本，C# (UWP) - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 12/09/2019
ms.author: erhopf
ms.topic: include
ms.openlocfilehash: 70db4e23aebe2a986c9aa4c85c28ac6529ac3202
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "76155962"
---
## <a name="prerequisites"></a>必备条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>添加示例代码

现在，添加定义应用程序用户界面的 XAML 代码，并添加 C# 代码隐藏实现。

1. 在“解决方案资源管理器”  中打开 `MainPage.xaml`。

1. 在设计器的 XAML 视图中，将以下 XAML 代码片段插入到“Grid”  标记中（位于 `<Grid>` 和 `</Grid>` 之间）：

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml#StackPanel)]

1. 在“解决方案资源管理器”  中，打开代码隐藏源文件 `MainPage.xaml.cs`。 （其分组在 `MainPage.xaml` 下。）

1. 将其中的所有代码替换为以下片段：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/translate-speech-to-text/helloworld/MainPage.xaml.cs#code)]

1. 在此文件的 `SpeechTranslationFromMicrophone_ButtonClicked` 处理程序中，查找字符串 `YourSubscriptionKey`，将其替换为订阅密钥。

1. 在 `SpeechTranslationFromMicrophone_ButtonClicked` 处理程序中，找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)。 （例如，将 `westus` 用于免费试用版订阅。）

1. 在菜单栏中，选择“文件”   > “全部保存”  以保存所做的更改。

## <a name="build-and-run-the-application"></a>构建并运行应用程序

现已准备好构建并测试应用程序。

1. 从菜单栏中，选择“构建”   > “构建解决方案”  以构建应用程序。 现在，编译代码时应不会提示错误。

1. 选择“调试”   > “开始调试”  （或按 F5  ）以启动应用程序。 此时将显示“helloworld”  窗口。

   ![C# 中的示例 UWP 翻译应用程序 - 快速入门](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. 选择“启用麦克风”  ，并在弹出访问权限请求时选择“是”  。

   ![麦克风访问权限请求](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. 选择“翻译来自麦克风输入的语音”，然后对着设备的麦克风说一个英文短语或句子  。 应用程序将语音传输到语音服务，该服务会将语音翻译为其他语言（在本例中为德语）的文本。 语音服务将翻译后的文本发送回应用程序，该应用程序在窗口中显示翻译。

   ![语音翻译用户界面](~/articles/cognitive-services/Speech-Service/media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
