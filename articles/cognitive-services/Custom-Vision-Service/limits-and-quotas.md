---
title: 定价和限制 - 自定义影像服务
titlesuffix: Azure Cognitive Services
description: 了解自定义影像服务的限制和配额。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: anroth
ms.openlocfilehash: 58109e17ed33e6af8dedf3ed8c1cc9ddf546a05e
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588663"
---
# <a name="pricing-and-limits"></a>定价和限制

自定义影像服务有三个密钥层。 有限试用版项目资源会附加到你的自定义视觉登录名（即 Azure Active Directory 帐户或 MSA 帐户）。 它们专用于服务的短期试用。 可以通过 Azure 门户注册 F0（免费）或 S0（标准）订阅。 有关定价和事务的详细信息，请参阅相应的[认知服务定价页](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。

在引入 Azure 预览版（2018 年 3 月 1 日）之前在早期免费预览期间创建的帐户将保持有限试用版的以前配额。

对于 S0 项目，预计每个项目训练图像数和每个项目的标记数量将随着时间的推移而增加。

||**有限试用版**|**F0**|**S0**|
|-----|-----|-----|-----|
|项目|2|2|100|
|每项目训练图像数，分类|5,000|5,000|50,000|
|每项目训练图像数，对象检测|5,000|5,000|10,000|
|每月预测数|10,000 |10,000|不受限制|
|每项目标记数|50|50|250|
|迭代 |10|10|10|
|每个标记的最少标记图像数，分类（建议 50+） |5|5|5|
|每个标记的最少标记图像数，对象检测（建议 50+）|15|15|15|
|将预测图像存储的时间长度|30 天|30 天|30 天|
|带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|2|10|
|不带存储的[预测](https://go.microsoft.com/fwlink/?linkid=865445)操作数（每秒事务数）|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446)（每秒 API 调用数）|2|2|10|
|[其他 API 调用](https://go.microsoft.com/fwlink/?linkid=865446)（每秒事务数）|10|10|10|
|最大图像大小（训练图像上传） |6 MB|6 MB|6 MB|
|最大图像大小（预测）|4 MB|4 MB|4 MB|


