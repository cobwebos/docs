---
title: Azure 委托资源管理
description: 托管服务提供允许服务提供商向 Azure Marketplace 中的客户销售资源管理服务。
ms.date: 01/30/2020
ms.topic: conceptual
ms.openlocfilehash: 15814b1ca3b1b78de521033836e3614d18fd0c71
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904313"
---
# <a name="azure-delegated-resource-management"></a>Azure 委托资源管理

Azure 委托资源管理是 Azure Lighthouse 的关键组件之一。 借助 Azure 委派的资源管理，服务提供商可以简化客户参与和载入体验，同时以灵活性和精度在大规模管理委托资源。

## <a name="what-is-azure-delegated-resource-management"></a>什么是 Azure 委托资源管理？

Azure 委托的资源管理可将资源从一个租户逻辑投影到另一个租户。 这使得一个 Azure Active Directory （Azure AD）租户中的授权用户可以在属于客户的不同 Azure AD 租户之间执行管理操作。 服务提供商可以登录到自己的 Azure AD 租户，并有权在委派的客户订阅和资源组中工作。 这样，他们就可以代表其客户执行管理操作，而无需登录到每个单独的客户租户。

> [!NOTE]
> 还可以[在具有多个 Azure AD 租户的企业内](enterprise.md)使用 Azure 委派的资源管理来简化跨租户管理。

使用 Azure 委托资源管理，授权用户可以直接在客户订阅的上下文中工作，而无需在该客户的租户中拥有帐户，也无需成为客户租户的共同所有者。 他们还可以在 Azure 门户的["新**我的客户**" 页中查看和管理所有委派的客户订阅](../how-to/view-manage-customers.md)。

[跨租户管理体验](cross-tenant-management-experience.md)可帮助你更高效地使用 azure 策略、Azure 安全中心等 azure 管理服务。 在活动日志中跟踪所有服务提供程序活动，该活动存储在服务提供商和客户的租户中。 这意味着客户和服务提供商可以轻松地识别与任何更改关联的用户。

将客户加入 Azure 委派的资源管理时，他们将有权访问 "Azure 门户中的" 新建**服务提供商**"页面，用户可在其中[确认和管理其产品/服务、服务提供商和委派的资源](../how-to/view-manage-service-providers.md)。 如果客户希望吊销服务提供商的访问权限，则可以随时执行此操作。

你可以将[新的托管服务产品类型发布到 Azure Marketplace](../how-to/publish-managed-services-offers.md) ，以便轻松地将客户加入 azure 委派的资源管理。 或者，你可以[通过部署 Azure 资源管理器模板来完成载入过程](../how-to/onboard-customer.md)。

## <a name="how-azure-delegated-resource-management-works"></a>Azure 委派的资源管理的工作原理

从较高层次来看，Azure 委托资源管理的工作原理如下：

1. 作为服务提供商，你可以确定你的组、服务主体或用户将需要管理客户 Azure 资源的访问权限（角色）。 访问定义包含服务提供商的租户 ID 以及所需的产品/服务的访问权限，使用映射到[内置**roleDefinition**值](../../role-based-access-control/built-in-roles.md)（参与者、VM 参与者、读者等）的租户中的**principalId**标识定义。
2. 可以通过以下两种方式之一来指定此访问权限并将客户加入 Azure 委派的资源管理：
   - 发布客户将接受的[Azure Marketplace 托管服务产品/服务](../how-to/publish-managed-services-offers.md)（专用或公用）
   - 为一个或多个特定订阅或资源组将[Azure 资源管理器模板部署到客户的租户](../how-to/onboard-customer.md)
3. 一旦客户载入，授权用户便可以登录到你的服务提供商租户，并根据你定义的访问权限在给定的客户范围内执行管理任务。

> [!NOTE]
> 不支持在不同的云之间委派两个租户之间的订阅。

## <a name="support-for-azure-delegated-resource-management"></a>支持 Azure 委托资源管理

如果需要与 Azure 委托资源管理相关的帮助，可以在 Azure 门户中打开支持请求。 对于 "**问题类型**"，选择 "**技术**"。 选择订阅，然后选择 " **Lighthouse** " （在 "**监视 & 管理**" 下）。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解[Azure Marketplace 中的托管服务产品/服务](managed-services-offers.md)。