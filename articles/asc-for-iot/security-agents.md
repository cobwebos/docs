---
title: 开始使用适用于 IoT 安全代理的 Azure 安全中心*微软文档
description: 开始了解、配置、部署和使用 IoT 设备上的 IoT 安全服务代理的 Azure 安全中心。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664177"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>使用适用于 IoT 设备安全代理的 Azure 安全中心开始

IoT 安全代理 Azure 安全中心提供增强的安全功能，例如监视远程连接、活动应用程序、登录事件和操作系统配置最佳实践。 通过单一服务控制设备现场威胁防护和安全状态。 

提供了 C# 和 C 中的 Linux 和 Windows 安全代理的参考体系结构。

IoT 安全代理的 Azure 安全中心处理来自设备操作系统的原始事件集合、事件聚合以降低成本以及通过设备模块孪生进行配置。 安全消息通过 IoT 中心发送到 Azure 安全中心，用于 IoT 分析服务。

使用以下工作流为 IoT 安全代理部署和测试 Azure 安全中心： 

1. [将 IoT 服务的 Azure 安全中心启用到 IoT 中心](quickstart-onboard-iot-hub.md)
1. 如果您的 IoT 中心没有注册的设备，[请注册新设备](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy)。
1. 为设备[创建 Azureiot 安全模块](quickstart-create-security-twin.md)。
1. 要在 Azure 模拟设备上安装代理，而不是在实际设备上安装，请在可用区域中[启动新的 Azure 虚拟机 （VM）。](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) 
1. [在 IoT 设备上部署 IoT 安全代理的 Azure 安全中心](how-to-deploy-linux-cs.md)，或部署新的 VM。
1. 按照[trigger_events](https://aka.ms/iot-security-github-trigger-events)的说明运行攻击的无害模拟。
1. 验证 Azure 安全中心是否发出 IoT 警报，以响应上一步的模拟攻击。 运行脚本五分钟后开始验证。
1. 使用 IoT 中心使用[日志分析](how-to-security-data-access.md)探索[警报](concept-security-alerts.md)、[建议](concept-recommendations.md)和深度潜水。 


## <a name="next-steps"></a>后续步骤

- 配置[解决方案](quickstart-configure-your-solution.md)
- [创建安全模块](quickstart-create-security-twin.md)
- 配置[自定义警报](quickstart-create-custom-alerts.md)
- [部署安全代理](how-to-deploy-agent.md)
