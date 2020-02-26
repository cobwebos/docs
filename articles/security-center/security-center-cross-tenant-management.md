---
title: Azure 安全中心中的跨租户管理 |Microsoft Docs
description: 了解如何在安全中心使用 Azure 委托资源管理设置跨租户管理，以管理多个租户的安全状况。
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
ms.openlocfilehash: 482154a37334b056719a18355f87b467d4fe95c7
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604254"
---
# <a name="cross-tenant-management-in-security-center"></a>安全中心的跨租户管理

跨租户管理使你可以利用[Azure 委派的资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)来查看和管理安全中心内多个租户的安全状况。 通过单一视图有效管理多个租户，而无需登录到每个租户的目录。

- 服务提供商可以在自己的租户中为多个客户管理资源的安全状况。

- 具有多个租户的组织的安全团队可从单个位置查看和管理其安全状况。

## <a name="set-up-cross-tenant-management"></a>设置跨租户管理

使用[Azure 委托资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)将对托管租户资源的访问权限委派给自己的租户，从而设置跨租户管理。

> [!NOTE]
> Azure 委派资源管理是 Azure Lighthouse 的关键组成部分之一。

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>跨租户管理如何在安全中心工作

你可以通过与在单个租户中管理多个订阅相同的方式查看和管理多个租户的订阅。

在顶部菜单栏中，单击 "筛选器" 图标，然后从每个租户的目录中选择要查看的订阅。

  ![筛选租户](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

视图和操作基本上相同。 下面是一些示例：

- **管理安全策略**：从一个视图中，使用[策略](tutorial-security-policy.md)管理多个资源的安全状况，采取安全建议的操作，以及收集和管理与安全相关的数据。
- **提高安全分数和符合性状况**：跨租户可见性使你可以查看所有租户的总体安全状态，以及如何最好地提高每个租户的[安全分数](security-center-secure-score.md)和[符合性](security-center-compliance-dashboard.md)状态。
- **修正建议**：一次监视和修正来自不同租户的多个资源的[建议](security-center-recommendations.md)。 然后，你可以立即解决所有租户面临的风险最高的漏洞。
- **管理警报**：在不同租户中检测[警报](security-center-alerts-overview.md)。 对不符合可操作[更正步骤](security-center-managing-and-responding-alerts.md)的资源执行操作。

- **管理高级云防御功能等**：管理各种威胁检测和保护服务，例如实时[（JIT） VM 访问](security-center-just-in-time.md)、[自适应网络强化](security-center-adaptive-network-hardening.md)、[自适应应用程序控件](security-center-adaptive-application.md)等。
 
## <a name="next-steps"></a>后续步骤
本文介绍了如何在安全中心进行跨租户管理。 若要了解有关安全中心的详细信息，请参阅以下文章：

* [通过 Azure 安全中心增强安全状况](security-center-monitoring.md)-了解如何监视 azure 资源的运行状况。
* [Azure 安全中心常见问题](faq-general.md) - 查找有关如何使用服务的常见问题。
* [了解企业方案中的 Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) -了解 azure Lighthouse 如何简化使用多 Azure AD 租户的企业内的跨租户管理。