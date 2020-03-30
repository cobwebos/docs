---
title: 适用于 IoT 的 Azure 安全中心的常见问题*微软文档
description: 查找有关 IoT 功能和服务的 Azure 安全中心常见问题的解答。
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
ms.openlocfilehash: 639a3f89e470a832279add8d2ed7cf49441611f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571803"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>适用于 IoT 的 Azure 安全中心常见问题  

本文提供了有关 IoT Azure 安全中心的常见问题和答案的列表。 

## <a name="does-azure-provide-support-for-iot-security"></a>Azure 是否支持 IoT 安全性？

Azure 提供了一个集成视图，用于监视和管理 IoT 安全性，作为通过 Azure 安全中心提供整体安全解决方案的一部分。 如果您是应用程序开发人员，则可以使用 IoT 中心视图来管理 IoT 应用程序安全性。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Azure 对 IoT 安全性的独特价值主张是什么？

面向 IoT 的 Azure 安全中心使企业能够将其现有的网络安全视图扩展到其整个 IoT 解决方案。 Azure 提供了业务解决方案的端到端视图，使您能够根据企业安全状况和收集的数据执行与业务相关的操作和决策。 使用 Azure IoT、Azure IoT 边缘和 Azure 安全中心组合安全性使您能够使用所需的安全性创建所需的解决方案。

## <a name="who-is-azure-security-center-for-iot-made-for"></a>为谁制作的 IoT Azure 安全中心？ 

IoT 的 Azure 安全中心集成在 Azure IoT 中心安全中，并为日常业务解决方案安全操作提供管理。 Azure IoT 安全中心还集成到 Azure 安全中心功能中，并提供集成视图，作为整体安全解决方案的一部分进行监视和管理 IoT 安全。

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>Azure IoT 安全中心与竞争对手相比如何？

虽然其他解决方案提供了一组功能，允许客户创建自己的解决方案，但 IoT Azure 安全中心提供了独特的端到端 IoT 安全解决方案，可在所有相关 Azure 的安全性范围内提供宽视图资源。 Azure 支持快速部署和与 IoT 中心模块孪生完全集成，以便轻松与现有设备管理工具集成。

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>使用此服务，我必须是 Azure 安全中心客户吗？

否，但建议使用。 如果没有 Azure 安全中心，IoT Azure 安全中心接收有限的连接资源数据，并提供对潜在攻击面、威胁和潜在攻击的有限分析。 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>我必须是 Azure IoT 客户吗？

是的。 IoT 的 Azure 安全中心依赖于 Azure IoT 连接和基础结构。

## <a name="do-i-have-to-install-an-agent"></a>我必须安装代理吗？

为了启用适用于 IoT 的 Microsoft Azure 安全中心，在 IoT 设备上安装代理不是强制性的。 您可以选择以下三个选项，根据您的选择获得不同级别的安全监视和管理功能：

1. 安装 IoT 安全代理的 Azure 安全中心，无论是否进行修改。 此选项提供对设备行为和访问的最高级别增强安全见解。

2. 创建您自己的代理并实现适用于 IoT 安全消息架构的 Microsoft Azure 安全中心。 此选项允许在设备安全代理之上使用 Microsoft Azure 安全中心进行 IoT 分析工具。

3. IoT 设备上没有安全代理安装。 此选项支持 IoT 中心通信监视，降低了安全监视和管理功能。 

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>IoT 代理的 Azure 安全中心的作用是什么？

IoT 代理 Azure 安全中心为设备配置、行为和访问（通过扫描配置）、进程&连接提供设备级威胁覆盖。 IoT 安全代理的 Azure 安全中心不扫描与业务相关的数据或活动。

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>在哪里可以获取适用于 IoT 安全代理的 Azure 安全中心？

IoT 安全代理的 Azure 安全中心是开源的，在 GitHub 上提供 32 位和 64https://github.com/Azure/Azure-Security-IoT位 Windows 和 Linux 版本： .

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>IoT 代理的 Azure 安全中心安装在哪里？ 

