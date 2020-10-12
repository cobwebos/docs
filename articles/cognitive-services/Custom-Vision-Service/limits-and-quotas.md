---
title: 限制和配额 - 自定义影像服务
titleSuffix: Azure Cognitive Services
description: 本文介绍了不同类型的许可密钥以及自定义影像服务的限制和配额。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: pafarley
ms.openlocfilehash: b79bf5e8ead16bbdf9c69e8d7faae43fa778ab3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316573"
---
# <a name="limits-and-quotas"></a>限制和配额

自定义视觉服务有两个级别的键。 可以通过 Azure 门户注册 F0（免费）或 S0（标准）订阅。 有关定价和事务的详细信息，请参阅相应的[认知服务定价页](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。

对于 S0 项目，预计每个项目训练图像数和每个项目的标记数量将随着时间的推移而增加。

|因子|**F0**|**S0**|
|-----|-----|-----|
|项目|2|100|
|每项目训练图像数 |5,000|100,000|
|预测/月|10,000 |无限制|
|标记/项目|50|500|
|迭代 |10|10|
|每个标记的最小标记图像，分类 (50 + 建议)  |5|5|
|每个标记的最小标记图像，对象检测 (50 多位建议) |15|15|
|将预测图像存储的时间长度|30 天|30 天|
|带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|10|
|不带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)（每秒 API 调用数）|2|10|
|[其他 API 调用](https://go.microsoft.com/fwlink/?linkid=865446)（每秒事务数）|10|10|
|接受的图像类型|jpg、png、bmp、gif|jpg、png、bmp、gif|
|最小图像高度/宽度（以像素为单位）|256 (请参阅 note) |256 (请参阅 note) |
|最大图像高度/宽度（以像素为单位）|10,240|10,240|
|最大图像大小（训练图像上传） |6 MB|6 MB|
|最大图像大小（预测）|4 MB|4 MB|
|每个对象的最大区域检测培训图像|300|300|
|每个分类的最大标记图像|100|100|

> [!NOTE]
> 小于256像素的图像将被接受，但升级。
> 图像纵横比不应大于25
