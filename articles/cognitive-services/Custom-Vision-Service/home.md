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
ms.openlocfilehash: d2daf7c211f9474f5636b6af69c5b700d597aa14
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285238"
---
# <a name="what-is-the-custom-vision-service"></a>什么是自定义影像服务？

自定义影像服务是一种 Microsoft 认知服务，可用于生成自定义的图像分类器。 它可以快速轻松地生成、部署和改善图像分类器。 自定义影像服务提供 REST API 和 Web 界面用于上传图像和训练分类器。

## <a name="what-does-custom-vision-service-do-well"></a>自定义影像服务最适合哪种情况？

如果要分类的项在图像中比较突出，则自定义影像服务可发挥最好的效果。 

只需使用少量的图像就能创建分类器或检测器。 对每个类使用 50 张图像就足以开始制作原型。 自定义影像服务使用的方法能够可靠地处理差异，因此，使用少量的数据就能开始制作原型。 这意味着，自定义影像服务并不是很适合用于检测细微的差异。 例如，检测质量保证场景中的细微裂纹或凹陷。

## <a name="next-steps"></a>后续步骤

[了解如何生成分类器](getting-started-build-a-classifier.md)
