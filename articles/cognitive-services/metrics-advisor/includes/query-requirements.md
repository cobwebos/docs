---
title: 查询要求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934692"
---
在查询中，使用 `@StartTime` 参数获取特定时间戳的指标数据。 这将替换为 `yyyy-MM-ddTHH:mm:ss` 格式的字符串。 

> [!IMPORTANT]
> 确保查询仅返回单一时间戳的指标数据。 指标顾问将针对每个时间戳运行查询，以获取相应的指标数据。 例如，对粒度为“每天”的指标的查询仅应包含一个单一时间戳，例如运行一次查询时的 `2020-06-21T00:00:00Z`。 
