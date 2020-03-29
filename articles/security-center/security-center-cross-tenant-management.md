---
title: Azure 安全中心的跨租户管理 |微软文档
description: 了解如何设置跨租户管理，以便使用 Azure 委派的资源管理管理安全中心中多个租户的安全状况。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919475"
---
# <a name="cross-tenant-management-in-security-center"></a>安全中心的跨租户管理

跨租户管理使您能够通过利用[Azure 委派的资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)来查看和管理安全中心中多个租户的安全状态。 从单个视图高效地管理多个租户，而无需登录到每个租户的目录。

- 服务提供商可以从自己的租户管理资源的安全状态，适用于多个客户。

- 具有多个租户的组织的安全团队可以从单个位置查看和管理其安全状态。

## <a name="set-up-cross-tenant-management"></a>设置跨租户管理

通过使用[Azure 委派的资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)，通过将对托管租户资源的访问委派给您自己的租户来设置跨租户管理。

> [!NOTE]
> Azure 委派资源管理是 Azure Lighthouse 的关键组成部分之一。

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>跨租户管理如何在安全中心工作

您可以查看和管理多个租户中的订阅，就像管理单个租户中的多个订阅一样。

从顶部菜单栏中，单击筛选器图标，并从每个租户的目录中选择要查看的订阅。

  ![筛选租户](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

观点和操作基本相同。 下面是一些示例：

- **管理安全策略**：从一个角度，使用[策略](tutorial-security-policy.md)管理许多资源的安全状态，使用安全建议执行操作，并收集和管理与安全相关的数据。
- **改善安全分数和合规性状态**：跨租户可见性使您能够查看所有租户的整体安全状况，以及最佳改进每个租户[的安全得分](security-center-secure-score.md)和[合规性状况](security-center-compliance-dashboard.md)的位置和方式。
- **修正建议**：一次监视和修正来自各个租户的许多资源[的建议](security-center-recommendations.md)。 然后，您可以立即解决在所有租户中呈现最高风险的漏洞。
- **管理警报**：检测不同租户的[警报](security-center-alerts-overview.md)。 对不符合可操作[补救步骤](security-center-managing-and-responding-alerts.md)的资源采取行动。

- **管理高级云防御功能等**：管理各种威胁防护服务，如[及时 （JIT） VM 访问](security-center-just-in-time.md)、[自适应网络强化](security-center-adaptive-network-hardening.md)、[自适应应用程序控制](security-center-adaptive-application.md)等。
 
## <a name="next-steps"></a>后续步骤
本文介绍了跨租户管理在安全中心的工作原理。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [使用 Azure 安全中心增强安全状态](security-center-monitoring.md)- 了解如何监视 Azure 资源的运行状况。
* [Azure 安全中心常见问题](faq-general.md) - 查找有关如何使用服务的常见问题。
* [了解企业方案中的 Azure 灯塔](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise)- 了解 Azure 灯塔如何在使用多个 Azure AD 租户的企业中简化跨租户管理。