---
title: 用于 IoT 代理的 Azure Defender 常见问题
description: 查找有关 Azure Defender IoT 代理的常见问题的解答。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.openlocfilehash: 9862519a2003eb373c43fef1b660986a8d830327
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094273"
---
# <a name="azure-defender-for-iot-agent-frequently-asked-questions"></a>用于 IoT 代理的 Azure Defender 常见问题

本文提供了有关 IoT 代理 Defender 的常见问题和解答列表。


## <a name="do-i-have-to-install-an-embedded-security-agent"></a>是否必须安装嵌入式安全代理？

对于启用 IoT 的 IoT，IoT 设备上的代理安装不是必需的。 可以选择以下三个选项，根据选择获取不同级别的安全监视和管理功能：

- 被动的非侵害性 (无代理) 使用 NTA (网络流量分析) 传感器的部署，监视并提供深入了解 IoT
- 无需修改即可安装适用于 IoT embedded 安全代理的 Defender。 此选项提供有关设备行为和访问的最高级别增强安全见解。

- 创建自己的代理并实现用于 IoT 安全消息架构的 Defender。 使用此选项，可以在设备安全代理顶部使用 IoT 分析工具。

- 不在 IoT 设备上安装安全代理。 此选项可启用 IoT 中心通信监视，缩减了安全监视和管理功能。

## <a name="what-does-the-defender-for-iot-agent-do"></a>IoT 代理的 Defender 有什么作用？

用于 IoT 代理的 Defender 通过扫描配置) ，处理 & 连接性，为设备配置、行为和访问 (提供设备级别的威胁范围。 用于 IoT security agent 的 Defender 不会扫描与业务相关的数据或活动。

适用于 IoT security agent 的 Defender 是开放源代码，在 GitHub 上的32位和64位 Windows 和 Linux 版本中提供： https://github.com/Azure/Azure-IoT-Security 。


## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理的依赖项和先决条件是什么？

Defender for IoT 支持多种平台。 请参阅[受支持的设备平台](how-to-deploy-agent.md)以验证对特定设备的支持。

## <a name="which-data-is-collected-by-the-agent"></a>代理收集哪些数据？

代理会收集连接、访问、防火墙配置、进程列表和 OS 基线。

## <a name="how-much-data-will-the-agent-generate"></a>代理将生成多少数据？

代理数据生成由设备、应用程序、连接类型和客户代理配置驱动。 由于设备与 IoT 解决方案之间的差异很大，因此建议首先在实验室或测试设置中部署代理，以观察、了解并设置符合需求的特定配置，同时度量所生成的数据量。 启动该服务后，IoT 代理的 Defender 为优化代理吞吐量提供操作建议，以帮助你进行配置和自定义过程。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理消息是否使用 IoT 中心的配额？

是的。 代理传输的数据会计入 IoT 中心配额。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下来要做什么？ 我安装了一个代理，但没有看到任何活动或日志...

1. 检查[代理类型是否适合设备的指定 OS 平台](how-to-deploy-agent.md)

1. 确认[代理正在设备上运行](how-to-agent-configuration.md)。

1. 检查在 IoT 中心内[服务是否已成功启用](quickstart-onboard-iot-hub.md)为“安全性”。

1. 检查设备是否已 [在 Iot 中心中配置了用于 iot 的 Defender 模块](quickstart-create-security-twin.md)。

如果活动或日志仍不可用，请联系你的 Defender for IoT 合作伙伴获取更多帮助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet 连接停止工作时会发生什么情况？

只要设备正在运行，传感器和代理就会继续运行并存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备重新连接时，安全消息会恢复发送。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理是否可以影响设备的性能或其他已安装的软件？

代理如同任何其他应用程序/进程一样使用计算机资源，不应中断正常的设备活动。 运行代理的设备上的资源消耗与其设置和配置相关联。 建议在生产环境中尝试部署之前，在包含的环境中测试代理配置，以及与其他 IoT 应用程序和功能的互操作性。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我要在设备上进行一些维护。 是否可以关闭代理？

无法关闭代理。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>是否有方法可以测试代理是否在正常工作？

如果代理停止通信或无法发送安全消息，则会生成“设备无提示”警报。



## <a name="next-steps"></a>后续步骤

若要详细了解如何开始将 Defender 用于 IoT，请参阅以下文章：

- 阅读用于 IoT 的 Defender [概述](overview.md)
- 验证[服务先决条件](service-prerequisites.md)
- 了解有关如何[入门](getting-started.md)的详细信息
- 了解 [用于 IoT 的 Defender 安全警报](concept-security-alerts.md)
