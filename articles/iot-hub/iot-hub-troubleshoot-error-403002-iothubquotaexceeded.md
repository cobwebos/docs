---
title: 排查 Azure IoT 中心错误 403002 IoTHubQuotaExceeded
description: 了解如何修复错误 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961109"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

本文介绍**403002 IoTHubQuotaExceeded**错误的原因和解决方案。

## <a name="symptoms"></a>症状

对 IoT 中心的所有请求都将失败，并出现错误**403002 IoTHubQuotaExceeded**。 在 Azure 门户中，不会加载 IoT 中心设备列表。

## <a name="cause"></a>原因

已超过 IoT 中心的每日消息配额。 

## <a name="solution"></a>解决方案

[升级或增加 IoT 中心上的单元数](iot-hub-upgrade.md)，或等待下一个 UTC 日期进行刷新。

## <a name="next-steps"></a>后续步骤

* 若要了解如何将操作计入配额（如克隆查询和直接方法），请参阅[了解 IoT 中心定价](iot-hub-devguide-pricing.md#charges-per-operation)
* 若要设置监视每日配额使用情况，请设置一个警报，其中包含已*使用的消息总数*。 有关分步说明，请参阅[使用 IoT 中心设置指标和警报](tutorial-use-metrics-and-diags.md#set-up-metrics)