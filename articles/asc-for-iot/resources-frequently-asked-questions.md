---
title: 常见问题
description: 查找有关适用于 IoT 的 Azure 安全中心功能和服务的常见问题的解答。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1aeab1a7dcdf2b12efc268ed0d47834b24d34b87
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734971"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>适用于 IoT 的 Azure 安全中心常见问题

本文提供了有关适用于 IoT 的 Azure 安全中心的常见问题和解答列表。

## <a name="does-azure-provide-support-for-iot-security"></a>Azure 是否为 IoT 安全提供支持？

Azure 通过 Azure 安全中心提供用于监视和管理 IoT 安全性的集成视图，作为总体安全解决方案的一部分。 如果你是应用程序开发人员，则可以使用 IoT 中心视图来管理 IoT 应用程序安全性。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>什么是 Azure 独特的 IoT 安全性价值主张？

适用于 IoT 的 Azure 安全中心使企业能够将其现有网络安全视图扩展到整个 IoT 解决方案。 Azure 提供了业务解决方案的端到端视图，使你能够根据企业安全状况和收集的数据来进行业务相关操作和决策。 使用 Azure IoT、Azure IoT Edge 和 Azure 安全中心的组合安全性使你可以创建具有所需安全性的所需解决方案。

## <a name="who-is-azure-security-center-for-iot-made-for"></a>适用于 IoT 的 Azure 安全中心面向哪些人员？

适用于 IoT 的 Azure 安全中心集成在 Azure IoT 中心安全性内，为日常业务解决方案安全操作提供管理。 适用于 IoT 的 Azure 安全中心还集成到 Azure 安装中心功能中，提供用于监视和管理 IoT 安全性的集成视图，作为总体安全解决方案的一部分。

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>如何将适用于 IoT 的 Azure 安全中心与竞争对手进行比较？

其他解决方案提供了一组允许客户创建自己的解决方案的功能，而适用于 IoT 的 Azure 安全中心提供了独特的端到端 IoT 安全性解决方案，可全面查看所有相关 Azure 资源的安全性。 Azure 可实现快速部署并与 IoT 中心模块孪生完整集成，以便与现有设备管理工具轻松集成。

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>是否必须是 Azure 安全中心客户才能使用此服务？

不需要，但建议这样做。 如果不使用 Azure 安全中心，则适用于 IoT 的 Azure 安全中心会收到有限的连接资源数据，并对潜在攻击面、威胁和潜在攻击进行有限的分析。

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>是否必须成为 Azure IoT 客户？

是的。 适用于 IoT 的 Azure 安全中心依赖于 Azure IoT 连接和基础结构。

## <a name="do-i-have-to-install-an-agent"></a>是否必须安装代理？

不是必须在 IoT 设备上安装代理才能启用适用于 IoT 的 Microsoft Azure 安全中心。 可以选择以下三个选项，根据选择获取不同级别的安全监视和管理功能：

1. 可以在修改或不修改的情况下安装适用于 IoT 的 Azure 安全中心安全代理。 此选项提供有关设备行为和访问的最高级别增强安全见解。

1. 创建自己的代理并实现适用于 IoT 的 Microsoft Azure 安全中心安全消息架构。 通过此选项可以在设备安全代理上使用适用于 IoT 的 Microsoft Azure 安全中心分析工具。

1. 不在 IoT 设备上安装安全代理。 此选项可启用 IoT 中心通信监视，缩减了安全监视和管理功能。

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>适用于 IoT 的 Azure 安全中心代理有什么作用？

适用于 IoT 的 Azure 安全中心代理针对设备配置、行为和访问（通过扫描配置）、进程和连接提供设备级别威胁覆盖范围。 适用于 IoT 的 Azure 安全中心安装代理不扫描与业务相关的数据或活动。

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>在何处可以获得适用于 IoT 的 Azure 安全中心安全代理？

适用于 IoT 的 Azure 安全中心安全代理是开放源代码的，在 GitHub 上提供 32 位和 64 位 Windows 与 Linux 版本： https://github.com/Azure/Azure-IoT-Security 。

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>适用于 IoT 的 Azure 安全中心代理安装在何处？

