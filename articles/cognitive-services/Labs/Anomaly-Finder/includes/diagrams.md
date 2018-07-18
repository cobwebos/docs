---
title: include 文件
description: include 文件
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366034"
---
返回的数据具有预期值和默认上限和下限。 在实践中，可定义 [sensitivity] 参数，然后使用 (ExpectedValue + sensitivity * UpperMargin) 作为上限，(ExpectedValue - sensitivity * LowerMargin) 作为自行调整异常点的下限。 [sensitivity] 的值应大于 1。 下面是一些调整图。

> [!NOTE]
> 图表并非由示例应用程序生成， 而是由带有示例应用程序的单独工具创建的。

![Tunning: sensitivity = 1.0](../media/sensitivity_1.png)
![Tunning: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Tunning: sensitivity = 2](../media/sensitivity_2.png)
![Tunning: sensitivity = 3.5](../media/sensitivity_3.5.png)