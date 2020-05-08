---
title: 将托管服务产品/服务发布到 Azure Marketplace
description: 了解如何发布加入客户到 Azure 委派的资源管理的托管服务产品/服务。
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 04da33d0232371f4175a935bf1e35925376babbc
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82788733"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>将托管服务产品/服务发布到 Azure Marketplace

本文介绍如何使用合作伙伴中心的 "[商用 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) " 计划向[Azure Marketplace](https://azuremarketplace.microsoft.com)发布公共或专用托管服务产品/服务。 然后，购买产品/服务的客户可以加入[Azure 委托资源管理](../concepts/azure-delegated-resource-management.md)的订阅和资源组。

## <a name="publishing-requirements"></a>发布要求

你需要[在合作伙伴中心](../../marketplace/partner-center-portal/create-account.md)拥有有效的帐户来创建和发布产品/服务。 如果你还没有帐户，则[注册过程](https://aka.ms/joinmarketplace)将指导你完成在合作伙伴中心创建帐户和注册商业市场计划的步骤。

根据[托管服务提供的认证要求](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)，你必须拥有[银牌或金牌云平台资格认证级别](https://docs.microsoft.com/partner-center/learn-about-competencies)，或是[Azure 专家 MSP](https://partner.microsoft.com/membership/azure-expert-msp)才能发布托管服务产品/服务。

你的 Microsoft 合作伙伴网络 (MPN) ID 将[自动关联](../../billing/billing-partner-admin-link-started.md)你发布的产品/服务，以跟踪你在客户参与中的影响。

> [!NOTE]
> 如果不想将产品/服务发布到 Azure 市场，可使用 Azure 资源管理器模板手动载入客户。 有关详细信息，请参阅[将客户载入到 Azure 委派资源管理](onboard-customer.md)。

## <a name="create-your-offer"></a>创建产品/服务

有关如何创建产品/服务（包括需要提供的所有信息和资产）的详细说明，请参阅[创建托管服务产品/服务](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)。

若要了解常规发布过程，请参阅[Azure Marketplace 和 AppSource 发布指南](../../marketplace/marketplace-publishers-guide.md)。 还应查看[商业市场认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies)，特别是[托管服务](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)部分。

客户添加你的产品/服务后，他们将能够委托一个或多个订阅或资源组，这些订阅或资源组将[载入 Azure 委托资源管理](#the-customer-onboarding-process)。

> [!IMPORTANT]
> 托管服务产品/服务中的每个计划都包含 "**清单详细信息**" 部分，你可以在其中定义租户中的 Azure Active Directory （Azure AD）实体，这些实体将有权访问委派的资源组和/或订阅（适用于购买该计划的客户）。 需要注意的是，包含的任何组（或用户或服务主体）将对每个购买计划的客户都具有相同的权限。 若要分配不同的组来与每个客户合作，你需要发布单独的[私有计划](../../marketplace/private-offers.md)，该计划对每个客户都是专用的。

## <a name="publish-your-offer"></a>发布产品/服务

完成所有部分后，下一步就是将产品/服务发布到 Azure 市场。 选择“发布”按钮启动产品/服务上线过程****。 有关此过程的详细信息，请参阅[此处](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)。 

你可以随时[发布产品/服务](../..//marketplace/partner-center-portal/update-existing-offer.md)的更新版本。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序****](view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都可以[查看更改](view-manage-service-providers.md#update-service-provider-offers)并决定是否要更新到新版本。 

## <a name="the-customer-onboarding-process"></a>客户加入过程

客户添加你的产品/服务后，他们将能够[委托一个或多个特定订阅或资源组](view-manage-service-providers.md#delegate-resources)，然后将加入这些订阅或资源组以进行 Azure 委托资源管理。 如果客户已接受产品/服务但尚未委托任何资源，则他们会在 Azure 门户[“服务提供商”****](view-manage-service-providers.md)页中“提供商产品/服务”部分的顶部看到备注****。

> [!IMPORTANT]
> 委托必须由客户租户中的非来宾帐户完成，此帐户具有载入订阅的[所有者内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)（或包含正在载入的资源组）。 若要查看所有可以委托订阅的用户，客户租户中的用户可以在 Azure 门户中选择订阅，打开“访问控制(IAM)”****，然后[查看具有“所有者”角色的所有用户](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

一旦客户委托了订阅（或订阅中的一个或多个资源组），就会为该订阅注册**ManagedServices**资源提供程序，租户中的用户将能够根据你的产品/服务中的授权访问委派的资源。

## <a name="next-steps"></a>后续步骤

- 了解[商用 Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)****。
