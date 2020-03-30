---
title: 了解 IoT 边缘 IoT 安全模块的 Azure 安全中心*微软文档
description: 了解 IoT 边缘 IoT 安全模块的 Azure 安全中心的体系结构和功能。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 148c68234a937efde554ef00a6014cdc1a350f34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315895"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT 边缘安全模块

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)提供了强大的功能，可在边缘管理和执行业务工作流。
IoT Edge 在 IoT 环境中扮演的关键角色使其对恶意参与者特别有吸引力。

面向 IoT 安全的 Azure 安全中心模块为您的 IoT 边缘设备提供了全面的安全解决方案。
IoT 模块的 Azure 安全中心会收集、聚合和分析来自操作系统和容器系统的原始安全数据，并将其转化为可操作的安全建议和警报。

与适用于 IoT 设备的 IoT 安全代理的 Azure 安全中心类似，IoT 边缘 Azure 安全中心模块通过其模块孪生高度可自定义。
请参阅[配置代理](how-to-agent-configuration.md)以了解更多信息。

适用于 IoT 边缘的 IoT 安全模块的 Azure 安全中心提供以下功能：

- 从基础操作系统 （Linux） 和 IoT 边缘容器系统收集原始安全事件。
  
  请参阅[Azure 安全中心进行 IoT 代理配置](how-to-agent-configuration.md)，了解有关可用安全数据收集器的更多信息。

- IoT 边缘部署清单的分析。

- 将原始安全事件聚合到通过[IoT 边缘中心](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)发送的消息中。

- 使用安全模块孪生来删除配置。

  请参阅[为 IoT 代理配置 Azure 安全中心](how-to-agent-configuration.md)以了解更多信息。

IoT 边缘 IoT 安全模块的 Azure 安全中心在 IoT 边缘下的特权模式下运行。
需要特权模式才能允许模块监视操作系统和其他 IoT 边缘模块。

## <a name="module-supported-platforms"></a>模块支持的平台

IoT 边缘 IoT 安全模块的 Azure 安全中心目前仅适用于 Linux。 

## <a name="next-steps"></a>后续步骤

在本文中，您了解了 IoT 边缘 IoT 安全模块的 Azure 安全中心的体系结构和功能。

要继续使用用于 IoT 部署的 Azure 安全中心，请使用以下文章：

- [为 IoT 边缘部署安全模块](how-to-deploy-edge.md)
- 了解如何[配置安全模块](how-to-agent-configuration.md)
- 查看 Azure 安全中心，查看 IoT[服务先决条件](service-prerequisites.md)
- 了解如何在[IoT 中心启用 IoT 服务的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 从[IoT 常见问题解答的 Azure 安全中心](resources-frequently-asked-questions.md)了解有关该服务