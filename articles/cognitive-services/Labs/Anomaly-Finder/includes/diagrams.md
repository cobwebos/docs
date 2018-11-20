---
title: include 文件
description: include 文件
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 506270b1828e98f14e3fe7a84b7f780e209e2669
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166111"
---
返回的数据具有预期值和默认上限和下限。 在实践中，可定义 [sensitivity] 参数，然后使用 (ExpectedValue + sensitivity * UpperMargin) 作为上限，(ExpectedValue - sensitivity * LowerMargin) 作为自行调整异常点的下限。 [sensitivity] 的值应大于 1。 下面是一些调整图。

> [!NOTE]
> 图表并非由示例应用程序生成， 而是由带有示例应用程序的单独工具创建。

![Tunning: sensitivity = 1.0](../media/sensitivity_1.png)
![Tunning: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Tunning: sensitivity = 2](../media/sensitivity_2.png)
![Tunning: sensitivity = 3.5](../media/sensitivity_3.5.png)