可在 GitHub 中找到详细安装和代理部署信息： https://github.com/Azure/Azure-IoT-Security 。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理的依赖项和先决条件是什么？

适用于 IoT 的 Azure 安全中心支持各种平台。 请参阅[受支持的设备平台](how-to-deploy-agent.md)以验证对特定设备的支持。

## <a name="which-data-is-collected-by-the-agent"></a>代理收集哪些数据？

代理会收集连接、访问、防火墙配置、进程列表和 OS 基线。

## <a name="how-much-data-will-the-agent-generate"></a>代理将生成多少数据？

代理数据生成由设备、应用程序、连接类型和客户代理配置驱动。 由于设备与 IoT 解决方案之间的差异很大，因此建议首先在实验室或测试设置中部署代理，以观察、了解并设置符合需求的特定配置，同时度量所生成的数据量。 启动服务之后，适用于 IoT 的 Azure 安全中心代理可提供操作建议来优化代理吞吐量，以帮助进行配置和自定义过程。

## <a name="how-can-i-control-my-billing"></a>如何控制我的帐单？

适用于 IoT 的 Azure 安全中心提供可配置的代理扫描、数据缓冲区，以及创建自定义警报的功能，这些警报可增加或减少代理生成的数据量。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理消息是否使用 IoT 中心的配额？

是的。 代理传输的数据会计入 IoT 中心配额。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下来要做什么？ 我安装了一个代理，但没有看到任何活动或日志...

1. 检查[代理类型是否适合设备的指定 OS 平台](how-to-deploy-agent.md)

1. 确认[代理正在设备上运行](how-to-agent-configuration.md)。

1. 检查在 IoT 中心内[服务是否已成功启用](quickstart-onboard-iot-hub.md)为“安全性”。

1. 检查是否[在 IoT 中心内使用适用于 IoT 的 Azure 安全中心模块配置](quickstart-create-security-twin.md)了设备。

如果活动或日志仍不可用，请联系适用于 IoT 的 Azure 安全中心合作伙伴获取更多帮助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet 连接停止工作时会发生什么情况？

只要设备正在运行，代理便会继续运行并存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备重新连接时，安全消息会恢复发送。

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>如果重启了设备，则安全代理是否会自行恢复？

安全代理设计为在每次设备重启时自动重新运行。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理是否可以影响设备的性能或其他已安装的软件？

代理如同任何其他应用程序/进程一样使用计算机资源，不应中断正常的设备活动。 运行代理的设备上的资源消耗与其设置和配置相关联。 建议在生产环境中尝试部署之前，在包含的环境中测试代理配置，以及与其他 IoT 应用程序和功能的互操作性。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我要在设备上进行一些维护。 是否可以关闭代理？

无法关闭代理。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>是否有方法可以测试代理是否在正常工作？

如果代理停止通信或无法发送安全消息，则会生成“设备无提示”警报。

## <a name="can-i-create-my-own-alerts"></a>我是否可以创建自己的警报？

是的。 可以对预先确定的行为集（例如 IP 地址和开放端口）设置自定义警报。 请参阅[创建自定义警报](quickstart-create-custom-alerts.md)以详细了解自定义警报以及如何创建它们。

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>可以在何处查看日志？ 是否可以自定义日志？

- 使用连接的 Log Analytics 工作区查看警报和建议。 在该工作区中配置存储大小和持续时间。

- 安全代理中的原始数据也可以存储在 Log Analytics 帐户中。 更改此选项的配置之前，请考虑大小、持续时间、存储要求和关联成本。

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>为什么应将适用于 IoT 的 Azure 安全中心添加到模块标识？ 用途

适用于 IoT 的 Azure 安全中心模块用于代理配置和管理。

## <a name="next-steps"></a>后续步骤

若要了解有关如何开始使用适用于 IoT 的 Azure 安全中心的详细信息，请参阅以下文章：

- 阅读适用于 IoT 的 Azure 安全中心[概述](overview.md)
- 验证[服务先决条件](service-prerequisites.md)
- 了解有关如何[入门](getting-started.md)的详细信息
- 了解[适用于 IoT 的 Azure 安全中心安全警报](concept-security-alerts.md)
