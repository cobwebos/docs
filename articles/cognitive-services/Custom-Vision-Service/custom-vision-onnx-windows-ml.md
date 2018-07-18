---
title: 结合使用自定义影像服务 ONNX 模型和 Windows ML - 认知服务 | Microsoft Docs
description: 了解如何创建使用从认知服务导出的 ONNX 模型的 Windows UWP 应用。
services: cognitive-services
author: larryfr
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: larryfr
ms.openlocfilehash: 0b128ba1800e74c20c09a9c5711c8473f1dd00d0
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939387"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>教程：结合使用自定义影像服务 ONNX 模型和 Windows ML（预览版）

了解如何结合使用从自定义影像服务导出的 ONNX 模型和 Windows ML（预览版）。

本文档介绍了如何结合使用从自定义影像服务导出的 ONNX 文件和 Windows ML。 同时还展示了示例 Windows UWP 应用。 示例中随附可识别狗和猫的定型模型。 此外，还分步介绍了如何对此示例使用自己的模型。

> [!div class="checklist"]
> * 关于示例应用
> * 获取示例代码
> * 运行示例
> * 使用自己的模型

## <a name="prerequisites"></a>先决条件

* Windows 10 设备且配备有：

    * 摄像头。

    * 启用了“通用 Windows 平台开发”工作负载的 Visual Studio 2017 版本 15.7 或更高版本。

    * 已启用的“开发人员模式”。 有关详细信息，请参阅[启用设备以用于开发](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)文档。

