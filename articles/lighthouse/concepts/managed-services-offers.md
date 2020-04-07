---
title: Azure 应用商店中的托管服务产品
description: 托管服务提供允许服务提供商向 Azure 应用商店中的客户提供资源管理产品。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672418"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure 应用商店中的托管服务产品

本文介绍[Azure 应用商店](https://azuremarketplace.microsoft.com)中的**托管服务**产品/服务类型。 托管服务提供允许您通过使用[Azure 委派的资源管理](azure-delegated-resource-management.md)为客户提供资源管理服务。 您可以向所有潜在客户或仅向一个或多个特定客户提供这些优惠。 由于直接向客户收取与这些托管服务相关的成本，因此 Microsoft 不收取任何费用。

## <a name="understand-managed-service-offers"></a>了解托管服务提供

托管服务提供简化为 Azure 委派资源管理的入职客户流程。 客户在 Azure 市场中购买产品/服务时，他们将能够指定应加入哪些订阅和/或资源组。

之后，根据您在[云合作伙伴门户](https://cloudpartner.azure.com/)中创建产品/服务时定义的访问权限，组织中的用户将能够从组织的租户中处理这些资源。 这通过清单来完成，该清单指定将使用 Azure 委托资源管理访问客户资源的 Azure AD 用户、组和服务主体，以及定义其访问级别的角色。 通过向 Azure AD 组而不是单个用户或应用程序帐户分配权限，可以在访问要求发生更改时添加或删除单个用户。

## <a name="public-and-private-offers"></a>公共和专用产品/服务

每个托管服务产品/服务均包含一个或多个计划。 计划可以是私有的，也可以是公开的。

如果要将产品/服务限制为特定客户，可以发布专用计划。 执行此操作时，只能为你提供的特定订阅 ID 购买计划。 有关详细信息，请参阅[专用产品/服务](../../marketplace/private-offers.md)。

可以利用公共计划将服务推广给新客户。 当只需要对客户租户的有限访问权限时，这些信息通常更合适。 建立与客户的关系后，如果他们决定向你的组织授予其他访问权限，你可以通过为该客户仅发布新的专用计划来完成此操作，或者通过[使用 Azure 资源管理器模板进行进一步访问来载入](../how-to/onboard-customer.md)。

如果需要，可以在同一个产品/服务中包括公用和专用计划。

> [!IMPORTANT]
> 计划一旦公开发布，您就无法将其更改为私有。 要控制哪些客户可以接受您的报价并委派资源，请使用私人计划。 使用公共计划时，您无法将可用性限制为特定客户，甚至限制特定数量的客户（尽管如果您选择这样做，您可以完全停止销售计划）。 只有在发布产品/服务时，在**角色定义**中包含"**授权"** 设置为["托管服务注册分配删除角色"时](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，才能在客户接受产品/服务后取消对[委派的访问权限](../how-to/onboard-customer.md#remove-access-to-a-delegation)。 您也可以联系客户，要求他们[删除您的访问权限](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

## <a name="publish-managed-service-offers"></a>发布托管服务产品

要了解如何发布托管服务产品，请参阅[将托管服务产品发布到 Azure 市场](../how-to/publish-managed-services-offers.md)。 有关使用云合作伙伴门户发布到 Azure 应用商店的一般信息，请参阅[Azure 应用商店和 AppSource 发布指南](../../marketplace/marketplace-publishers-guide.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委托的资源管理](azure-delegated-resource-management.md)和[跨租户管理体验](cross-tenant-management-experience.md)。
- 向 Azure 应用商店[发布托管服务产品/](../how-to/publish-managed-services-offers.md)服务。
