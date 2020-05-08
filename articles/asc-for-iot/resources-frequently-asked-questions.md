---
title: 常见问题
description: 查找有关 Azure 安全中心的有关 IoT 功能和服务的常见问题的解答。
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
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734971"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>适用于 IoT 的 Azure 安全中心常见问题

本文提供了有关适用于 IoT 的 Azure 安全中心的常见问题和解答列表。

## <a name="does-azure-provide-support-for-iot-security"></a>Azure 是否为 IoT 安全提供支持？

Azure 通过 Azure 安全中心提供监视和管理 IoT 安全的集成视图，作为总体安全解决方案的一部分。 如果你是应用程序开发人员，则可以使用 IoT 中心视图来管理 IoT 应用程序安全性。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>什么是 Azure 独特的 IoT 安全价值主张？

Azure 安全中心 IoT 使企业能够将其现有的网络安全视图扩展到整个 IoT 解决方案。 Azure 提供了业务解决方案的端到端视图，使你能够根据企业安全状况和收集的数据来进行业务相关的操作和决策。 结合使用 Azure IoT、Azure IoT Edge 和 Azure 安全中心的安全性，您可以使用所需的安全性来创建所需的解决方案。

## <a name="who-is-azure-security-center-for-iot-made-for"></a>谁是用于 IoT 的 Azure 安全中心？

适用于 IoT 的 azure 安全中心集成在 Azure IoT 中心安全性内，并为日常业务解决方案安全操作提供管理。 适用于 IoT 的 azure 安全中心也集成到 Azure 安全中心功能中，并提供集成的视图来监视和管理你的 IoT 安全，作为整体安全解决方案的一部分。

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>如何将 Azure 安全中心与竞赛进行比较？

其他解决方案提供了一组允许客户创建自己的解决方案的功能，而用于 IoT 的 Azure 安全中心提供了独特的端到端 IoT 安全解决方案，可跨所有相关 Azure 资源的安全性提供丰富的视图。 Azure 可实现快速部署并与 IoT 中心模块孪生完全集成，以便与现有设备管理工具轻松集成。

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>是否必须是 Azure 安全中心客户才能使用此服务？

不，但建议这样做。 如果不使用 Azure 安全中心，Azure 安全中心的 IoT 会接收有限的连接资源数据，并对潜在攻击面、威胁和潜在攻击进行有限的分析。

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>我是否需要成为 Azure IoT 客户？

是的。 用于 IoT 的 azure 安全中心依赖于 Azure IoT 连接和基础结构。

## <a name="do-i-have-to-install-an-agent"></a>需要安装代理吗？

IoT 设备上的代理安装不是为 IoT 启用 Microsoft Azure 安全中心所必需的。 您可以选择以下三个选项，根据您的选择获取不同级别的安全监视和管理功能：

1. 无需修改即可为 IoT security agent 安装 Azure 安全中心。 此选项提供有关设备行为和访问权限的最高级别的增强安全见解。

1. 创建自己的代理并实现 Microsoft Azure 安全中心以进行 IoT 安全消息架构。 使用此选项，可以在设备安全代理顶部将 Microsoft Azure 安全中心用于 IoT 分析工具。

1. IoT 设备上无安全代理安装。 此选项启用 IoT 中心通信监视，同时降低了安全监视和管理功能。

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>IoT 代理的 Azure 安全中心有什么作用？

用于 IoT 代理的 Azure 安全中心为设备配置、行为和访问（通过扫描配置）、处理 & 连接提供设备级别的威胁范围。 用于 IoT security agent 的 Azure 安全中心不会扫描与业务相关的数据或活动。

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>在哪里可以获得用于 IoT 安全代理的 Azure 安全中心？

适用于 IoT security agent 的 Azure 安全中心是开放源代码，在 GitHub 上的32位和64位 Windows 和 Linux 版本中https://github.com/Azure/Azure-IoT-Security提供：。

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>用于 IoT 代理的 Azure 安全中心安装在何处？

可在 GitHub 中找到详细的安装和代理部署信息https://github.com/Azure/Azure-IoT-Security：。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理的依赖项和先决条件是什么？

