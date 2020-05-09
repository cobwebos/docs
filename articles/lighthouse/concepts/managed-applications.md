---
title: Azure Lighthouse 和 Azure 托管应用程序
description: Azure Lighthouse 和 Azure 托管应用程序 .。。
ms.date: 05/01/2020
ms.topic: conceptual
ms.openlocfilehash: d9923d31e78675927b4ca235607b2a61b24ccc41
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783625"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 和 Azure 托管应用程序

Azure 托管应用程序和 Azure Lighthouse 通过使服务提供商能够访问位于客户租户中的资源来工作。 了解它们的工作方式和它们可帮助启用的方案以及它们如何一起使用可能会很有帮助。

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>比较 Azure Lighthouse 和 Azure 托管应用程序

### <a name="azure-lighthouse"></a>Azure Lighthouse

使用[Azure Lighthouse](../overview.md)，服务提供商可以直接在客户的订阅（或资源组）上执行各种管理任务。 这种访问是通过逻辑投影实现的，它允许服务提供商登录到自己的租户并访问属于客户租户的资源。 客户可以确定要委派给服务提供商的订阅或资源组，客户可以保持对这些资源的完全访问权限。 他们还可以随时删除服务提供商的访问权限。

若要使用 Azure Lighthouse，通过[部署 ARM 模板](../how-to/onboard-customer.md)或通过[azure Marketplace 中的托管服务产品/服务](managed-services-offers.md)，客户载入用于[azure 委托的资源管理](azure-delegated-resource-management.md)。 你可以通过[链接你的合作伙伴 ID](../../cost-management-billing/manage/link-partner-id.md)跟踪对客户服务的影响。

通常，在服务提供商将定期为客户执行管理任务时，使用 Azure Lighthouse。

### <a name="azure-managed-applications"></a>Azure 托管应用程序

[Azure 托管应用程序](../../azure-resource-manager/managed-applications/overview.md)允许服务提供商或 ISV 提供便于客户在自己的订阅中进行部署和使用的云解决方案。

在托管应用程序中，应用程序使用的资源捆绑在一起，并部署到由发布服务器管理的资源组。 此资源组存在于客户的订阅中，但发布者租户中的标识有权访问该资源组。 ISV 继续管理和维护托管的应用程序，而客户不能直接访问其资源组中的工作，也不能访问其资源。

托管应用程序支持自定义的[Azure 门户体验](../../azure-resource-manager/managed-applications/concepts-view-definition.md)和[与自定义提供程序的集成](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md)。 这些选项可用于提供更多自定义的集成体验，使客户能够更轻松地执行一些管理任务。

可以将托管应用程序[发布到 Azure Marketplace](../../azure-resource-manager/managed-applications/publish-marketplace-app.md)，作为特定客户使用的专用产品/服务，或作为多个客户可以购买的公共产品/服务。 还可以通过将[托管应用程序发布到服务目录](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)，将它们传递给你的组织中的用户。 你可以使用 ARM 模板部署服务目录和 Marketplace 实例，其中可以包含商业市场合作伙伴的唯一标识符，以跟踪[客户使用情况归属](../../marketplace/azure-partner-customer-usage-attribution.md)。

Azure 托管应用程序通常用于特定客户需求，可通过完全由服务提供商管理的全包式解决方案来实现。

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>一起使用 Azure Lighthouse 和 Azure 托管应用程序

尽管 Azure Lighthouse 和 Azure 托管应用程序使用不同的访问机制来实现不同的目标，但在某些情况下，服务提供商可以将这两个应用程序同时用于同一客户。

例如，客户可能想要通过 Azure Lighthouse 通过服务提供商提供托管服务，以便他们可以查看合作伙伴的操作以及对其委托订阅的持续控制。 但是，服务提供商可能不希望客户访问将存储在客户的租户中的某些资源，也不允许对这些资源进行任何自定义操作。 为了满足这些目标，服务提供商可以将专用产品/服务作为托管应用程序发布。 托管应用程序可以包括部署在客户的租户中的资源组，但不能由客户直接访问该资源组。

客户也可能对多个服务提供商提供的托管应用程序感兴趣，无论他们是否还通过 Azure Lighthouse 使用托管服务。 此外，云解决方案提供商（CSP）计划中的合作伙伴可将其他 Isv 发布的某些托管应用程序转售给他们通过 Azure Lighthouse 支持的客户。 借助各种选项，服务提供商可以根据客户的需求选择适当的平衡，同时限制对资源的访问。

## <a name="next-steps"></a>后续步骤

- 了解[Azure 托管应用程序](../../azure-resource-manager/managed-applications/overview.md)。
- 了解如何[加入 Azure 委派资源管理的订阅](../how-to/onboard-customer.md)。
