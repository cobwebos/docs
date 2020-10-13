---
title: 排查 Azure IoT 中心错误 504101 GatewayTimeout
description: 了解如何修复错误 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81759552"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

本文介绍 **504101 GatewayTimeout** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

尝试从 IoT 中心调用直接方法到设备时，请求失败并出现错误 **504101 GatewayTimeout**。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

IoT 中心遇到错误，无法确认直接方法是否在超时前完成。

### <a name="cause-2"></a>原因 2

使用早期版 Azure IoT C# SDK（低于 1.19.0）时，可以在设备和 IoT 中心之间以静默方式删除 AMQP 链接，因为有 Bug。

## <a name="solution"></a>解决方案

### <a name="solution-1"></a>解决方案 1

发出重试请求。

### <a name="solution-2"></a>解决方案 2

升级到最新版 Azure IOT C# SDK。