有关安装和代理部署信息，请参阅 GitHub： https://github.com/Azure/Azure-IoT-Security。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理的依赖性和先决条件是什么？

IoT 的 Azure 安全中心支持各种平台。 请参阅[支持的设备平台](how-to-deploy-agent.md)，以验证对特定设备的支持。 

## <a name="which-data-is-collected-by-the-agent"></a>代理收集了哪些数据？

代理收集连接、访问、防火墙配置、&操作系统基线的进程列表。

## <a name="how-much-data-will-the-agent-generate"></a>代理将生成多少数据？

代理数据生成由设备、应用程序、连接类型和客户代理配置驱动。 由于设备和 IoT 解决方案之间的可变性很高，我们建议首先在实验室或测试设置中部署代理，以观察、学习和设置适合您需求的特定配置，同时测量生成的数据量。 启动服务后，IoT 代理 Azure 安全中心提供用于优化代理吞吐量的操作建议，以帮助您进行配置和自定义过程。

## <a name="how-can-i-control-my-billing"></a>如何控制我的帐单？

IoT 的 Azure 安全中心提供可配置的代理扫描、数据缓冲区以及创建自定义警报的能力，这些警报会增加或减少代理生成的数据量。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理消息是否会占用 IoT 中心配额？

是的。 代理传输的数据在 IoT 中心配额中计数。 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下来要做什么？ 我已经安装了代理，没有看到任何活动或日志...

1. 检查[代理类型是否适合设备的指定操作系统平台](how-to-deploy-agent.md)

1. 确认[代理在设备上运行](how-to-agent-configuration.md)。

2. 检查[服务已成功启用](quickstart-onboard-iot-hub.md)到 IoT 中心中的 **"安全**"。 

3. 检查设备是否[配置在 IoT 中心与 IoT 模块的 Azure 安全中心](quickstart-create-security-twin.md)。  

如果活动或日志仍然不可用，请与 Azure IoT 合作伙伴联系以获取其他帮助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>互联网连接停止工作时会发生什么情况？

只要设备正在运行，代理将继续运行和存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备恢复连接时，安全消息将恢复发送。 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>如果设备重新启动，安全代理是否会自行恢复？

安全代理旨在在每个设备重新启动时自动重新运行。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理是否会影响设备或其他已安装软件的性能？

代理消耗机器资源作为任何其他应用程序/进程，不应中断正常的设备活动。 代理运行设备上的资源消耗与其设置和配置相结合。 我们建议您在包含的环境中测试代理配置，以及与其他 IoT 应用程序和功能的互操作性，然后再尝试在生产环境中部署。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我正在对设备进行一些维护。 我可以关闭代理吗？

无法关闭代理。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>是否有方法可以测试代理是否正常工作？ 

如果代理停止通信或无法发送安全消息，则生成**设备处于静音**警报。

## <a name="can-i-create-my-own-alerts"></a>我可以创建自己的警报吗？

是的。 您可以针对预先确定的行为集（如 IP 地址和打开端口）设置自定义警报。 请参阅[创建自定义警报](quickstart-create-custom-alerts.md)，以了解有关自定义警报以及如何创建它们的更多详细信息。 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>我在哪里可以看到日志？ 我可以自定义日志吗？

- 使用连接的日志分析工作区查看警报和建议。 在工作区中配置存储大小和持续时间。

- 来自安全代理的原始数据也可以存储在日志分析帐户中。 在更改此选项的配置之前，请考虑大小、持续时间、存储要求和相关成本。 

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>为什么要将 IoT 的 Azure 安全中心添加到模块标识中？ 用途

IoT 的 Azure 安全中心模块用于代理配置和管理。


## <a name="next-steps"></a>后续步骤

要了解有关如何开始使用 IoT Azure 安全中心，请参阅以下文章：


- 阅读 Azure 安全中心，了解 IoT[概述](overview.md)
- 验证[服务先决条件](service-prerequisites.md)
- 了解有关如何[入门](getting-started.md)的更多
- 了解[针对 IoT 安全警报的 Azure 安全中心](concept-security-alerts.md)

