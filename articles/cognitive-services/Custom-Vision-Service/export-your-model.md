---
title: 将模型导出到移动设备 - 自定义视觉服务
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何导出模型，以便在创建移动应用程序时使用，或在本地运行以进行实时分类。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: be28a10f8a96d21d64d08cfacee4543c065168ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85391751"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>导出模型以用于移动设备

自定义影像服务支持导出分类器以脱机运行。 可以将导出的分类器嵌入应用程序，并在设备上本地运行，进行实时分类。

## <a name="export-options"></a>导出选项

自定义影像服务支持导出以下内容：

* 用于 Android 的 Tensorflow________。
* 用于 iOS11 的 CoreML________。
* 用于 Windows ML 的 ONNX________。
* __[视觉 AI 开发人员工具包](https://azure.github.io/Vision-AI-DevKit-Pages/)__。
* 适用于 Windows、Linux 或 ARM 体系结构的 __Docker 容器__ 。 容器包含 Tensorflow 模型和服务代码，可使用自定义视觉 API。

> [!IMPORTANT]
> 自定义影像服务仅导出压缩域____。 已针对移动设备上实时分类的约束对压缩域生成的模型进行了优化。 面对相同数量的定型数据时，用压缩域生成的分类器比用标准域生成的分类器的准确度略低。
>
> 有关如何改进分类器的信息，请参阅[改进分类器](getting-started-improving-your-classifier.md)文档。

## <a name="convert-to-a-compact-domain"></a>转换为压缩域

> [!NOTE]
> 本部分中的步骤仅适用于是否有未设置为 compact 域的现有模型。

若要转换现有模型的域，请执行以下步骤：

1. 在 [自定义视觉网站](https://customvision.ai)上，选择 " __主页__ " 图标以查看项目的列表。

    ![“主页”图标和项目列表的图像](./media/export-your-model/projects-list.png)

1. 选择一个项目，然后选择页面右上角的齿轮图标____。

    ![齿轮图标的图像](./media/export-your-model/gear-icon.png)

1. 在 " __域__ " 部分中，选择一个 __压缩__ 域。 选择“保存更改”来保存更改____。 

    > [!NOTE]
    > 对于视觉 AI 开发工具包，必须创建具有__常规 (Compact) __域的项目，并且必须在 "**导出功能**" 部分下指定 "**视觉 AI 开发工具包**" 选项。

    ![域选项的图像](./media/export-your-model/domains.png)

1. 从页面顶部选择“定型”以使用新域进行重新定型____。

## <a name="export-your-model"></a>导出模型

若要在重新定型后导出模型，请采用以下步骤：

1. 转到“性能”选项卡并选择“导出”****____。 

    ![导出图标的图像](./media/export-your-model/export.png)

    > [!TIP]
    > 如果“导出”项不可用，则表示所选迭代未使用压缩域____。 使用此页面中的“迭代”部分，选择使用压缩域的迭代，然后选择“导出”________。

1. 选择所需的导出格式，然后选择 " __导出__ " 以下载模型。

## <a name="next-steps"></a>后续步骤

通过浏览以下文章或示例之一将导出的模型集成到应用程序中：

* [将 Tensorflow 模型与 Python 配合使用](export-model-python.md)
* [将 ONNX 模型用于 Windows 机器学习](custom-vision-onnx-windows-ml.md)
* 请参阅 [iOS 应用程序中的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726) 示例，以便使用 Swift 实现实时映像分类。
* 请参阅 android [应用程序中的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) 示例，了解 android 上的实时图像分类。
* 请参阅在 Xamarin iOS 应用中使用 Xamarin 进行实时映像分类的 [CoreML 模型](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) 示例。
