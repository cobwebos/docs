---
title: Azure 市场中的托管服务产品
description: 服务提供商可借助托管服务产品向 Azure 市场中的客户销售资源管理服务。
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: f7dcaa34b1fb471823acd74a7d9b05e42dd44161
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463996"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Azure 市场中的托管服务产品

本文介绍 **Azure 市场**中的“新托管服务产品”类型[](https://azuremarketplace.microsoft.com)。 托管服务产品通过 Azure 委派资源管理为客户提供资源管理服务。 你可以向所有潜在客户提供这些产品/服务，也可以仅向一个或多个特定客户提供这些产品/服务。 由于直接向客户收取与这些托管服务相关的成本，因此 Microsoft 不收取任何费用。

## <a name="understand-managed-services-offers"></a>了解托管服务产品

托管服务产品简化了 Azure 委托资源管理的客户的管理过程。 客户在 Azure 市场中购买产品/服务时，他们将能够指定应加入哪些订阅和/或资源组。 请注意，必须先通过手动注册 **Microsoft.ManagedServices** 资源提供程序来对订阅进行加入授权。

之后，你组织中的用户将能够根据你在[云合作伙伴门户](https://cloudpartner.azure.com/)中创建产品/服务时定义的访问权限，从你组织的租户中对这些资源执行管理任务。 这通过清单来完成，该清单指定将使用 Azure 委托资源管理访问客户资源的 Azure AD 用户、组和服务主体，以及定义其访问级别的角色。 通过向 Azure AD 组而不是单个用户或应用程序帐户分配权限，可以在访问要求发生更改时添加或删除单个用户。

## <a name="public-and-private-offers"></a>公共和专用产品/服务

每个托管服务产品/服务均包含一个或多个计划。 这些计划可以是专用或公用。

如果要将产品/服务限制为特定客户，可以发布专用计划。 执行此操作时，只能为你提供的特定订阅 ID 购买计划。 有关详细信息，请参阅[专用产品/服务](https://docs.microsoft.com/azure/marketplace/private-offers)。

可以利用公共计划将服务推广给新客户。 当只需要对客户租户的有限访问权限时，这些信息通常更合适。 建立与客户的关系后，如果他们决定向你的组织授予其他访问权限，你可以通过为该客户仅发布新的专用计划来完成此操作，或者通过[使用 Azure 资源管理器模板进行进一步访问来载入](../how-to/onboard-customer.md)。

请记住，一旦某个计划已发布为公用，就无法将其更改为专用。 此外，不能将公用计划的可用性限制为特定客户，甚至不能限制特定数量的客户，但如果你选择这样做，则可以完全停止销售计划。

如果需要，可以在同一个产品/服务中包括公用和专用计划。

## <a name="publish-managed-service-offers"></a>发布托管服务产品

要了解如何发布托管服务产品，请参阅[将托管服务产品发布到 Azure 市场](../how-to/publish-managed-services-offers.md)。 有关使用云合作伙伴门户发布到 Azure 市场的常规信息，请参阅 [Azure 市场和 AppSource 发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)以及[管理 Azure 和 AppSource 市场产品/服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委托的资源管理](azure-delegated-resource-management.md)和[跨租户管理体验](cross-tenant-management-experience.md)。
- 向 Azure 市场[发布托管服务](../how-to/publish-managed-services-offers.md)。
