---
title: 如何链接 Azure 订阅 - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure 订阅中启用 Azure AD B2C 租户计费的分步指南。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 892f47b6acf22c62ce2290e2ede9d0bcd21eefc8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065900"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>将 Azure 订阅链接到 Azure Active Directory B2C 租户

> [!IMPORTANT]
> 有关 Azure Active Directory B2C （Azure AD B2C）使用情况计费和定价的最新信息，请参阅[Azure AD B2C 价格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。

将在 Azure 订阅中计收 Azure AD B2C 的使用费。 创建 Azure AD B2C 租户后，租户管理员需要将 Azure AD B2C 租户显式链接到 Azure 订阅。 本文介绍相关实现方法。

> [!NOTE]
> 链接到 Azure AD B2C 租户的订阅可用于对 Azure AD B2C 使用情况或其他 Azure 资源（包括其他 Azure AD B2C 资源）进行计费。  不能使用该订阅在 Azure AD B2C 租户中添加其他基于 Azure 许可证的服务或 Office 365 许可证。

订阅链接是通过在目标 Azure 订阅中创建 Azure AD B2C“资源”实现的。 可在单个 Azure 订阅中创建许多 Azure AD B2C“资源”以及其他 Azure 资源（例如 VM、数据存储和逻辑应用）。 转到与订阅关联到的 Azure AD 租户，即可查看该订阅中的所有资源。

Azure AD B2C 中支持 Azure 云解决方案提供商 (CSP) 订阅。 可以使用 API 或 Azure 门户针对 Azure AD B2C 和所有 Azure 资源提供此功能。 CSP 订阅管理员可以链接、移动以及删除与 Azure AD B2C 的关系，所用方法与用于所有 Azure 资源的方法相同。 使用基于角色的访问控制管理 Azure AD B2C 不受 Azure AD B2C 租户与 Azure CSP 订阅之间的关联影响。 基于角色的访问控制是使用租户基角色实现的，不是使用基于订阅的角色实现的。

需要使用有效的 Azure 订阅才能继续操作。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

首先必须创建要将订阅链接到的 [Azure AD B2C 租户](active-directory-b2c-get-started.md)。 如果已创建 Azure AD B2C 租户，则可跳过此步骤。

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>在 Azure AD 租户中打开 Azure 门户显示 Azure 订阅

导航到显示 Azure 订阅的 Azure AD 租户。 打开 [Azure 门户](https://portal.azure.com)，并切换到显示了想要使用的 Azure 订阅的 Azure AD 租户。

![切换到 Azure AD 租户](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>在 Azure 市场中找到 Azure AD B2C

单击“创建资源”按钮。 在“搜索市场”字段中，输入 `Active Directory B2C`。

![市场搜索中包含“Active Directory B2C”的门户屏幕截图](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

在结果列表中，选择“Azure AD B2C”。

![已在结果列表中选择“Azure AD B2C”](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

此时会显示有关 Azure AD B2C 的详细信息。 若要开始配置新的 Azure Active Directory B2C 租户，请单击“创建”按钮。

在资源创建屏幕中，选择“将现有的 Azure AD B2C 租户链接到我的 Azure 订阅”。

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>在 Azure 订阅中创建 Azure AD B2C 资源

在资源创建对话框中，从下拉列表中选择一个 Azure AD B2C 租户。 可以看到你是其全局管理员的所有租户，以及已链接到订阅的租户。

将预先选择与 Azure AD B2C 租户域名匹配的 Azure AD B2C 资源名称

对于“订阅”，请选择你是其管理员的活动 Azure 订阅。

选择资源组和资源组位置。 此处所做的选择不会对 Azure AD B2C 租户位置、性能或计费状态造成影响。

![Azure 门户中的 Azure AD B2C 资源创建页](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租户资源

在 Azure 订阅中成功创建 Azure AD B2C 资源后，应会看到连同其他 Azure 资源一起添加了“B2C 租户”类型的新资源。

使用此资源可以：

- 导航到该订阅以查看计费信息。
- 转到 Azure AD B2C 租户
- 提交支持请求
- 将 Azure AD B2C 租户资源移到另一个 Azure 订阅或另一个资源组。

![Azure 门户中的 B2C 资源设置页](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.PNG)

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>更改 Azure AD B2C 租户计费订阅

如果源订阅和目标订阅存在于同一 Azure Active Directory 租户中，则可以将 Azure AD B2C 租户移到另一个订阅。

若要了解如何将 Azure 资源（如 Azure AD B2C 租户）移到其他订阅，请参阅[将资源移到新的资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)。

在开始移动之前，请务必阅读整篇文章，以充分了解此类移动的限制和要求。 除了移动资源的说明之外，它还包括一些重要信息，如移动前检查清单以及如何验证移动操作。

## <a name="known-issues"></a>已知问题

### <a name="self-imposed-restrictions"></a>自我施加的限制

用户可能已针对 Azure 资源创建建立了区域限制。 此限制可能会阻止创建 Azure AD B2C 资源。 若要消除此问题，请放宽此限制。

## <a name="next-steps"></a>后续步骤

针对每个 Azure AD B2C 租户完成这些步骤后，会根据 Azure Direct 或企业协议详细信息在 Azure 订阅中计费。

可以在选定的 Azure 订阅中查看用量和计费详细信息。 还可以使用[用量报告 API](active-directory-b2c-reference-usage-reporting-api.md) 查看详细的每日用量报告。
