---
title: 限制和配额 IoT 即插即用 |Microsoft Docs
description: 了解使用 IoT 即插即用时适用的限制、配额和限制。
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577979"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>IoT 即插即用限制、配额和限制

本文介绍 IoT 即插即用特定的限制、配额和限制。 还适用于现有的 [IoT 中心配额和限制](../iot-hub/iot-hub-devguide-quotas-throttling.md) 。

## <a name="iot-hub"></a>IoT 中心

以下限制和配额适用于 IoT 中心：

| 限制、限制和限制 | “值” | 说明 |
|-----|-----|-----|
| 每个中心可注册的接口数 | 1500 ||
| 组件名称的最大大小 | 1-64 个字符 | 允许的字符： a-z、a-z、0-9 (不是第一个字符) ，而下划线 (不是第一个或最后一个字符) 。 |
| 属性名的最大大小 | 1-64 个字符 | 允许的字符： a-z、a-z、0-9 (不是第一个字符) ，而下划线 (不是第一个或最后一个字符) 。 |
| 属性值的最大大小 | 与数字孪生定义语言[属性](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)相同 | 深度为5个级别，并且不能是数组或包含数组的任何复杂架构 |
| 命令名称的最大大小 | 1-64 个字符 | 允许的字符： a-z、a-z、0-9 (不是第一个字符) ，而下划线 (不是第一个或最后一个字符) 。|
| 设备孪生的大小 | 与[IoT 中心限制](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size)相同 ||

## <a name="parser-library"></a>分析器库

分析器库遵循适用于 [数字孪生定义语言](https://github.com/Azure/opendigitaltwins-dtdl)的限制。

## <a name="next-steps"></a>后续步骤

建议的下一步是查看 [IoT 即插即用的体系结构](concepts-architecture.md)。