用于 IoT 的 Azure 安全中心支持多种平台。 请参阅[支持的设备平台](how-to-deploy-agent.md)以验证对特定设备的支持。

## <a name="which-data-is-collected-by-the-agent"></a>代理收集哪些数据？

连接、访问、防火墙配置、进程列表 & OS 基线由代理收集。

## <a name="how-much-data-will-the-agent-generate"></a>代理将生成多少数据？

代理数据生成由设备、应用程序、连接类型和客户代理配置驱动。 由于设备和 IoT 解决方案之间的差异很高，因此，我们建议你首先在实验室或测试设置中部署代理，以观察、了解并设置符合你的需求的特定配置，同时测量所生成的数据量。 启动服务后，适用于 IoT 代理的 Azure 安全中心提供优化代理吞吐量的操作建议，以帮助你进行配置和自定义过程。

## <a name="how-can-i-control-my-billing"></a>如何控制我的帐单？

适用于 IoT 的 Azure 安全中心提供了可配置的代理扫描、数据缓冲区，以及用于创建自定义警报的功能，这些警报可增加或减少代理生成的数据量。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理消息是否使用 IoT 中心的配额？

是的。 代理传输的数据将计入 IoT 中心配额。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下来要做什么？ 我安装了一个代理，但没有看到任何活动或日志 .。。

1. 检查[代理类型是否适合设备的指定操作系统平台](how-to-deploy-agent.md)

1. 确认该[代理正在设备上运行](how-to-agent-configuration.md)。

1. 检查[服务是否已成功启用](quickstart-onboard-iot-hub.md)到 IoT 中心的**安全性**。

1. 检查是否已[在 Iot 中心中配置了用于 iot 模块的 Azure 安全中心](quickstart-create-security-twin.md)的设备。

如果活动或日志仍不可用，请联系 Azure 安全中心的 IoT 合作伙伴获取更多帮助。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>Internet 连接停止工作时会发生什么情况？

只要设备正在运行，代理就会继续运行并存储数据。 数据根据大小配置存储在安全消息缓存中。 当设备重新获得连接时，将继续发送安全消息。

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>如果重新启动了设备，安全代理是否会自行恢复？

安全代理设计为在每次设备重新启动时自动重新运行。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理是否可以影响设备的性能或其他已安装的软件？

代理以任何其他应用程序/进程的形式使用计算机资源，不应中断正常的设备活动。 运行代理的设备上的资源消耗与设置和配置耦合在一起。 建议在生产环境中部署之前，在包含的环境中测试代理配置，以及与其他 IoT 应用程序和功能的互操作性。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我在设备上进行了一些维护。 可以关闭代理吗？

无法关闭代理。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>是否有方法可以测试代理是否正常工作？

如果代理停止通信或无法发送安全消息，则会生成一个**无提示的设备**警报。

## <a name="can-i-create-my-own-alerts"></a>我是否可以创建自己的警报？

是的。 你可以对预先确定的行为集（例如 IP 地址和开放端口）设置自定义警报。 请参阅[创建自定义警报](quickstart-create-custom-alerts.md)，了解有关自定义警报的详细信息以及如何进行自定义警报。

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>在哪里可以查看日志？ 能否自定义日志？

- 使用连接的 Log Analytics 工作区查看警报和建议。 在工作区中配置存储大小和持续时间。

- 安全代理中的原始数据也可以存储在 Log Analytics 帐户中。 更改此选项的配置之前，请考虑大小、持续时间、存储要求和相关成本。

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>为什么要将 IoT 的 Azure 安全中心添加到模块标识？ 用途

用于 IoT 模块的 Azure 安全中心用于代理配置和管理。

## <a name="next-steps"></a>后续步骤

若要了解有关如何开始 Azure 安全中心的 IoT 的详细信息，请参阅以下文章：

- 阅读 Azure 安全中心以获取 IoT[概述](overview.md)
- 验证[服务先决条件](service-prerequisites.md)
- 了解有关如何[入门](getting-started.md)的详细信息
- 了解[Azure 安全中心的 IoT 安全警报](concept-security-alerts.md)
