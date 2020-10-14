---
title: 数据架构要求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: c754efef02cdad6edbf047c5de9f1af6d758f137
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043184"
---
指标监视器是一种用于时序异常检测、诊断和分析的服务。 作为由 AI 提供支持的服务，它使用你的数据来训练所使用的模型。 服务接受具有以下各列的聚合数据表：

* **度量值**（必需）：一个或多个包含数值的列。
* **时间戳**（可选）：零个或一个类型为 `DateTime` 或 `String` 的列。 如果未设置此列，则时间戳将设置为每个引入周期的开始时间。 将时间戳格式设置为：`yyyy-MM-ddTHH:mm:ssZ`。 
  * 时间戳应与指标的粒度匹配。例如，每日指标应确保时间戳上的小时、分钟和秒标记为 `00:00:00`。
* **维度**（可选）：列可以是任意数据类型。 处理大量的列和值时要格外小心，应避免处理过多的维度。

> [!Note]
> 对于每个指标，每个度量值只能有一个时间戳，对应于一个维度组合。 在载入之前对数据进行聚合，或者使用查询来指定要引入的数据。