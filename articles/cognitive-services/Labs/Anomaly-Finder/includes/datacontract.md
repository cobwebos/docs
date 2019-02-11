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
ms.openlocfilehash: 3cc0e521e43f6855397a19fe34fce99da3e20494
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228844"
---
借助[异常查找器 API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder) 可将 JSON 格式的时序数据上传到 API 终结点，然后从 API 响应中读取结果。 可上传时序数据，每个数据点包括：  
* 时间戳 - 数据点的时间戳。 请确保时间戳使用 UTC 日期时间字符串，例如“2017-08-01T00:00:00Z”
* 值 - 数据点的度量值

结果包括：
* Period - 用于检测异常点的 API 的使用周期
* WarningText - 可能的警告信息
* ExpectedValue - 基于模型的学习预测的值
* IsAnomaly - 用于显示两个方向（峰值和下降）的数据点是否异常的结果
* IsAnomaly_Neg - 用于显示反方向（下降）数据点是否异常的结果
* IsAnomaly_Pos - 用于指示正向（峰值）数据点是否异常的结果
* UpperMargin - ExpectedValue 和 UpperMargin 的总和用于确定数据点正常范围的上限
* LowerMargin - (ExpectedValue - LowerMargin) 用于确定下限，数据点在该范围内仍被视为正常

可在[此处](../apiref.md)找到数据协定的详细信息。

