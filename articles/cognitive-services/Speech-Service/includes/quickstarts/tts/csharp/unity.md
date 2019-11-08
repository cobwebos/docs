---
title: 快速入门：合成语音，C# (Unity) - 语音服务
titleSuffix: Azure Cognitive Services
description: 参考本指南可使用 Unity 和适用于 Unity 的语音 SDK 创建文本转语音应用程序。 在完成后，可将文本中的语音实时合成到设备的扬声器中。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 9/19/2019
ms.author: yinhew
ms.openlocfilehash: 281fbcf3f42160b18adcad3f06f6ef4a7b8de243
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504897"
---
> [!NOTE]
> Unity 支持 Windows 桌面版（x86 和 x64）或通用 Windows 平台（x86、x64、ARM/ARM64）、Android（x86、ARM32/64）或 iOS（x64 模拟器、ARM32 和 ARM64）。

## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建 Azure 语音资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=unity)

## <a name="add-a-ui"></a>添加 UI

我们向场景中添加一个极简 UI，其中包括一个用于输入文本以进行合成的输入字段、一个用于触发语音合成的按钮，以及一个用于显示结果的文本字段。

* 在[“层次结构”窗口](https://docs.unity3d.com/Manual/Hierarchy.html)（默认位于左侧）中，显示了 Unity 通过新项目创建的示例场景。
* 选择“层次结构”窗口顶部的“创建”按钮，然后选择“UI” > “输入字段”。    
* 此选项会创建三个显示在“层次结构”窗口中的游戏对象：一个嵌套在“画布”对象中的“输入字段”对象，以及一个“EventSystem”对象    。 
* [浏览“场景”视图](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，以便全面查看[“场景”视图](https://docs.unity3d.com/Manual/UsingTheSceneView.html)中的画布和输入字段。
* 在“层次结构”窗口中选择“输入字段”对象，以便在[“检查器”窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中显示其设置。  
* 将“位置 X”  和“位置 Y”  属性都设置为 **0**，以使输入字段在画布上居中。
* 再次选择“层次结构”窗口顶部的“创建”按钮。   选择“UI” > “按钮”，创建一个按钮。  
* 在“层次结构”窗口中选择“按钮”对象，以便在[“检查器”窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中显示其设置。  
* 将“位置 X”和“位置 Y”属性分别设置为 **0** 和 **-48**。   将“宽度”和“高度”属性分别设置为 **160** 和 **30**，确保按钮和输入字段不会重叠。  
* 再次选择“层次结构”窗口顶部的“创建”按钮。   选择“UI” > “文本”以创建文本字段。  
* 在“层次结构”窗口中选择“文本”对象，以便在[“检查器”窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中显示其设置。  
* 将“位置 X”和“位置 Y”属性分别设置为 **0** 和 **80**。   将“宽度”和“高度”属性分别设置为 **320** 和 **80**，确保文本字段和输入字段不会重叠。  
* 再次选择“层次结构”窗口顶部的“创建”按钮。   选择“音频” > “音频源”以创建音频源。  

完成后，UI 应如以下屏幕截图所示：

[![Unity 编辑器中快速入门用户界面的屏幕截图](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>添加示例代码

1. 在[“项目”窗口](https://docs.unity3d.com/Manual/ProjectView.html)（默认位于左下角）中，选择“创建”按钮并选择“C# 脚本”。   将脚本命名为 `HelloWorld`。

1. 双击脚本对其进行编辑。

   > [!NOTE]
   > 可以选择“编辑” > “首选项”来配置要启动的代码编辑器。   有关详细信息，请参阅 [Unity 用户手册](https://docs.unity3d.com/Manual/Preferences.html)。

1. 将所有代码替换为以下内容：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/text-to-speech/Assets/Scripts/HelloWorld.cs#code)]

1. 找到字符串 `YourSubscriptionKey` 并将其替换为你的语音服务订阅密钥。

1. 找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)。 例如，如果使用免费试用版，则区域为 `westus`。

1. 保存对脚本所做的更改。

1. 返回到 Unity 编辑器，将脚本作为组件添加到游戏对象之一。

   * 在“层次结构”窗口中选择“画布”对象，以便在[“检查器”窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中打开设置。  
   * 在“检查器”窗口中选择“添加组件”按钮。   然后，搜索以前创建的 `HelloWorld` 脚本并添加它。
   * HelloWorld 组件包含四个未初始化的属性：“输出文本”  、“输入字段”  、“朗读按钮”  和“音频源”  ，它们与 `HelloWorld` 类的公共属性相匹配。
     若要连接它们，请选择对象选取器（属性右侧的小圆圈图标）。 选择此前创建的文本和按钮对象。

     > [!NOTE]
     > 输入字段和按钮还包含嵌套的文本对象。 请确保不要意外选择该对象来提供文本输出。 或者，可以在“检查器”窗口中重命名使用“名称”字段的文本对象，以避免这种混淆。  

## <a name="run-the-application-in-the-unity-editor"></a>在 Unity 编辑器中运行应用程序

* 在菜单栏下方的 Unity 编辑器工具栏中选择“播放”按钮。 
* 应用启动后，在输入字段中输入一些文本，然后选择该按钮。 文本会传输到语音服务并合成为语音，以在扬声器上播放。

  [![Unity 游戏窗口中运行的快速入门应用程序的屏幕截图](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-inline.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* 在[“控制台”窗口](https://docs.unity3d.com/Manual/Console.html)中检查调试消息。

## <a name="additional-options-to-run-this-application"></a>用于运行此应用程序的其他选项

还可将此应用程序作为 Windows 单机应用或 UWP 应用程序部署到 Android。
请参阅 quickstart/csharp-unity 文件夹中的[示例存储库](https://aka.ms/csspeech/samples)，其中描述了这些附加目标的配置。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>另请参阅

- [创建自定义语音](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [录制自定义语音示例](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
