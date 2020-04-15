---
title: 安全模块和设备孪生
description: 了解安全模块孪生的概念，以及如何在 Azure IoT 安全中心使用它们。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311450"
---
# <a name="security-module"></a>安全模块

本文介绍 IoT 的 Azure 安全中心如何使用设备孪生和模块。

## <a name="device-twins"></a>设备克隆

对于在 Azure 中生成的 IoT 解决方案，设备孪生在设备管理和流程自动化方面发挥着关键作用。

适用于 IoT 的 Azure 安全中心可与现有的 IoT 设备管理平台完全集成，使你能够管理设备的安全状态，以及利用现有的设备控制功能。 集成是通过利用 IoT 中心孪生机制实现的。

详细了解 Azure IoT 中心中的[设备孪生概念](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)。

## <a name="security-module-twins"></a>安全模块双胞胎

IoT 的 Azure 安全中心为服务中的每个设备维护一个安全模块孪生。
安全模块孪生包含解决方案中每个特定设备与设备安全性相关的所有信息。
设备安全属性保存在专用安全模块孪生中，以便更安全的通信，并启用需要较少资源的更新和维护。

请参阅[创建安全模块孪生](quickstart-create-security-twin.md)和[配置安全代理](how-to-agent-configuration.md)，了解如何创建、自定义和配置孪生。 [请参阅了解模块孪生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)，了解有关 IoT 中心中模块孪生概念的更多信息。

## <a name="see-also"></a>请参阅

- [用于 IoT 概述的 Azure 安全中心](overview.md)
- [部署安全代理](how-to-deploy-agent.md)
- [安全代理身份验证方法](concept-security-agent-authentication-methods.md)
