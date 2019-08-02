---
title: 了解适用于 IoT Edge 预览版的适用于 IoT 的 Azure 安全中心安全模块 |Microsoft Docs
description: 了解 Azure 安全中心 Azure 安全中心的体系结构和功能, 以便 IoT Edge。
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
ms.openlocfilehash: 6114fc768ad04ef812f6093d006ec9ad91b17af3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596857"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge 安全模块

> [!IMPORTANT]
> 适用于 IoT Edge 的 IoT 服务的 Azure 安全中心目前为公共预览版。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)提供了强大的功能来管理和执行边缘的业务工作流。
IoT Edge 在 IoT 环境中发挥作用的关键部分使其对于恶意执行组件尤其有吸引力。

Azure 安全中心 IoT 安全模块为 IoT Edge 设备提供了一个全面的安全解决方案。
用于 IoT 模块的 Azure 安全中心收集、聚合和分析来自你的操作系统和容器系统的原始安全数据到可操作的安全建议和警报。

与用于 IoT 设备的 IoT 安全代理的 Azure 安全中心类似, IoT Edge 模块的 Azure 安全中心通过其模块克隆可高度自定义。
有关详细信息, 请参阅[配置代理](how-to-agent-configuration.md)。

适用于 IoT 的 Azure 安全中心安全模块 IoT Edge 提供以下功能:

- 从基础操作系统 (Linux) 和 IoT Edge 容器系统收集原始安全事件。
  
  请参阅[Azure 安全中心以获取 IoT 代理配置](how-to-agent-configuration.md), 详细了解可用的安全数据收集器。

- 分析 IoT Edge 部署清单。

- 将原始安全事件聚合到通过[IoT Edge 集线器](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)发送的消息中。

- 通过使用安全模块克隆来删除配置。

  有关详细信息, 请参阅[配置 Azure 安全中心的 IoT 代理](how-to-agent-configuration.md)。

适用于 IoT 的 Azure 安全中心安全模块 IoT Edge 在 IoT Edge 下的特权模式下运行。
要使模块能够监视操作系统和其他 IoT Edge 模块, 特权模式是必需的。

## <a name="module-supported-platforms"></a>模块支持的平台

适用于 IoT Edge 的用于 IoT 安全模块的 Azure 安全中心目前仅适用于 Linux。 

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心 Azure 安全中心的体系结构和功能, 用于 IoT Edge 的 IoT 安全模块。

若要继续开始使用 Azure 安全中心进行 IoT 部署, 请使用以下文章:

- 部署[IoT Edge 的安全模块](how-to-deploy-edge.md)
- 了解如何[配置安全模块](how-to-agent-configuration.md)
- 查看 Azure 安全中心以了解 IoT[服务先决条件](service-prerequisites.md)
- 了解如何[在 Iot 中心为 iot 服务启用 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 详细了解[Azure 安全中心提供的 IOT 常见问题解答](resources-frequently-asked-questions.md)