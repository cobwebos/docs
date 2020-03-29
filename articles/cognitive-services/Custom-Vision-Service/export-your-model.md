---
title: 将模型导出到移动设备 - 自定义视觉服务
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何导出模型以用于创建移动应用程序或本地运行以进行实时分类。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: f734f4f1a11f57b759615e7a9ce2cd2f7f8028fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718949"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>导出模型以用于移动设备

自定义影像服务支持导出分类器以脱机运行。 可以将导出的分类器嵌入应用程序，并在设备上本地运行，进行实时分类。

## <a name="export-options"></a>导出选项

自定义影像服务支持导出以下内容：

* 用于 Android 的 Tensorflow________。
* 用于 iOS11 的 CoreML________。
* 用于 Windows ML 的 ONNX________。
* __[愿景 AI 开发人员工具包](https://azure.github.io/Vision-AI-DevKit-Pages/)__。
* 用于 Windows、Linux 或 ARM 体系结构的__Docker 容器__。 容器包括使用自定义视觉 API 的 Tensorflow 模型和服务代码。

> [!IMPORTANT]
> 自定义影像服务仅导出压缩域____。 已针对移动设备上实时分类的约束对压缩域生成的模型进行了优化。 面对相同数量的定型数据时，用压缩域生成的分类器比用标准域生成的分类器的准确度略低。
>
> 有关如何改进分类器的信息，请参阅[改进分类器](getting-started-improving-your-classifier.md)文档。

## <a name="convert-to-a-compact-domain"></a>转换为压缩域

> [!NOTE]
> 仅当现有模型未设置为压缩域时，本节中的步骤才适用。

要转换现有模型的域，请执行以下步骤：

1. 在[自定义视觉网站上](https://customvision.ai)，选择 __"主页__"图标以查看项目列表。

    ![“主页”图标和项目列表的图像](./media/export-your-model/projects-list.png)

1. 选择一个项目，然后选择页面右上角的齿轮图标____。

    ![齿轮图标的图像](./media/export-your-model/gear-icon.png)

1. 在 __"域"__ 部分中，选择其中一个__紧凑的__域。 选择“保存更改”来保存更改____。 

    > [!NOTE]
    > 对于 Vision AI 开发工具包，必须使用__常规（压缩）__ 域创建项目，并且必须在 **"导出功能"** 部分下指定 **"视觉 AI 开发工具包**"选项。

    ![域选项的图像](./media/export-your-model/domains.png)

1. 从页面顶部选择“定型”以使用新域进行重新定型____。

## <a name="export-your-model"></a>导出模型

若要在重新定型后导出模型，请采用以下步骤：

1. 转到“性能”选项卡并选择“导出”****____。 

    ![导出图标的图像](./media/export-your-model/export.png)

    > [!TIP]
    > 如果“导出”项不可用，则表示所选迭代未使用压缩域____。 使用此页面中的“迭代”部分，选择使用压缩域的迭代，然后选择“导出”________。

1. 选择所需的导出格式，然后选择 __"导出"__ 以下载模型。

## <a name="next-steps"></a>后续步骤

通过浏览以下文章或示例之一，将导出的模型集成到应用程序中：

* [将 Tensorflow 模型与 Python 一起使用](export-model-python.md)
* [将 ONNX 模型与 Windows 机器学习一起使用](custom-vision-onnx-windows-ml.md)
* 有关使用 Swift 的实时图像分类，请参阅[iOS 应用程序中的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)示例。
* 有关 Android 上的实时图像分类，请参阅[Android 应用程序中的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)示例。
* 有关 Xamarin iOS 应用中的实时图像分类，请参阅[使用 Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)的 CoreML 模型的示例。
