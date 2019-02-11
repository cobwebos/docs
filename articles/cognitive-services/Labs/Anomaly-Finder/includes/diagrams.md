---
title: include 文件
description: include 文件
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 79ae38db73d55021572d04f693e5cb809e9bd056
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228961"
---
返回的数据具有预期值和默认上限和下限。 在实践中，可定义 [sensitivity] 参数，然后使用 (ExpectedValue + sensitivity * UpperMargin) 作为上限，(ExpectedValue - sensitivity * LowerMargin) 作为自行调整异常点的下限。 [sensitivity] 的值应大于 1。 下面是一些调整图。

> [!NOTE]
> 图表并非由示例应用程序生成， 而是由带有示例应用程序的单独工具创建。

![Tunning: sensitivity = 1.0](../media/sensitivity_1.png)
![Tunning: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Tunning: sensitivity = 2](../media/sensitivity_2.png)
![Tunning: sensitivity = 3.5](../media/sensitivity_3.5.png)
