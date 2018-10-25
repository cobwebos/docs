---
title: 发行说明 - 自定义影像服务
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: 8b4ba1601a7c5a9f523f7f8145a032861aac5d8c
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901311"
---
# <a name="custom-vision-service-release-notes"></a>自定义影像服务发行说明

## <a name="october-9-2018"></a>2018 年 10 月 9 日
- 对象检测进入付费预览版。 现在可以使用 Azure 资源创建对象检测项目。
- 向网站添加了“移至 Azure”功能，以便更轻松地升级有限试用版项目以链接到 Azure。 资源链接的项目（F0 或 S0）。可以在产品的“设置”页上找到此信息。  
- 添加了导出到 ONNX 1.2 的功能，以支持 Windows ML 的 Windows 2018 10 月更新版本。
Bug 修复，包括带有特殊字符的 ONNX 导出。 


## <a name="august-14-2018"></a>2018 年 8 月 14 日
- 在 customvision.ai 网站上添加了“入门”小部件，以指导用户完成项目培训。 
- 进一步改进机器学习管道，使多标签项目受益（新的损失层）。

## <a name="june-28-2018"></a>2018 年 6 月 28 日
- Bug 修复和后端改进。
- 实现了图像正好只有一个标签的项目的多类分类。 在多类模式的预测中，概率将共计为 1（所有图像按指定的标签分类）。

## <a name="june-13-2018"></a>2018 年 6 月 13 日
- UX 刷新，侧重于易用性和可访问性。 
- 改进了机器学习管道，使具有大量标签的多标签项目受益。
- 修复了 TensorFlow 导出中的 bug。 启用了导出的模型版本控制，便于多次导出迭代。 

## <a name="may-7-2018"></a>2018 年 5 月 7 日
- 针对有限试用项目引入了对象检测预览版功能。
- 升级到 2.0 API
- S0 层已扩展为最多支持 250 个标记和 50,000 张图像。 
- 针对图像分类项目所用的机器学习管道做出了重大的后端改进。 在 2018 年 4 月 27 日之后训练的项目将受益于这些更新。
- 添加了将模型导出到 ONNX 的功能，可在 Windows 机器学习中使用此功能。
- 添加了将模型导出到 Dockerfile 的功能。 这样，你便可以下载项目，以生成自己的 Windows 或 Linux 容器，包括 DockerFile、TensorFlow 模型和服务代码。 
- 对于已导出到常规（精简）和地标（精简）域中 TensorFlow 的新训练的模型，[中间值现在为 (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)，目的是在所有项目之间保持一致性。 

## <a name="march-1-2018"></a>2018 年 3 月 1 日
- 推出了付费预览版，并已将其载入 Azure 门户。 现在，可将项目附加到 F0（免费）或 S0（标准）层的 Azure 资源。 引入了 S0 层项目，最多允许 100 个标记和 25,000 张图像。 
- 对机器学习管道/规范化参数做了后端更改。 客户在调整概率阈值时，可以更好地控制精度和召回率之间的权衡。 作为这些更改的一部分，CustomVision.ai 门户中的默认概率阈值已设置为 50%。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 添加了导出到 Android (TensorFlow) 的功能，此前已发布导出到 iOS (CoreML) 的功能。因此，可以导出已训练的精简模型，以便在应用程序中脱机运行。
- 添加了零售和地标“精简”域，以支持对这些域使用模型导出。
- 已发布版本 [1.2 训练 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 预测 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支持模型导出（不会将图像保存到“预测”的新预测操作），并在训练 API 中引入了批处理操作。
- UX 经过调整，现在可以查看使用了哪个域来训练迭代。
- 更新了 [C# SDK 和示例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。

