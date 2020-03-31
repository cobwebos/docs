---
title: 排查 Azure IoT 中心错误 429001 ThrottlingException
description: 了解如何修复错误 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960693"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

本文介绍 **429001 ThrottlingException** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

对 IoT 中心的请求失败，出现错误 **429001 ThrottlingException**。

## <a name="cause"></a>原因

已超过所请求操作的 IoT 中心[限制](./iot-hub-devguide-quotas-throttling.md)。

## <a name="solution"></a>解决方案

根据上面指定的限制比较你的**“遥测消息发送尝试次数”指标，检查你是否达到了限制 还可以检查“限制错误数”** 指标。 有关适用于 IoT 中心的这些指标和其他指标的详细信息，请参阅 [IoT 中心指标及其用法](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)。

只有在违反限制的时间过长的情况下，IoT 中心才返回 429 ThrottlingException。 这样做以后，如果 IoT 中心获得突发流量，则不会丢弃消息。 在此期间，IoT 中心会按操作受限速率处理消息。如果积压了太多流量，则处理起来可能需要很长时间。 若要了解详细信息，请参阅 [IoT 中心流量调整](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)。

## <a name="next-steps"></a>后续步骤

如果遇到配额限制或限制限制，请考虑[向上扩展 IoT 中心](./iot-hub-scaling.md)。