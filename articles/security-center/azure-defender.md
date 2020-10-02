---
title: Azure Defender 和可用计划概述
description: 了解 Azure Defender 的计划、保护和警报。 然后在订阅上启用 Azure Defender。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5a5b96d5a9ea6aa05da30238690b8f5fa745b3f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448430"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 简介

Azure 安全中心的功能涵盖了云安全性的两大重要领域：

- **云安全状况管理 (CSPM)**
- **云工作负载保护 (CWP)**

安全中心的 CSPM 功能（例如安全分数、Windows 和 Linux Azure 计算机中安全错误配置的检测）是所有 Azure 用户都可以免费使用的安全中心体验的一部分。 使用这些 CSPM 功能来增强安全状况、确保合规性。

**Azure Defender** 是集成到安全中心内部的云工作负载保护平台 (CWPP)，用于为 Azure 和混合工作负载提供高级智能的保护。

这是 Azure 安全中心中的 Azure Defender 仪表板：

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 仪表板示例" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender 可以保护哪些资源类型？

Azure Defender 为虚拟机、SQL 数据库、容器、web 应用程序、网络等提供安全警报和高级威胁防护。

从 Azure 安全中心的“定价和设置”区域启用 Azure Defender 时，将同时启用以下 Defender 计划，并为环境的计算、数据和服务层提供全面防护：

- [适用于服务器的 Azure Defender](defender-for-servers-introduction.md)
- [适用于应用服务的 Azure Defender](defender-for-app-service-introduction.md)
- [适用于存储的 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [适用于 IoT 的 Azure Defender](defender-for-iot-introduction.md)
- [适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)
- [适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)

安全中心的文档对其中每个计划单独进行了介绍。


## <a name="hybrid-cloud-protection"></a>混合云保护

与保护 Azure 环境一样，也可以在混合云环境中添加 Azure Defender 功能：

- 保护非 Azure 服务器
- 保护其他云中的虚拟机（例如 AWS 和 GCP）
- 保护 IoT 设备

你将根据你的特定环境获取自定义威胁智能和设置了优先级别的警报，这样你便能够专注于最重要的事务

部署 [Azure Arc](https://azure.microsoft.com/services/azure-arc/)，将 Azure Defender 的保护扩展到本地和多云虚拟机以及 SQL 数据库。 Azure Arc for servers 是一项免费服务，但在启用了 Arc 的服务器上使用的服务（例如 Azure Defender）将按照该服务的定价收费。

[详细了解 Azure Arc](https://docs.microsoft.com/azure/azure-arc/overview)。


## <a name="azure-defender-alerts"></a>Azure Defender 警报 

当 Azure Defender 检测到环境中的任何区域遭到威胁时，会生成警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。

无论警报是由安全中心生成，还是由安全中心从集成的安全产品接收，你都可以导出该警报。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。

> [!NOTE]
> 来自不同源的警报可能在不同的时间后出现。 例如，需要分析网络流量的警报的出现时间，可能比虚拟机上运行的可疑进程的相关警报要晚一些。


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender 高级保护功能

Azure Defender 在定制与资源相关的建议时会使用高级分析。 

保护措施包括使用实时访问和自适应应用程序控件保护 VM 的管理端口，创建允许列表来确定在计算机上应或不应运行哪些应用。 

使用 Azure Defender 仪表板中的高级保护磁贴来监视和配置每种保护措施。 

## <a name="vulnerability-assessment-and-management"></a>漏洞评估和管理

Azure Defender 为你的虚拟机和容器注册表提供漏洞扫描，且无需额外付费。 扫描程序由 Qualys 提供支持，但你无需具备 Qualys 许可证，甚至也不需要 Qualys 帐户 - 所有操作都在安全中心内无缝执行。 

查看这些漏洞扫描程序中的发现结果，并相应从安全中心内部作出全部响应。 这使安全中心更接近于用于集中了解所有云安全工作情况的统一视窗。

通过以下页面了解详细信息：

- [Azure 虚拟机安全中心的集成漏洞评估解决方案](deploy-vulnerability-assessment-vm.md)
- [标识 Azure 容器注册表映像中的漏洞](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Defender 的优点。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](security-center-pricing.md)