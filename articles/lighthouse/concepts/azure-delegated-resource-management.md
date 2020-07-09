---
title: Azure 委派资源管理
description: Azure 委派的资源管理是 Azure Lighthouse 的关键部分，允许服务提供商以灵活性和精度在大规模管理委派的资源。
ms.date: 05/28/2020
ms.topic: conceptual
ms.openlocfilehash: bbe3c28cdcd252755b8350eaa5d2e72044981174
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120885"
---
# <a name="azure-delegated-resource-management"></a>Azure 委派资源管理

Azure 委托资源管理是[Azure Lighthouse](../overview.md)的关键组件之一。 借助 Azure 委托资源管理，服务提供商可以简化客户参与和载入体验，同时灵活精确地管理大规模委托资源。

## <a name="what-is-azure-delegated-resource-management"></a>什么是 Azure 委派资源管理？

通过 Azure 委派资源管理，可以将资源从一个租户逻辑投影到另一个租户。 因此某个 Azure Active Directory (Azure AD) 租户中的授权用户可以跨属于其客户的不同 Azure AD 租户执行管理操作。 服务提供商可以登录到自己的 Azure AD 租户，并且有权在委派的客户订阅和资源组中工作。 这样，他们就可以代表其客户执行管理操作，而无需登录每个单独的客户租户。

> [!NOTE]
> 还可在[具有多个其自己的 Azure AD 租户的企业中](enterprise.md)使用 Azure 委派资源管理，以简化跨租户管理。

借助 Azure 委派资源管理，授权用户可直接处理客户订阅的上下文，而无需具有客户的租户帐户或成为客户的租户的共同所有者。 他们还可以在 Azure 门户[查看并管理“我的客户”页中的所有委派客户订阅](../how-to/view-manage-customers.md)****。

[跨租户管理体验](cross-tenant-management-experience.md)有助于你使用 Azure Policy、Azure 安全中心等 Azure 管理服务更有效率地工作。 在活动日志中跟踪所有服务提供程序活动，该活动存储在客户的租户中（并且可以由管理租户中的用户查看）。 这意味着客户和服务提供商可以轻松地识别与任何更改关联的用户。

将客户加入 Azure 委派的资源管理时，他们将有权访问 Azure 门户中的**服务提供商**页面，用户可在其中[确认和管理其产品/服务、服务提供商和委派的资源](../how-to/view-manage-service-providers.md)。 如果客户想撤销服务提供商的访问权限，则可在此处随时撤销。

你可以将[新的托管服务产品类型发布到 Azure Marketplace](../how-to/publish-managed-services-offers.md) ，以便轻松地将客户加入 azure 委托资源管理。 或者，可以[通过部署 Azure 资源管理器模板来完成载入过程](../how-to/onboard-customer.md)。

## <a name="how-azure-delegated-resource-management-works"></a>Azure 委派资源管理的工作原理

概括而言，Azure 委派资源管理的工作原理如下：

1. 作为服务提供商，你需要确定你的组、服务主体或用户管理客户的 Azure 资源所需的访问权限（角色）。 访问权限定义包含服务提供商的租户 ID 以及产品/服务的所需访问权限，使用来自映射到[内置 roleDefinition 值](../../role-based-access-control/built-in-roles.md)的租户的 principalId 标识（参与者、VM 参与者、读者等）********。
2. 可使用以下两种方式之一来指定此访问权限并将客户载入到 Azure 委派资源管理：
   - 发布客户将接受的[Azure Marketplace 托管服务产品/服务](../how-to/publish-managed-services-offers.md)（专用或公用）
   - 为一个或多个特定订阅或资源组[将 Azure 资源管理器模板部署到客户的租户](../how-to/onboard-customer.md)
3. 载入客户之后，授权用户可根据你定义的访问权限登录到你的服务提供商租户，并在给定的客户范围内执行管理任务。

> [!NOTE]
> 可以管理位于不同[区域](../../availability-zones/az-overview.md#regions)的委托资源。 但是，不支持跨[全国云和](../../active-directory/develop/authentication-national-cloud.md)Azure 公有云或跨两个不同的国家云的订阅委派。

## <a name="support-for-azure-delegated-resource-management"></a>Azure 委派资源管理支持

如果需要关于 Azure 委派资源管理方面的帮助，可以在 Azure 门户中打开支持请求。 对于“问题类型”，请选择“技术”********。 选择订阅，然后选择 " **Lighthouse** " （在 "**监视 & 管理**" 下）。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解 [Azure 市场中的托管服务产品](managed-services-offers.md)。