---
title: Azure Lighthouse 方案中的租户、角色和用户
description: 了解 Azure Active Directory 租户、用户和角色的概念，以及如何在 Azure Lighthouse 方案中使用它们。
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 11/05/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: b87ef8534dab2c8f08aa8cdee9d939e2d1a3a0e7
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73616035"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Azure Lighthouse 方案中的租户、角色和用户

在将客户加入 [Azure 委派资源管理](azure-delegated-resource-management.md)之前，请务必了解 Azure Active Directory (Azure AD) 租户、用户和角色的工作原理以及如何在 Azure Lighthouse 方案中使用它们。

租户是 Azure AD 的专用受信任的实例  。 通常，每个租户表示一个组织。 通过 Azure 委派资源管理，可以将资源从一个租户逻辑投影到另一个租户。 这样一来，管理租户中的用户（例如属于服务提供商的用户）可以访问客户租户中的委派资源，或者让[具有多个租户的企业集中其管理操作](enterprise.md)。

为了实现此逻辑投影，必须加入客户租户中的订阅（或订阅中的一个或多个资源组）以进行 Azure 委派资源管理  。 可以[通过 Azure 资源管理器模板](../how-to/onboard-customer.md)或[将公共或私有产品/服务发布到 Azure 市场](../how-to/publish-managed-services-offers.md)来完成此加入过程。

无论选择哪种加入方法，都需要定义授权  。 每个授权在管理租户中指定可以访问委派资源的用户帐户，还指定用于设置其中每个用户对这些资源的权限的内置角色。

## <a name="role-support-for-azure-delegated-resource-management"></a>Azure 委派资源管理的角色支持

定义授权时，必须为每个用户帐户分配其中某个[基于角色的访问控制 (RBAC) 内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。 不支持自定义角色和[经典订阅管理员角色](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators)。

Azure 委派资源管理目前支持所有[内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)，但有以下例外：

- 不支持[所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)角色。
- 不支持具有 [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) 权限的任何内置角色。
- 支持[用户访问管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator)内置角色，但仅限用于[向客户租户中的托管标识分配角色](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)。 此角色通常会授予的其他权限不适用。 如果定义具有此角色的用户，则还必须指定该用户可以分配给托管标识的内置角色。

## <a name="best-practices-for-defining-users-and-roles"></a>定义用户和角色的最佳做法

创建授权时，建议使用以下最佳做法：

- 在大多数情况下，需要将权限分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。
- 请务必遵循最低权限原则，使用户仅具有完成作业所需的权限，从而帮助减少意外错误发生的几率。 有关详细信息，请参阅[建议的安全做法](../concepts/recommended-security-practices.md)。
- 包括具有[托管服务注册分配删除角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role)的用户，以便以后可以随时[删除对委派的访问权限](../how-to/onboard-customer.md#remove-access-to-a-delegation)。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。
- 确保需要[查看 Azure门户中的“我的客户”页](../how-to/view-manage-customers.md)的所有用户都具有[读者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)角色（或包含读者访问权限的其他内置角色）。

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure 委派资源管理的推荐安全做法](recommended-security-practices.md)。
- 要将客户加入 Azure 委派资源管理，可以[使用 Azure 资源管理器模板](../how-to/onboard-customer.md)，或[将专用或公共托管服务发布到 Microsoft Azure 市场](../how-to/publish-managed-services-offers.md)。
