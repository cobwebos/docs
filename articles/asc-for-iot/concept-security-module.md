---
title: 了解 Azure 安全中心的 IoT 安全模块孪生预览 |Microsoft Docs
description: 了解有关安全模块孪生以及如何使用它们在 Azure 安全中心对 IoT 的概念。
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: c5814b188c73ea03094d7dae565e40ca09e705c2
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862431"
---
# <a name="security-module"></a>安全模块

> [!IMPORTANT]
> IoT 的 azure 安全中心当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍了 iot 的 Azure 安全中心 (ASC) 如何使用设备孪生和模块。 

## <a name="device-twins"></a>设备克隆

对于在 Azure 中生成的 IoT 解决方案，设备孪生在设备管理和流程自动化方面发挥着关键作用。  

ASC for IoT 可与现有的 IoT 设备管理平台完全集成，使你能够管理设备的安全状态，以及利用现有的设备控制功能。 通过使用与 IoT 中心的集成机制的克隆。  

了解有关这一概念的详细信息[设备孪生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)Azure IoT 中心内。 

## <a name="security-module-twins"></a>安全模块孪生

适用于 IoT 的 ASC 维护服务中的每个设备一个安全模块孪生。
安全模块孪生解决方案中承载的所有信息与每个特定设备的设备安全性。
中的专用的安全模块孪生更安全的通信以及用于启用更新和维护需要较少的资源的维护设备安全属性。  

请参阅[创建安全模块孪生](quickstart-create-security-twin.md)并[配置安全代理](how-to-agent-configuration.md)若要了解如何创建、 自定义和配置孪生。 请参阅[了解模块孪生](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins)若要了解有关在 IoT 中心模块孪生的概念的详细信息。 
 

## <a name="see-also"></a>另请参阅
- [ASC IoT 预览版](overview.md)
- [部署安全代理](how-to-deploy-agent.md)
- [安全代理身份验证方法](concept-security-agent-authentication-methods.md)