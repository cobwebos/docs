---
title: 向 Azure 应用商店发布托管服务产品/服务
description: 了解如何发布将客户载入到 Azure 委派资源管理的托管服务产品。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673939"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>向 Azure 应用商店发布托管服务产品/服务

在本文中，您将学习如何使用[合作伙伴中心](https://partner.microsoft.com/)中的[商业应用商店](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)计划向[Azure 应用商店](https://azuremarketplace.microsoft.com)发布公共或私有托管服务产品/ 然后，购买产品/服务的客户将能够将订阅和资源组用于[Azure 委派的资源管理](../concepts/azure-delegated-resource-management.md)。

## <a name="publishing-requirements"></a>发布要求

您需要[在合作伙伴中心](../../marketplace/partner-center-portal/create-account.md)中拥有有效的帐户才能创建和发布产品/服务。 如果您还没有帐户，[注册过程](https://aka.ms/joinmarketplace)将引导您完成在合作伙伴中心创建帐户并注册商业市场计划的步骤。

根据[托管服务提供认证要求](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)，您必须具有[银云或金云平台能力级别](https://docs.microsoft.com/partner-center/learn-about-competencies)或 Azure 专家[MSP](https://partner.microsoft.com/membership/azure-expert-msp)才能发布托管服务产品/服务。

你的 Microsoft 合作伙伴网络 (MPN) ID 将[自动关联](../../billing/billing-partner-admin-link-started.md)你发布的产品/服务，以跟踪你在客户参与中的影响。

> [!NOTE]
> 如果不想将产品/服务发布到 Azure 市场，可使用 Azure 资源管理器模板手动载入客户。 有关详细信息，请参阅[将客户载入到 Azure 委派资源管理](onboard-customer.md)。

## <a name="create-your-offer"></a>创建产品/服务

有关如何创建产品/服务的详细信息（包括您需要提供的所有信息和资产）的详细说明，请参阅[创建新的托管服务产品/服务](../../marketplace/partner-center-portal/create-new-managed-service-offer.md)。

要了解常规发布过程，请参阅[Azure 应用商店和 AppSource 发布指南](../../marketplace/marketplace-publishers-guide.md)。 还应查看[商业市场认证策略](https://docs.microsoft.com/legal/marketplace/certification-policies)，特别是[托管服务](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)部分。

客户添加产品/服务后，他们将能够委派一个或多个订阅或资源组，然后这些订阅或资源组将[注册用于 Azure 委派的资源管理](#the-customer-onboarding-process)。

> [!IMPORTANT]
> Each plan in a Managed service offer includes a **Manifest Details** section, where you define the Azure Active Directory (Azure AD) entities in your tenant that will have access to the delegated resource groups and/or subscriptions for customers who purchase that plan. 请务必注意，您包括的任何组（或用户或服务主体）对于购买计划的每个客户都将具有相同的权限。 要分配不同的组以与每个客户合作，您需要发布每个客户独有的单独[私人计划](../../marketplace/private-offers.md)。

## <a name="publish-your-offer"></a>发布产品/服务

完成所有部分后，下一步就是将产品/服务发布到 Azure 市场。 选择“发布”按钮启动产品/服务上线过程****。 有关此过程的详细信息，请参阅[此处](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)。

你可以随时[发布产品/服务](../../marketplace/partner-center-portal/update-existing-offer.md)的更新版本。 例如，你可能想要向以前发布的产品/服务添加新的角色定义。 当你执行此操作时，已添加该产品/服务的客户会在 Azure 门户中的[服务提供程序****](view-manage-service-providers.md)页面上看到一个图标，他们可通过图标知道更新可用。 每个客户都可以[查看更改](view-manage-service-providers.md#update-service-provider-offers)并决定是否要更新到新版本。 

## <a name="the-customer-onboarding-process"></a>客户加入过程

客户添加你的产品/服务后，他们将能够[委托一个或多个特定订阅或资源组](view-manage-service-providers.md#delegate-resources)，然后将加入这些订阅或资源组以进行 Azure 委托资源管理。 如果客户已接受产品/服务但尚未委托任何资源，则他们会在 Azure 门户[“服务提供商”****](view-manage-service-providers.md)页中“提供商产品/服务”部分的顶部看到备注****。

> [!IMPORTANT]
> 委派必须由客户租户中的非来宾帐户完成，该帐户具有已加入的订阅[的所有者内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)（或包含正在装机的资源组）。 若要查看所有可以委托订阅的用户，客户租户中的用户可以在 Azure 门户中选择订阅，打开“访问控制(IAM)”****，然后[查看具有“所有者”角色的所有用户](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

一旦客户委派订阅（或订阅中的一个或多个资源组 **），Microsoft.托管服务**资源提供程序将注册该订阅，租户中的用户将能够根据产品/服务中的授权访问委派的资源。

## <a name="next-steps"></a>后续步骤

- 了解[商业市场](../../marketplace/partner-center-portal/commercial-marketplace-overview.md)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)****。
