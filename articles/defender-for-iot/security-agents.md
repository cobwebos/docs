---
title: 安全代理概述
description: 开始在 IoT 设备上了解、配置、部署和使用适用于 IoT 安全服务代理的 Azure Defender。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 888fdbb2352c4ac972eac8b24c29d22ce973e04d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934560"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Azure Defender for IoT 设备安全代理入门

用于 IoT 安全代理的 Defender 提供增强的安全功能，如监视远程连接、活动应用程序、登录事件和操作系统配置最佳实践。 使用单个服务控制设备字段威胁防护和安全状况。

同时还提供了适用于 Linux 和 Windows 安全代理的参考体系结构。

用于 IoT 安全代理的 Defender 可处理设备操作系统的原始事件收集、事件聚合以降低成本，并通过设备模块进行配置。 安全消息通过 IoT 中心发送到 IoT analytics 服务的 Defender。

使用以下工作流来部署和测试用于 IoT 安全代理的 Defender：

1. [为 IoT 中心启用用于 IoT 服务的 Defender](quickstart-onboard-iot-hub.md)
1. 如果 IoT 中心没有已注册的设备，则 [注册新设备](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)。
1. 为设备[创建 azureiotsecurity 安全模块](quickstart-create-security-twin.md)。
1. 若要在 Azure 模拟设备上安装代理，而不是在实际设备上安装，请 [启动新的 Azure 虚拟机 (VM) ](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 在可用区域中。
1. 在 IoT 设备或新 VM 上[部署用于 iot security agent 的 Defender](how-to-deploy-linux-cs.md) 。
1. 按照 [trigger_events](https://aka.ms/iot-security-github-trigger-events) 的说明运行攻击的无害模拟。
1. 验证用于 IoT 警报的 Defender，以响应上一步中的模拟攻击。 运行脚本5分钟后开始验证。
1. 使用 IoT 中心浏览 [警报](concept-security-alerts.md)、 [建议](concept-recommendations.md)和 [深入探讨 Log Analytics](how-to-security-data-access.md) 。

## <a name="next-steps"></a>后续步骤

- 配置 [解决方案](quickstart-configure-your-solution.md)
- [创建安全模块](quickstart-create-security-twin.md)
- 配置 [自定义警报](quickstart-create-custom-alerts.md)
- [部署安全代理](how-to-deploy-agent.md)
