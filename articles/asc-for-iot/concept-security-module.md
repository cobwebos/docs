---
title: 了解 IoT 安全模块孪生预览 ASC |Microsoft Docs
description: 了解有关安全模块孪生以及如何使用它们在 ASC 中对 IoT 的概念。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d766b17c9d49792d2e8192a952e8e6e559a8acd3
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579370"
---
# <a name="security-module"></a>安全模块

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍了如何 ASC iot 使用设备孪生和模块。 

## <a name="device-twins"></a>设备克隆

对于在 Azure 中构建的 IoT 解决方案，设备孪生播放设备管理和流程自动化中的关键角色。  

ASC 为 IoT 提供了完全集成与你现有的 IoT 设备管理平台，使您能够管理你设备的安全状态以及进行使用的现有设备控制功能。 通过使用与 IoT 中心的集成机制的克隆。  

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