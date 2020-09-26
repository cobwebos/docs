---
title: 查询要求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 68b5946cb21bef1c8979a94c8780682cfe9defa6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376421"
---
在查询中，使用 `@StartTime` 参数获取特定时间戳的指标数据。 这将替换为 `yyyy-MM-ddTHH:mm:ss` 格式的字符串。 

> [!IMPORTANT]
> 确保查询仅返回单一时间戳的指标数据。 指标顾问将针对每个时间戳运行查询，以获取相应的指标数据。 例如，对粒度为“每天”的指标的查询仅应包含一个单一时间戳，例如运行一次查询时的 `2020-06-21T00:00:00Z`。 
