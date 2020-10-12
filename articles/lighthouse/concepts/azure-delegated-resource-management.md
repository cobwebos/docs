---
title: Azure 委派资源管理
description: Azure 委派的资源管理是 Azure Lighthouse 的关键部分，允许服务提供商以灵活性和精度在大规模管理委派的资源。
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 9a499ceda546b7ea5c71cd8c770f1a4b99001b08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163520"
---
# <a name="azure-delegated-resource-management"></a>Azure 委派资源管理

Azure 委托资源管理是 [Azure Lighthouse](../overview.md)的关键组件之一。 借助 Azure 委托资源管理，服务提供商可以简化客户参与和载入体验，同时灵活精确地管理大规模委托资源。

## <a name="what-is-azure-delegated-resource-management"></a>什么是 Azure 委派资源管理？

通过 Azure 委派资源管理，可以将资源从一个租户逻辑投影到另一个租户。 因此某个 Azure Active Directory (Azure AD) 租户中的授权用户可以跨属于其客户的不同 Azure AD 租户执行管理操作。 服务提供商可以登录到自己的 Azure AD 租户，并且有权在委派的客户订阅和资源组中工作。 这样，他们就可以代表其客户执行管理操作，而无需登录每个单独的客户租户。

> [!TIP]
> 还可在[具有多个其自己的 Azure AD 租户的企业中](enterprise.md)使用 Azure 委派资源管理，以简化跨租户管理。

借助 Azure 委派资源管理，授权用户可直接处理客户订阅的上下文，而无需具有客户的租户帐户或成为客户的租户的共同所有者。

[跨租户管理体验](cross-tenant-management-experience.md)使你可以更高效地使用 azure 策略、Azure 安全中心等 azure 管理服务。 在活动日志中跟踪所有服务提供程序活动，该活动存储在客户的租户 (中，并可由管理租户) 中的用户查看。 这表示管理和托管租户中的用户都可以轻松地识别与任何更改关联的用户。

可以将 [新的托管服务产品类型发布到 Azure Marketplace](../how-to/publish-managed-services-offers.md) ，轻松地将客户加入 azure Lighthouse。 或者，可以[通过部署 Azure 资源管理器模板来完成载入过程](../how-to/onboard-customer.md)。

## <a name="how-azure-delegated-resource-management-works"></a>Azure 委派资源管理的工作原理

概括而言，Azure 委派资源管理的工作原理如下：

1. 首先，确定 (角色的访问权限，) 组、服务主体或用户将需要管理客户的 Azure 资源。 访问定义包含管理的租户 ID 以及从租户映射到[内置**roleDefinition**值](../../role-based-access-control/built-in-roles.md) (参与者、VM 参与者、读者等 ) 的**principalId**标识。
2. 可以通过以下两种方式之一指定此访问权限并将客户加入 Azure Lighthouse：
   - [发布 Azure Marketplace 托管服务产品/服务](../how-to/publish-managed-services-offers.md) (客户将接受的私有或公共) 
   - 为一个或多个特定订阅或资源组[将 Azure 资源管理器模板部署到客户的租户](../how-to/onboard-customer.md)
3. 一旦客户载入，授权用户便可以登录到你的管理租户，并根据你定义的访问权限在给定的客户范围内执行任务。

> [!NOTE]
> 可以管理位于不同 [区域](../../availability-zones/az-overview.md#regions)的委托资源。 但是，不支持跨 [全国云和](../../active-directory/develop/authentication-national-cloud.md) Azure 公有云或跨两个不同的国家云的订阅委派。

## <a name="support-for-azure-delegated-resource-management"></a>Azure 委派资源管理支持

如果需要关于 Azure 委派资源管理方面的帮助，可以在 Azure 门户中打开支持请求。 对于“问题类型”，请选择“技术”********。 选择 "订阅"，然后选择 "**监视 & 管理**) 下的**Lighthouse** (。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解 [Azure 市场中的托管服务产品](managed-services-offers.md)。
