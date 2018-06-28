---
title: 将自定义影像服务模型导出到移动环境 - 自定义影像服务 - Azure 认知服务| Microsoft Docs
description: 了解如何导出模型以用于创建移动应用程序。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: ce8f42d6239867dd217cddfc61a27d7835dc9c9b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "35366906"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>导出模型以用于移动设备

自定义影像服务支持导出分类器以脱机运行。 可以将导出的分类器嵌入应用程序，并在设备上本地运行，进行实时分类。 

自定义影像服务支持导出以下内容：

* 用于 Android 的 Tensorflow。
* 用于 iOS11 的 CoreML。
* 用于 Windows ML 的 ONNX。
* Windows 或 Linux 容器。 容器包括 Tensorflow 模型和服务代码，可用于使用自定义影像服务 API。 

> [!IMPORTANT]
> 自定义影像服务仅导出压缩域。 已针对移动设备上实时分类的约束对压缩域生成的模型进行了优化。 面对相同数量的定型数据时，用压缩域生成的分类器比用标准域生成的分类器的准确度略低。
>
> 有关如何改进分类器的信息，请参阅[改进分类器](getting-started-improving-your-classifier.md)文档。

## <a name="convert-to-a-compact-domain"></a>转换为压缩域

> [!NOTE]
> 本部分中的步骤仅适用于现有分类器未设置为压缩域的情况。
 
若要转换现有分类器的域，请采用以下步骤：

1. 在[自定义影像页面](https://customvision.ai)，选择“主页”图标以查看项目列表。 也可以访问 [https://customvision.ai/projects](https://customvision.ai/projects) 查看项目。

    ![“主页”图标和项目列表的图像](./media/export-your-model/projects-list.png)

2. 选择一个项目，然后选择页面右上角的齿轮图标。

    ![齿轮图标的图像](./media/export-your-model/gear-icon.png)

3. 在“域”部分，选择一个压缩域。 选择“保存更改”来保存更改。

    ![域选项的图像](./media/export-your-model/domains.png)

4. 从页面顶部选择“定型”以使用新域进行重新定型。

## <a name="export-your-model"></a>导出模型

若要在重新定型后导出模型，请采用以下步骤：

1. 转到“性能”选项卡并选择“导出”。 

    ![导出图标的图像](./media/export-your-model/export.png)

    > [!TIP]
    > 如果“导出”项不可用，则表示所选迭代未使用压缩域。 使用此页面中的“迭代”部分，选择使用压缩域的迭代，然后选择“导出”。

2. 选择导出格式，然后选择“导出”以下载该模型。

## <a name="next-steps"></a>后续步骤

将导出的模型集成到应用程序中。 以下几个示例应用程序可供参考：

* [在 iOS 应用程序中使用导出的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)以使用 Swift 进行实时图像分类的示例
* [将导出的 CoreML 模型与 Xamarin 配合使用](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)以进行实时图像分类的 iOS 应用程序示例 
* [在 Android 应用程序中使用导出的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)进行实时图像分类的示例 
* [配合使用 Tensorflow 模型和 Windows ](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/export-model-python)
* [将导出的 ONNX 模型与 Windows 机器学习配合使用](https://azure.microsoft.com/en-us/resources/samples/cognitive-services-onnx-customvision-sample/)的示例
