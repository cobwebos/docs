---
title: 快速入门：合成语音，Unity - 语音服务
titleSuffix: Azure Cognitive Services
description: 参考本指南可使用 Unity 和适用于 Unity 的语音 SDK (Beta) 创建文本转语音应用程序。 在完成后，可将文本中的语音实时合成到设备的扬声器中。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/26/2019
ms.author: yinhew
ms.openlocfilehash: 5240ea45097ce3c0ae7ccbc15a7f99b2f5990832
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467263"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-unity-beta"></a>快速入门：使用适用于 Unity 的语音 SDK (Beta) 合成语音

针对[语音识别](quickstart-csharp-unity.md)也提供了快速入门。

参考本指南可使用 [Unity](https://unity3d.com/) 和适用于 Unity 的语音 SDK (Beta) 创建文本转语音应用程序。
在完成后，可将文本中的语音实时合成到设备的扬声器中。
如果你不熟悉 Unity，我们建议在开始开发应用程序之前，先学习 [Unity 用户手册](https://docs.unity3d.com/Manual/UnityManual.html)。

> [!NOTE]
> 适用于 Unity 的语音 SDK 目前为 Beta 版。
> 它支持 Windows 桌面版（x86 和 x64）或通用 Windows 平台（x86、x64、ARM/ARM64）以及 Android（x86、ARM32/64）。

## <a name="prerequisites"></a>先决条件

若要完成本项目，需要：

* [Unity 2018.3 或更高版本](https://store.unity.com/)，以及[支持 UWP ARM64 的 Unity 2019.1](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
     * 为了支持 ARM64，请安装[适用于 ARM64 的可选版本，以及适用于 ARM64 的 Windows 10 SDK](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/) 
* 语音服务的订阅密钥。 [免费获得一个](get-started.md)。

## <a name="create-a-unity-project"></a>创建 Unity 项目

* 启动 Unity，并在“项目”选项卡下选择“新建”。  
* 指定 **csharp-unity** 作为“项目名称”，指定“3D”作为“模板”，然后选择一个位置。   
  选择“创建项目”。 
* 片刻之后，Unity 编辑器窗口应会弹出。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

* 适用于 Unity 的语音 SDK (Beta) 打包为 Unity 资产包 (.unitypackage)。
  从[此处](https://aka.ms/csspeech/unitypackage)下载它。
* 选择“资产” > “导入包” > “自定义包”导入 Speech SDK。   
  有关详细信息，请查看 [Unity 文档](https://docs.unity3d.com/Manual/AssetPackages.html)。
* 在文件选取器中，选择前面下载的语音 SDK .unitypackage 文件。
* 确保选择所有文件，然后单击“导入”： 

  ![导入语音 SDK Unity 资产包时的 Unity 编辑器屏幕截图](media/sdk/qs-csharp-unity-01-import.png)

## <a name="add-ui"></a>添加 UI

我们可向场景中添加少量的 UI，其中包括一个用于输入文本以进行合成的输入字段、一个用于触发语音合成的按钮，以及一个用于显示结果的文本字段。

* 在[层次结构窗口](https://docs.unity3d.com/Manual/Hierarchy.html)（默认位于左侧）中，显示了 Unity 创建的、包含新项目的示例场景。
* 单击“层次结构窗口”  顶部的“创建”按钮，然后依次选择“UI”   > “输入字段”  。
* 这会创建三个显示在“层次结构窗口”中的游戏对象：一个嵌套在“画布”对象中的“输入字段”对象，以及一个“EventSystem”对象    。
* [浏览场景视图](https://docs.unity3d.com/Manual/SceneViewNavigation.html)，以便全面查看[场景视图](https://docs.unity3d.com/Manual/UsingTheSceneView.html)中的画布和输入字段。
* 在“层次结构窗口”中单击“输入字段”  对象，以在[检查器窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中显示其设置。
* 将“位置 X”  和“位置 Y”  属性都设置为 **0**，以使输入字段在画布上居中。
* 再次单击“层次结构窗口”顶部的“创建”  按钮，然后依次选择“UI”   > “按钮”以创建按钮  。
* 在“层次结构窗口”中单击“按钮”对象，以便在[检查器窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中显示其设置。 
* 将“位置 X”  和“位置 Y”  属性分别设置为 **0** 和 **-48**，而将“宽度”  和“高度”  属性分别设置为 **160** 和 **30**，以确保按钮和输入字段不会重叠。
* 再次单击“层次结构窗口”顶部的“创建”按钮，然后选择“UI” > “文本”以创建文本字段。   
* 在“层次结构窗口”中单击“文本”对象，以便在[检查器窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中显示其设置。 
* 将“位置 X”  和“位置 Y”  属性分别设置为 **0** 和 **80**，将“宽度”  和“高度”  属性分别设置为 **320** 和 **80**，以确保文本字段和输入字段不会重叠。
* 再次单击“层次结构窗口”顶部的“创建”  按钮，然后依次选择“音频”   > “音频源”  以创建音频源。

完成后，UI 应如以下屏幕截图所示：

[![Unity 编辑器中快速入门用户界面的屏幕截图](media/sdk/qs-tts-csharp-unity-ui-inline.png)](media/sdk/qs-tts-csharp-unity-ui-expanded.png#lightbox)

## <a name="add-the-sample-code"></a>添加示例代码

1. 在[项目窗口](https://docs.unity3d.com/Manual/ProjectView.html)（默认位于左下角）中，单击“创建”按钮并选择“C# 脚本”。   将脚本命名为 `HelloWorld`。

1. 双击脚本对其进行编辑。

   > [!NOTE]
   > 可以在“编辑” > “首选项”下配置要启动的代码编辑器，详情请参阅 [Unity 用户手册](https://docs.unity3d.com/Manual/Preferences.html)。  

1. 将所有代码替换为以下内容：

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-unity/Assets/Scripts/HelloWorld.cs#code)]

1. 找到字符串 `YourSubscriptionKey` 并将其替换为你的语音服务订阅密钥。

1. 找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](regions.md)。 例如，如果使用的是免费试用版，区域是 `westus`。

1. 保存对脚本所做的更改。

1. 返回到 Unity 编辑器。需要将脚本作为组件添加到游戏对象之一。

   * 单击“层次结构窗口”中的“画布”对象。  这会在[检查器窗口](https://docs.unity3d.com/Manual/UsingTheInspector.html)（默认位于右侧）中打开设置。
   * 单击“检查器窗口”中的“添加组件”按钮，然后搜索并添加前面创建的 HelloWorld 脚本。 
   * 请注意，Hello World 组件包含四个未初始化的属性：“输出文本”  、“输入字段”  、“朗读按钮”  和“音频源”  ，它们与 `HelloWorld` 类的公共属性相匹配。
     若要连接它们，请单击对象选取器（属性右侧的小圆圈图标），并选择前面创建的文本和按钮对象。

     > [!NOTE]
     > 输入字段和按钮还包含嵌套的文本对象。 请确保不要意外选择该对象来提供文本输出（或者，使用“检查器窗口”中的“名称”字段来重命名文本对象，以避免混淆）。

## <a name="run-the-application-in-the-unity-editor"></a>在 Unity 编辑器中运行应用程序

* 在 Unity 编辑器工具栏（菜单栏下方）中按下“播放”按钮。 

* 在应用启动后，请在输入字段中输入一些文本，然后单击该按钮。 你的文本将传输到语音服务，并合成为语音，以在扬声器上播放。

  [![Unity 游戏窗口中运行的快速入门应用程序的屏幕截图](media/sdk/qs-tts-csharp-unity-output-inline.png)](media/sdk/qs-tts-csharp-unity-output-expanded.png#lightbox)

* 在[控制台窗口](https://docs.unity3d.com/Manual/Console.html)中检查调试消息。

* 完成语音合成后，在 Unity 编辑器工具栏中单击“播放”按钮以停止应用  。

## <a name="additional-options-to-run-this-application"></a>用于运行此应用程序的其他选项

还可将此应用程序作为 Windows 单机应用或 UWP 应用程序部署到 Android。
请参阅 quickstart/csharp-unity 文件夹中的[示例存储库](https://aka.ms/csspeech/samples)，其中描述了这些附加目标的配置。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [自定义语音字体](how-to-customize-voice-font.md)
- [录制语音示例](record-custom-voice-samples.md)
