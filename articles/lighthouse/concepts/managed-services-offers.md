---
title: Azure 市场中的托管服务产品
description: 托管服务产品/服务允许你向 Azure Marketplace 中的客户销售资源管理产品/服务。
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 6c3047cd95128f689e75d9c1f5fba5a39f86291c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88163316"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure 市场中的托管服务产品

本文介绍[Azure Marketplace](https://azuremarketplace.microsoft.com)中的**托管服务**提供类型。 托管服务产品/服务允许你通过 [Azure Lighthouse](../overview.md)向客户提供资源管理服务。 你可以向所有潜在客户提供这些产品/服务，也可以仅向一个或多个特定客户提供这些产品/服务。 由于直接向客户收取与这些托管服务相关的成本，因此 Microsoft 不收取任何费用。

## <a name="understand-managed-service-offers"></a>了解托管服务产品

托管服务提供简化将客户加入 Azure Lighthouse 的过程。 客户在 Azure 市场中购买产品/服务时，他们将能够指定应加入哪些订阅和/或资源组。

之后，你的组织中的用户可以根据创建产品/服务时定义的访问权限，通过 [Azure 委托资源管理](azure-delegated-resource-management.md)从管理租户中处理这些资源。 这是通过一个清单来完成的，该清单指定 Azure Active Directory (Azure AD 将有权访问客户资源的) 用户、组和服务主体，以及定义其访问级别的角色。 通过向 Azure AD 组而不是单个用户或应用程序帐户分配权限，可以在访问要求发生更改时添加或删除单个用户。

## <a name="public-and-private-offers"></a>公共和专用产品/服务

每个托管服务产品/服务包含一个或多个计划。 计划可以是私有的，也可以是公共的。

如果要将产品/服务限制为特定客户，可以发布专用计划。 当你执行此操作时，只能为你提供的特定订阅 Id 购买计划。 有关详细信息，请参阅[专用产品/服务](../../marketplace/private-offers.md)。

> [!NOTE]
> 通过云解决方案提供商的经销商 (CSP) program 建立的订阅不支持专用产品/服务。

可以利用公共计划将服务推广给新客户。 当只需要对客户租户的有限访问权限时，这些信息通常更合适。 建立与客户的关系后，如果他们决定向你的组织授予其他访问权限，你可以通过为该客户仅发布新的专用计划来完成此操作，或者通过[使用 Azure 资源管理器模板进行进一步访问来载入](../how-to/onboard-customer.md)。

如果需要，可以在同一个产品/服务中包括公用和专用计划。

> [!IMPORTANT]
> 一旦某个计划已发布为公用，就无法将其更改为专用。 若要控制哪些客户可以接受产品/服务并委派资源，请使用专用计划。 使用公用计划时，不能将其可用性限制为特定客户，甚至不能限制特定数量的客户（不过，如果选择这样做，可完全停止销售计划）。 仅当在发布产品/服务时，包含将“角色定义”设置为[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的“授权”的情况下，才能在客户接受产品/服务后[删除对委派的访问权限](../how-to/remove-delegation.md)。 你还可以与客户联系，要求他们[删除你的访问权限](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

## <a name="publish-managed-service-offers"></a>发布托管服务产品

若要了解如何发布托管服务产品/服务，请参阅将 [托管服务产品/服务发布到 Azure Marketplace](../how-to/publish-managed-services-offers.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委托的资源管理](azure-delegated-resource-management.md)和[跨租户管理体验](cross-tenant-management-experience.md)。
- 将[托管服务产品/服务发布](../how-to/publish-managed-services-offers.md)到 Azure Marketplace。
