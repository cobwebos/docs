---
title: 云解决方案提供商计划注意事项
description: 对于 CSP 合作伙伴，Azure 委派资源管理通过启用细化权限来帮助提高安全性和控制力。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 27bb1c4f82fe3c9ff8abe74b74bef19f16a8a874
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967156"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse 和云解决方案提供商计划

如果你是 [CSP（云解决方案提供商）](/partner-center/csp-overview)合作伙伴，则已经可以使用[代表客户管理 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 功能，通过 CSP 计划访问为客户创建的 Azure 订阅。 通过这一访问权限，可直接支持、配置和管理客户的订阅。

使用 [Azure Lighthouse](../overview.md)，可将 azure 委托资源管理与 AOBO 一起使用。 这通过为用户启用更细粒度的权限，有助于提高安全性并减少不必要的访问。 它还可以提高效率和可伸缩性，因为你的用户可以使用租户中的单个登录名来处理多个客户订阅。

> [!TIP]
> 为了帮助保护客户资源，请务必查看并遵循我们的[建议安全做法](recommended-security-practices.md)以及[合作伙伴安全要求](/partner-center/partner-security-requirements)。

## <a name="administer-on-behalf-of-aobo"></a>代表客户管理 (AOBO)

使用 AOBO，租户中具有[管理员代理](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)角色的任何用户都可对通过 CSP 计划创建的 Azure 订阅进行 AOBO 访问。 需要访问任意客户订阅的任何用户都必须是此组的成员。 AOBO 不允许灵活创建对不同客户有效的不同组，也不允许为组或用户启用不同角色。

![显示使用 AOBO 的租户管理的关系图。](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure 委派资源管理

使用 Azure Lighthouse，你可以将不同的组分配给不同的客户或角色，如下图所示。 由于用户将通过 Azure 委托的资源管理获取适当的访问级别，因此你可以减少具有“管理员代理”角色的用户数量（因此拥有完全 AOBO 访问权限）。 这有助于通过限制对客户资源的不必要访问权限来提高安全性。 此外，它还可以让你更灵活地大规模管理多个客户。

载入通过 CSP 计划创建的订阅，并遵循在 [Azure Lighthouse 订阅](../how-to/onboard-customer.md)中所述的步骤。 租户中具有管理员代理角色的任何用户都可以执行此加入过程。

![显示使用 AOBO 和 Azure 委托资源管理的租户管理的示意图。](../media/csp-2.jpg)

> [!TIP]
> 通过云解决方案提供商 (CSP) 计划，通过云解决方案提供商的经销商建立的订阅不支持具有私有计划的[托管服务提供](managed-services-offers.md)。 可以 [使用 azure 资源管理器模板](../how-to/onboard-customer.md)将这些订阅登记到 azure Lighthouse。

> [!NOTE]
> Azure 门户中的 " [**我的客户** " 页面](../how-to/view-manage-customers.md) 现在包含一个 **云解决方案提供商 (预览版) ** "部分，它显示已 [签署 MICROSOFT 客户协议 () MCA 的 ](/partner-center/confirm-customer-agreement) CSP 客户的帐单信息和资源，并 [在 Azure 计划下](/partner-center/azure-plan-get-started)。 有关详细信息，请参阅 [Microsoft 合作伙伴协议计费帐户入门](../../cost-management-billing/understand/mpa-overview.md)。
>
> 无论他们是否已载入 Azure Lighthouse，此部分中都可能会显示 CSP 客户。 如果有，则它们也会显示在 **customers** 节中，如 [查看和管理客户和委派的资源](../how-to/view-manage-customers.md)中所述。 同样，CSP 客户无需在 "**我的客户**" 的 "**云解决方案提供商 (预览") **部分中显示，即可将它们加入 Azure Lighthouse。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解如何 [在 Azure Lighthouse](../how-to/onboard-customer.md)中加入订阅。
- 详细了解[云解决方案提供商计划](/partner-center/csp-overview)。
