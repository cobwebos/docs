---
title: 快速入门：从麦克风中识别语音，C# (Unity) - 语音服务
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: babc6b1c0b6207d404091db3117f95f6d71c53ac
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505553"
---
> [!NOTE]
> 适用于 Unity 的语音 SDK 支持 Windows 桌面版（x86 和 x64）或通用 Windows 平台（x86、x64、ARM/ARM64）、Android（x86、ARM32/64）或 iOS（x64 模拟器、ARM32 和 ARM64）

## <a name="prerequisites"></a>先决条件

在开始之前，请务必：

> [!div class="checklist"]
> * [创建一个 Azure 搜索资源](../../../../get-started.md)
> * [设置开发环境](../../../../quickstarts/setup-platform.md?tabs=unity)
> * [创建空示例项目](../../../../quickstarts/create-project.md?tabs=unity)。如果已完成此操作，那就太好了。 让我们继续。

## <a name="create-a-unity-project"></a>创建 Unity 项目

1. 打开 Unity。 首次使用 Unity，会显示“Unity Hub <version number>”窗口。   （也可以直接打开 Unity Hub 转到此窗口。）

   [![Unity Hub 窗口](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-hub.png#lightbox)
1. 选择“新建”  。 此时会显示“使用 Unity <version number> 创建新项目”窗口。  

   [![在 Unity Hub 中创建新项目](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-create-a-new-project.png#lightbox)
1. 在“项目名称”中输入 **csharp-unity**。 
1. 在“模板”中，如果尚未选择“3D”，请选择它。  
1. 在“位置”中，选择或创建用于保存项目的文件夹。 
1. 选择“创建”  。

片刻之后，会显示 Unity 编辑器窗口。



## <a name="add-ui"></a>添加 UI

现在让我们将一个极简的 UI 添加到场景中。 此 UI 包括一个用于触发语音识别的按钮，以及一个用于显示结果的文本字段。 在[“层次结构”窗口](https://docs.unity3d.com/Manual/Hierarchy.html)中，显示了 Unity 创建的、包含新项目的示例场景。 

1. 在“层次结构”窗口的顶部选择“创建” > “UI” > “按钮”。    

   此操作会创建三个显示在“层次结构”窗口中的游戏对象：一个 **Button** 对象、一个包含按钮的 **Canvas** 对象，以及一个 **EventSystem** 对象。 

   [![Unity 编辑器环境](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-editor-window.png#lightbox)

1. [浏览“场景”视图](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，以全面查看[“场景”视图](https://docs.unity3d.com/Manual/UsingTheSceneView.html)中的画布和按钮。  

1. 在[“检查器”窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中，将“位置 X”和“位置 Y”属性设置为 0，使按钮在画布上居中显示。    

1. 在“层次结构”窗口中选择“创建” > “UI” > “文本”以创建一个 **Text** 对象。    

1. 在“检查器”窗口中，将“位置 X”和“位置 Y”属性设置为分别设置为 **0** 和 **120**，将“宽度”和“高度”属性分别设置为 **240** 和 **120**。      这些值确保文本字段和按钮不会重叠。

完成后，“场景”视图应如以下屏幕截图所示： 

[![Unity 编辑器中的“场景”视图](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-02-ui-inline.png#lightbox)

## <a name="add-the-sample-code"></a>添加示例代码

若要添加 Unity 项目的示例脚本代码，请执行以下步骤：

1. 在[“项目”窗口](https://docs.unity3d.com/Manual/ProjectView.html)中，选择“创建” > “C# 脚本”以添加新的 C# 脚本。  

   [![Unity 编辑器中的“项目”窗口](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-project-window.png#lightbox)
1. 将脚本命名为 `HelloWorld`。

1. 双击 `HelloWorld` 以编辑新建的脚本。

   > [!NOTE]
   > 若要配置 Unity 使用的代码编辑器，请选择“编辑” > “首选项”，然后单击“外部工具”首选项。    有关详细信息，请参阅 [Unity 用户手册](https://docs.unity3d.com/Manual/Preferences.html)。

1. 将现有脚本替换为以下代码：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/unity/from-microphone/Assets/Scripts/HelloWorld.cs#code)]

1. 找到字符串 `YourSubscriptionKey` 并将其替换为你的语音服务订阅密钥。

1. 找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](~/articles/cognitive-services/Speech-Service/regions.md)。 例如，如果使用的是免费试用版，区域是 `westus`。

1. 保存对脚本所做的更改。

现在，请返回到 Unity 编辑器，并将脚本作为组件添加到游戏对象之一：

1. 在“层次结构”窗口中选择“Canvas”对象。  

1. 在“检查器”窗口中选择“添加组件”按钮。  

   [![Unity 编辑器中的“检查器”窗口](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-inspector-window.png#lightbox)

1. 在下拉列表中，搜索前面创建的 `HelloWorld` 脚本并添加它。 “检查器”窗口中将显示“Hello World (脚本)”部分，其中列出了两个未初始化的属性：“输出文本”和“开始识别按钮”。     这些 Unity 组件与 `HelloWorld` 类的公共属性相匹配。

1. 选择“开始识别按钮”属性的对象选取器（属性右侧的小圆圈图标），并选择前面创建的 **Button** 对象。 

1. 选择“输出文本”属性的对象选取器，并选择前面创建的 **Text** 对象。 

   > [!NOTE]
   > 按钮中还包括嵌套的文本对象。 请确保不要意外选择该对象来提供文本输出（或者，可以使用“检查器”窗口中的“名称”字段来重命名某个文本对象，以避免混淆）。  

## <a name="run-the-application-in-the-unity-editor"></a>在 Unity 编辑器中运行应用程序

现已准备好在 Unity 编辑器中运行该应用程序。

1. 在 Unity 编辑器工具栏（位于菜单栏下方）中，选择“播放”按钮（向右三角形）。 

1. 转到[“游戏”视图](https://docs.unity3d.com/Manual/GameView.html)，等待 **Text** 对象显示“单击按钮以识别语音”。   （当应用程序尚未启动或未准备好响应时，该对象会显示“新建文本”。） 

1. 选择创建的按钮，对着计算机的麦克风讲出英语短语或句子。 你的语音将传输到语音服务并转录为文本，该文本将显示在“游戏”视图中。 

   [![Unity 编辑器中的“游戏”视图](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-unity-03-output-inline.png#lightbox)

1. 在[“控制台”窗口](https://docs.unity3d.com/Manual/Console.html)中检查调试消息。  如果“控制台”窗口未显示，转到菜单栏并选择“窗口” > “常规” > “控制台”即可显示。    

1. 完成语音识别后，在 Unity 编辑器工具栏中选择“播放”按钮以停止应用程序。 

## <a name="additional-options-to-run-this-application"></a>用于运行此应用程序的其他选项

还可将此应用程序部署为 Android 应用、Windows 单机应用或 UWP 应用程序。
有关详细信息，请参阅我们的[示例存储库](https://aka.ms/csspeech/samples)。 `quickstart/csharp-unity` 文件夹描述了这些附加目标的配置。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [footer](./footer.md)]
