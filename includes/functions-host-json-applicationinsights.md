---
title: include 文件
description: include 文件
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279558"
---
控制 [Application Insights 中的采样功能](../articles/azure-functions/functions-monitoring.md#configure-sampling)。

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|属性  |默认 | 说明 |
|---------|---------|---------| 
|isEnabled|true|启用或禁用采样。| 
|maxTelemetryItemsPerSecond|5|开始采样所要达到的阈值。| 
