---
title: Azure Marketplace 中的托管服务提供
description: 托管服务提供允许服务提供商向 Azure Marketplace 中的客户销售资源管理服务。
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 13e1825ae6eb50b1b376e3bd3de908a545fbe023
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144898"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace 中的托管服务提供

本文介绍[Azure Marketplace](https://azuremarketplace.microsoft.com)中的**托管服务**提供类型。 托管服务产品/服务允许你使用[Azure 委派的资源管理](azure-delegated-resource-management.md)为客户提供资源管理服务。 你可以向所有潜在客户提供这些产品/服务，也可以仅向一个或多个特定客户提供这些产品/服务。 由于直接向客户收取与这些托管服务相关的成本，因此 Microsoft 不收取任何费用。

## <a name="understand-managed-service-offers"></a>了解托管服务产品

托管服务提供简化 Azure 委托资源管理的客户的管理过程。 客户在 Azure 市场中购买产品/服务时，他们将能够指定应加入哪些订阅和/或资源组。

之后，你的组织中的用户将能够根据你在创建产品/服务时定义的访问权限，从你组织的租户中处理这些资源。 这通过清单来完成，该清单指定将使用 Azure 委托资源管理访问客户资源的 Azure AD 用户、组和服务主体，以及定义其访问级别的角色。 通过向 Azure AD 组而不是单个用户或应用程序帐户分配权限，可以在访问要求发生更改时添加或删除单个用户。

## <a name="public-and-private-offers"></a>公共和专用产品/服务

每个托管服务产品/服务均包含一个或多个计划。 计划可以是私有的，也可以是公共的。

如果要将产品/服务限制为特定客户，可以发布专用计划。 执行此操作时，只能为你提供的特定订阅 ID 购买计划。 有关详细信息，请参阅[专用产品/服务](../../marketplace/private-offers.md)。

可以利用公共计划将服务推广给新客户。 当只需要对客户租户的有限访问权限时，这些信息通常更合适。 建立与客户的关系后，如果他们决定向你的组织授予其他访问权限，你可以通过为该客户仅发布新的专用计划来完成此操作，或者通过[使用 Azure 资源管理器模板进行进一步访问来载入](../how-to/onboard-customer.md)。

如果需要，可以在同一个产品/服务中包括公用和专用计划。

> [!IMPORTANT]
> 一旦某个计划作为公共计划发布，则不能将其更改为 "专用"。 若要控制哪些客户可以接受您的产品/服务和委托资源，请使用私有计划。 使用公用计划，您不能将可用性限制为特定客户或甚至是特定数量的客户（不过，如果您选择这样做，您可以完全停止销售计划）。 仅当你在发布产品/服务时，如果你在将**角色定义**设置为 "[托管服务注册分配](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)" "角色 **"，则**可以在客户接受产品/服务后[删除对委派的访问权限](../how-to/remove-delegation.md)。 你还可以联系客户并要求他们[删除你的访问权限](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

## <a name="publish-managed-service-offers"></a>发布托管服务产品

要了解如何发布托管服务产品，请参阅[将托管服务产品发布到 Azure 市场](../how-to/publish-managed-services-offers.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委托的资源管理](azure-delegated-resource-management.md)和[跨租户管理体验](cross-tenant-management-experience.md)。
- 将[托管服务产品/服务发布](../how-to/publish-managed-services-offers.md)到 Azure Marketplace。
