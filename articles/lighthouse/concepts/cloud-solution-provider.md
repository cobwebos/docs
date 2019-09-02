---
title: Azure Lighthouse 和云解决方案提供商计划
description: 使用 Azure 委派资源管理时，请务必考虑安全性和访问控制。
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 399d2cb829c0425e3c9ee70a61cafde8568f903b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012125"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse 和云解决方案提供商计划

如果你是 [CSP（云解决方案提供商）](https://docs.microsoft.com/partner-center/csp-overview)合作伙伴，则已经可以使用[代表客户管理 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 功能，通过 CSP 计划访问为客户创建的 Azure 订阅。 通过这一访问权限，可直接支持、配置和管理客户的订阅。

通过 Azure Lighthouse，可以将 Azure 委派资源管理与 AOBO 一起使用。 这通过为用户启用更细粒度的权限，有助于提高安全性并减少不必要的访问。 它还可以提高效率和可伸缩性，因为你的用户可以使用租户中的单个登录名来处理多个客户订阅。

## <a name="administer-on-behalf-of-aobo"></a>代表客户管理 (AOBO)

使用 AOBO，租户中具有[管理员代理](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)角色的任何用户都可对通过 CSP 计划创建的 Azure 订阅进行 AOBO 访问。 需要访问任意客户订阅的任何用户都必须是此组的成员。 AOBO 不允许灵活创建对不同客户有效的不同组，也不允许为组或用户启用不同角色。

![使用 AOBO 进行租户管理](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure 委派资源管理

使用 Azure 委派资源管理，可以将不同的组分配给不同的客户或角色，如下图所示。 通过 Azure 委派资源管理，用户将具有适当级别的访问权限，所以可以减少具有管理员代理角色（并因此具有完整的 AOBO 访问权限）的用户的数量。 这有助于通过限制对客户资源的不必要访问来提高安全性。 还可以更灵活地大规模管理多个客户。

按照[将订阅加入到 Azure 委派资源管理](../how-to/onboard-customer.md)中所述的步骤，加入通过 CSP 计划创建的订阅。 租户中具有管理员代理角色的任何用户都可以执行此加入过程。

![使用 AOBO 和 Azure 委派资源管理进行租户管理](../media/csp-2.jpg)

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解如何[加入 Azure 委派资源管理的订阅](../how-to/onboard-customer.md)。
- 详细了解[云解决方案提供商计划](https://docs.microsoft.com/partner-center/csp-overview)。
