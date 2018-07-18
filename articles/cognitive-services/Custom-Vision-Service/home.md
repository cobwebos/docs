---
title: 自定义影像服务机器学习概述 - Azure 认知服务 | Microsoft Docs
description: 自定义影像服务是一种 Microsoft 认知服务，可用于在 Azure 平台上生成自定义的图像分类器。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087517"
---
# <a name="what-is-the-custom-vision-service"></a>什么是自定义影像服务？

自定义影像服务是一种 Microsoft 认知服务，可用于生成自定义的图像分类器。 它可以快速轻松地生成、部署和改善图像分类器。 自定义影像服务提供 REST API 和 Web 界面用于上传图像和训练分类器。

## <a name="what-does-custom-vision-service-do-well"></a>自定义影像服务最适合哪种情况？

如果要分类的项在图像中比较突出，则自定义影像服务可发挥最好的效果。 

只需使用少量的图像就能创建分类器或检测器。 对每个类使用 50 张图像就足以开始制作原型。 自定义影像服务使用的方法能够可靠地处理差异，因此，使用少量的数据就能开始制作原型。 这意味着，自定义影像服务并不是很适合用于检测细微的差异。 例如，检测质量保证场景中的细微裂纹或凹陷。

## <a name="release-notes"></a>发行说明

### <a name="may-7-2018"></a>2018 年 5 月 7 日
- 针对有限试用项目引入了对象检测预览版功能。
- 升级到 2.0 API
- S0 层已扩展为最多支持 250 个标记和 50,000 张图像。 
- 针对图像分类项目所用的机器学习管道做出了重大的后端改进。 在 2018 年 4 月 27 日之后训练的项目将受益于这些更新。
- 添加了将模型导出到 ONNX 的功能，可在 Windows 机器学习中使用此功能。
- 添加了将模型导出到 Dockerfile 的功能。 这样，你便可以下载项目，以生成自己的 Windows 或 Linux 容器，包括 DockerFile、TensorFlow 模型和服务代码。 
- 对于已导出到常规（精简）和地标（精简）域中 TensorFlow 的新训练的模型，[中间值现在为 (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)，目的是在所有项目之间保持一致性。 

### <a name="march-1-2018"></a>2018 年 3 月 1 日
- 推出了付费预览版，并已将其载入 Azure 门户。 现在，可将项目附加到 F0（免费）或 S0（标准）层的 Azure 资源。 引入了 S0 层项目，最多允许 100 个标记和 25,000 张图像。 
- 对机器学习管道/规范化参数做了后端更改。 客户在调整概率阈值时，可以更好地控制精度和召回率之间的权衡。 作为这些更改的一部分，CustomVision.ai 门户中的默认概率阈值已设置为 50%。

### <a name="december-19-2017"></a>2017 年 12 月 19 日

- 添加了导出到 Android (TensorFlow) 的功能，此前已发布导出到 iOS (CoreML) 的功能。因此，可以导出已训练的精简模型，以便在应用程序中脱机运行。
- 添加了零售和地标“精简”域，以支持对这些域使用模型导出。
- 已发布版本 [1.2 训练 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 预测 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支持模型导出（不会将图像保存到“预测”的新预测操作），并在训练 API 中引入了批处理操作。
- UX 经过调整，现在可以查看使用了哪个域来训练迭代。
- 更新了 [C# SDK 和示例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。

## <a name="next-steps"></a>后续步骤

[了解如何生成分类器](getting-started-build-a-classifier.md)
