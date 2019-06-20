---
title: 限制和配额 - 自定义影像服务
titlesuffix: Azure Cognitive Services
description: 了解自定义影像服务的限制和配额。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 9cff5fdac39be2338305cd37a4b2328a28a48255
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269263"
---
# <a name="limits-and-quotas"></a>限制和配额

有两个层的自定义影像服务的密钥。 可以通过 Azure 门户注册 F0（免费）或 S0（标准）订阅。 有关定价和事务的详细信息，请参阅相应的[认知服务定价页](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。

对于 S0 项目，预计每个项目训练图像数和每个项目的标记数量将随着时间的推移而增加。

||**F0**|**S0**|
|-----|-----|-----|
|项目|2|100|
|每项目训练图像数 |5,000|100,000|
|每月预测数|10,000 |不受限制|
|每项目标记数|50|500|
|迭代 |10|10|
|带有标记的每个标记，分类 （超过 50 个建议） 的图像的最小值 |5|5|
|带有标记的每个标记，对象检测 （超过 50 个建议） 的图像的最小值|15|15|
|将预测图像存储的时间长度|30 天|30 天|
|带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|10|
|不带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)（每秒 API 调用数）|2|10|
|[其他 API 调用](https://go.microsoft.com/fwlink/?linkid=865446)（每秒事务数）|10|10|
|最大图像大小（训练图像上传） |6 MB|6 MB|
|最大图像大小（预测）|4 MB|4 MB|
|每个对象检测训练图像的最大区域|200|200|
|每个分类图像的最大标记|30|30|
