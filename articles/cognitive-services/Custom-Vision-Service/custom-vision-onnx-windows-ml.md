---
title: 结合使用 ONNX 模型和 Windows ML - 自定义视觉服务
titleSuffix: Azure Cognitive Services
description: 了解如何创建使用从 Azure 认知服务导出的 ONNX 模型的 Windows UWP 应用。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: d6bcb5485833fbfaa3eb72191ee54b1ee69bab04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82594289"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>结合使用自定义视觉服务 ONNX 模型和 Windows ML（预览版）

了解如何结合使用从自定义影像服务导出的 ONNX 模型和 Windows ML（预览版）。

在本指南中，你将了解如何使用通过 Windows ML 从自定义影像服务导出的 ONNX 文件。 你将在示例 UWP 应用程序中使用自己训练的图像分类器。

## <a name="prerequisites"></a>先决条件

* Windows 10 版本 1809 或更高版本
* 适用于内部版本 17763 或更高版本的 Windows SDK
* 启用了“通用 Windows 平台开发”工作负载的 Visual Studio 2017 版本 15.7 或更高版本____。
* 已在你的电脑上启用开发人员模式。 有关详细信息，请参阅 [启用设备进行开发](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)。

## <a name="about-the-example-app"></a>关于示例应用

包含的应用程序是通用的 Windows UWP 应用程序。 它允许您从计算机中选择一个映像，然后由本地存储的分类模型进行处理。 模型返回的标记和分数显示在图像旁边。

## <a name="get-the-example-code"></a>获取示例代码

自定义视觉 GitHub 上的 " [认知服务 ONNX" 示例](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample) 存储库中提供了示例应用程序。 将其克隆到本地计算机，并在 Visual Studio 中打开 *SampleOnnxEvaluationApp* 。

## <a name="test-the-application"></a>测试应用程序

1. 按 `F5` 键，通过 Visual Studio 启动应用。 系统可能会提示启用“开发人员模式”。
1. 当应用程序启动时，请使用相关按钮来选择要评分的图像。 默认 ONNX 模型经过训练，可对不同类型的 plankton 进行分类。

## <a name="use-your-own-model"></a>使用自己的模型

若要使用自己的映像分类器模型，请执行以下步骤：

1. 使用自定义视觉服务创建和训练一个分类器。 有关如何执行此操作的说明，请参阅 [创建和训练分类器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)。 使用 **精简** 的域之一，例如 **一般 (compact) **。 
   * 如果现有分类器使用不同的域，则可以在项目设置中将其转换为 **compact** 。 然后，重新训练您的项目，然后再继续。
1. 导出模型。 切换到 "性能" 选项卡，然后选择已使用 **精简** 域进行训练的迭代。 选择显示的 " **导出** " 按钮。 然后选择 " **ONNX**"，然后选择 " **导出**"。 文件就绪以后，请选择“下载”按钮****。 有关导出选项的详细信息，请参阅 [导出模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)。
1. 打开下载的 *.zip* 文件并从中提取 *onnx* 文件。 此文件包含你的分类器模型。
1. 在 Visual Studio 的 "解决方案资源管理器中，右键单击" **资产** "文件夹，然后选择" __添加现有项__"。 选择 ONNX 文件。
1. 在解决方案资源管理器中，右键单击 ONNX 文件并选择 " **属性**"。 更改文件的以下属性：
   * __生成操作__ -> __内容__
   * __复制到输出目录__ -> __如果较新则复制__
1. 然后打开 _MainPage.xaml.cs_ 并将的值更改 `_ourOnnxFileName` 为 ONNX 文件的名称。
1. 使用 `F5` 生成并运行项目。
1. 单击按钮选择要评估的图像。

## <a name="next-steps"></a>后续步骤

要了解导出和使用自定义影像服务模型的其他方法，请参阅以下文档：

* [导出模型](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [在 Android 应用程序中使用导出的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [在 Swift iOS 应用中使用导出的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)
* [在带 Xamarin 的 iOS 应用程序中使用导出的 CoreML 模型](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

有关将 ONNX 模型与 Windows ML 一起使用的详细信息，请参阅使用 [WINDOWS ml 将模型集成到应用](/windows/ai/windows-ml/integrate-model)。
