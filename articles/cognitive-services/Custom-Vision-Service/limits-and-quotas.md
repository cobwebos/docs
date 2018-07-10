---
title: 自定义影像服务的限制和配额 - Azure 认知服务 | Microsoft Docs
description: 了解 Azure 认知服务自定义影像服务的限制和配额
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366618"
---
# <a name="limits-and-quotas"></a>限制和配额

自定义影像服务有三个密钥层。 F0 和 S0 资源是通过 Azure 门户获取的。 有关定价和事务定义的详细信息，请参阅[定价页面](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。  F0 项目可以升级为 S0 项目。

有限试用版项目资源会附加到你的自定义视觉登录名（即 AAD 帐户或 MSA 帐户）。它们专用于服务的短期试用。  在引入 Azure 预览版（2018 年 3 月 1 日）之前在早期免费预览版中创建的帐户将保持有限试用版的以前配额。 

||**有限试用版**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|项目|2|2|100|
|每项目训练图像数|5,000|5,000|50,000|
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
|最大图像大小（训练图像上传） |6MB|6MB|6MB|
|最大图像大小（预测）|4MB|4MB|4MB|

对于 S0 项目，应当会随时间推移提高针对*每项目训练图像数*和*每项目标记数*的限制。 