* （可选）从自定义影像服务导出的 ONNX 文件。 有关详细信息，请参阅[导出模型以用于移动设备](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)文档。

    > [!NOTE]
    > 若要使用自己的模型，请按照[使用自己的模型](#use-your-own-model)部分中的步骤操作。

## <a name="about-the-example-app"></a>关于示例应用

应用是通用 Windows UWP 应用。 它使用 Windows 10 设备上的摄像头为模型提供图像。 模型返回的标记和分数显示在视频预览下方。

* 当数据通过摄像头传入时，[MediaFrameReader](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader) 用于提取各个帧。 帧会发送到模型以供评分。

* 模型返回定型标记，以及表示图像包含相应项的可信度的浮点值。

### <a name="the-ui"></a>UI

示例应用的 UI 是使用 CaptureElement 和 TextBlock 控件创建而成。 CaptureElement 显示摄像头捕获的视频预览，TextBlock 显示模型返回的结果。 

### <a name="the-model"></a>模型

此示例随附的模型 (`cat-or-dog.onnx`) 是使用认知服务自定义影像服务创建并定型。 定型模型随后导出为 ONNX 模型。 若要详细了解如何使用此服务，请参阅[如何生成分类器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)和[导出模型以用于移动设备](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)文档。

> [!IMPORTANT]
> 此示例随附的模型是使用一小部分狗图像和猫图像进行定型。 所以在识别狗和猫方面它可能不是世界上做得最好的。

### <a name="the-model-class-file"></a>模型类文件

将 ONNX 文件添加到 Windows UWP 应用时，它会创建 .cs 文件。 此文件与 `.onnx` 文件同名（在此示例中为 `cat-or-dog`），并包含用于处理模型的 C# 类。 不过，生成的类实体的名称可能为 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`。 可以出于稳妥考虑将这些条目重命名（右键单击并选择“重命名”）为易记名称。

> [!NOTE]
> 示例代码已将生成的类名和方法名称重构为：
>
> * `ModelInput`
> * `ModelOutput`
> * `Model`
> * `CreateModel`

### <a name="camera-access"></a>摄像头访问

`Package.appxmanifest` 文件中的“功能”选项卡配置为，允许访问摄像头和麦克风。

> [!NOTE]
> 尽管此示例不使用音频，但我不得不先启用麦克风，然后才能访问设备上的摄像头。

应用尝试访问设备背面的摄像头（若有）。 它使用 [MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) 类开始从摄像头捕获视频。 [MediaFrameReader](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) 用于捕获视频帧，并将帧发送到模型。

## <a name="get-the-example-code"></a>获取示例代码

可以从 [https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP](https://github.com/Azure-Samples/Custom-Vision-ONNX-UWP) 获取此示例应用。

## <a name="run-the-example"></a>运行示例

1. 按 `F5` 键，以通过 Visual Studio 启动应用。 系统可能会提示启用“开发人员模式”。 有关详细信息，请参阅[启用设备以用于开发](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)文档。

2. 当出现提示时，允许应用访问设备上的摄像头和麦克风。

3. 将摄像头对准狗或猫。 图像是否包含狗或猫的分数显示在应用预览下方。

    > [!TIP]
    > 如果手边没有狗或猫，可以使用狗或猫的照片。

## <a name="use-your-own-model"></a>使用自己的模型

若要使用自己的模型，请按照以下步骤操作：

> [!IMPORTANT]
> 本部分介绍了如何重命名当前模型 (cat-or-dog.cs)，并重构新模型的类名和方法名称。 这是为了避免与示例模型发生命名冲突。

1. 使用自定义影像服务定型模型。 若要了解如何定型模型，请参阅[如何生成分类器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)。

2. 将定型模型导出为 ONNX 模型。 若要了解如何导出模型，请参阅[导出模型以用于移动设备](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)文档。

3. 在解决方案资源管理器中，右键单击“cat-or-dog.cs”，并将它重命名为“cat-or-dog.txt”。 重命名可防止与新模型发生命名冲突。

    > [!TIP]
    > 还可以在新模型中使用不同的类名，而重用现有名称则更简单。

4. 在解决方案资源管理器中，右键单击“VisionApp”条目，再依次选择“添加” > “现有项...”。

5. 若要为模型生成类，请依次选择要导入的 ONNX 文件和“添加”按钮。 此时，在解决方案资源管理器中添加了与 ONNX 文件同名（但扩展名为 `.cs`）的新类。

6. 打开生成的 .cs 文件，找到以下项的名称：

    > [!IMPORTANT]
    > 请参考示例 `cat-or-dog.txt` 文件来识别类和函数。

    * 定义模型输入的类。 生成的名称可能类似于 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelInput`。 将此类重命名为 ModelInput。
    * 定义模型输出的类。 生成的名称可能类似于 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70ModelOutput`。 将此类重命名为 ModelOutput。
    * 定义模型的类。 生成的名称可能类似于 `_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`。 将此类重命名为 Model。
    * 创建模型的方法。 生成的名称可能类似于 `Create_x0033_04aa07b_x002D_6c8c_x002D_4641_x002D_93a6_x002D_f3152f8740a1_028da4e3_x002D_9c6e_x002D_480b_x002D_b53c_x002D_c1db13d24d70Model`。 将此方法重命名为 CreateModel。

7. 在解决方案资源管理器中，将 `.onnx` 文件移到“资产”文件夹中。 

8. 若要在应用包中添加 ONNX 文件，请选择“`.onnx`”文件，并在属性中将“生成操作”设置为“内容”。

9. 打开“MainPage.xaml.cs”文件。 找到以下代码行，并将文件名更改为新的 `.onnx` 文件：

    ```csharp
    var file = await StorageFile.GetFileFromApplicationUriAsync(new Uri($"ms-appx:///Assets/cat-or-dog.onnx"));
    ```

    此更改会在运行时加载新模型。

10. 构建并运行应用程序。 它现在使用新模型来评分图像。

## <a name="next-steps"></a>后续步骤

若要了解导出和使用自定义影像服务模型的其他方法，请参阅以下文档：

* [导出模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [在 Android 应用中使用导出的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [在 Swift iOS 应用中使用导出的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)
* [在 iOS 应用中结合使用导出的 CoreML 模型和 Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

若要详细了解如何结合使用 ONNX 模型和 Windows ML，请参阅[使用 Windows ML 将模型集成到应用](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model)文档。
