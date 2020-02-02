---
title: 排查 Azure IoT 中心错误 400027 ConnectionForcefullyClosedOnNewConnection
description: 了解如何修复错误 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960524"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

本文介绍**400027 ConnectionForcefullyClosedOnNewConnection**错误的原因和解决方案。

## <a name="symptoms"></a>症状

设备到云的硬编码操作（如读取或修补程序报告属性）或直接方法调用失败，错误代码为**400027**。

## <a name="cause"></a>原因

另一个客户端使用相同的凭据创建了到 IoT 中心的新连接，因此 IoT 中心关闭了上一个连接。 IoT 中心不允许多个客户端使用相同的一组凭据进行连接。

## <a name="solution"></a>解决方案

确保每个客户端使用其自己的标识连接到 IoT 中心。