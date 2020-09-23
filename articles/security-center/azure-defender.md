---
title: Azure Defender 和可用计划概述
description: 了解 Azure Defender 的计划、保护和警报。 然后继续在订阅上启用 Azure Defender。
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 627fc52043054557ca6f6baf9827da22d2af5139
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933557"
---
# <a name="introduction-to-azure-defender"></a>Azure Defender 简介

Azure 安全中心的功能涵盖了云安全性的两大主要要素：

- **云安全状况管理 (CSPM) **
- **云工作负荷保护 (CWP) **

安全中心的 CSPM 功能（例如安全分数、Windows 和 Linux Azure 计算机中的安全错误配置）都是所有 Azure 用户可用的免费安全中心体验的一部分。 使用这些 CSPM 功能增强你的状况，确保法规遵从性， 

**Azure Defender** 是 (CWPP) 集成在安全中心内的云工作负荷保护平台，用于对 Azure 和混合工作负荷进行高级、智能、保护。

这是 Azure 中的 Azure Defender 仪表板安全中心：

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Azure Defender 仪表板的示例" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Azure Defender 可以保护哪些资源类型？

Azure Defender 为虚拟机、SQL 数据库、容器、web 应用程序、网络等提供安全警报和高级威胁防护。

当你从 Azure 安全中心的 " **定价和设置** " 区域启用 azure Defender 时，将同时启用以下 Defender 计划，并为环境中的计算、数据和服务层提供综合防御：

- [适用于服务器的 Azure Defender](defender-for-servers-introduction.md)
- [适用于应用服务的 Azure Defender](defender-for-app-service-introduction.md)
- [适用于存储的 Azure Defender](defender-for-storage-introduction.md)
- [Azure Defender for SQL](defender-for-sql-introduction.md)
- [适用于 IoT 的 Azure Defender](defender-for-iot-introduction.md)
- [适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md)
- [适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md)
- [适用于 Key Vault 的 Azure Defender](defender-for-key-vault-introduction.md)

其中每个计划分别在安全中心文档中进行了介绍。


## <a name="hybrid-cloud-protection"></a>混合云保护

还可以在混合云环境中添加 Azure Defender 功能，并将其用于保护 Azure 环境：

- 保护非 Azure 服务器
- 保护其他云中的虚拟机 (如 AWS 和 GCP) 
- 保护 IoT 设备

您将根据您的特定环境获取自定义威胁智能和优先顺序的警报，以便您可以专注于最重要的内容

部署 [Azure Arc](https://azure.microsoft.com/services/azure-arc/) 并使 azure Defender 能够将保护扩展到本地和多云虚拟机和 SQL 数据库。 服务器的 Azure Arc 是一种免费服务，但在启用 Arc 的服务器上使用的服务（例如，Azure Defender）将按照该服务的定价收费。

[了解有关 Azure Arc 的详细信息](https://docs.microsoft.com/azure/azure-arc/overview)。


## <a name="azure-defender-alerts"></a>Azure Defender 警报 

当 Azure Defender 在环境中的任何区域检测到威胁时，会生成警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。

无论警报是由安全中心生成还是由安全中心从集成安全产品接收，都可以导出。 若要将警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。

> [!NOTE]
> 来自不同源的警报可能在不同的时间后出现。 例如，需要分析网络流量的警报的出现时间，可能比虚拟机上运行的可疑进程的相关警报要晚一些。


## <a name="azure-defender-advanced-protection-capabilities"></a>Azure Defender 高级保护功能

Azure Defender 使用高级分析来定制与资源相关的建议。 

保护包括使用实时访问和自适应应用程序控件保护 Vm 的管理端口，为应用程序创建和不应在计算机上运行的应用程序创建允许列表。 

使用 Azure Defender 仪表板中的高级防护磁贴来监视和配置每种保护。 

## <a name="vulnerability-assessment-and-management"></a>漏洞评估和管理

Azure Defender 为你的虚拟机和容器注册表包括漏洞扫描，无需额外付费。 这些扫描程序由 Qualys 提供支持，但不需要 Qualys 许可证，甚至 Qualys 帐户-所有内容都在安全中心内无缝处理。 

查看这些漏洞扫描程序中的发现结果，并从安全中心内对它们做出响应。 这使安全中心更接近于所有云安全工作的单个窗格。

在以下页面上了解详细信息：

- [Azure 虚拟机安全中心的集成漏洞评估解决方案](deploy-vulnerability-assessment-vm.md)
- [标识 Azure 容器注册表中的映像中的漏洞](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Defender 的好处。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](security-center-pricing.md)