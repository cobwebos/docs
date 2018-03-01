---
title: "如何将 Azure 订阅链接到 Azure AD B2C | Microsoft 文档"
description: "在 Azure 订阅中启用 Azure AD B2C 租户计费的分步指南。"
services: active-directory-b2c
documentationcenter: dev-center-name
author: parakhj
manager: mtillman
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2017
ms.author: parja
ms.openlocfilehash: c61cf24083ac3422e30dabf8514dcbd3aec0599d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="linking-an-azure-subscription-to-an-azure-ad-b2c-tenant"></a>将 Azure 订阅链接到 Azure AD B2C 租户

> [!IMPORTANT]
> 有关 Azure AD B2C 用量计费和定价的最新信息，请参阅以下网页：[Azure AD B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

将在 Azure 订阅中计收 Azure AD B2C 的使用费。 创建 Azure AD B2C 租户后，租户管理员需要将 Azure AD B2C 租户显式链接到 Azure 订阅。 本文介绍相关实现方法。

> [!NOTE]
> 链接到 Azure AD B2C 租户的订阅只可用于 Azure AD B2C 用量计费。 不能使用该订阅在 Azure AD B2C 租户中添加其他 Azure 服务或 Office 365 许可证。

 订阅链接是通过在目标 Azure 订阅中创建 Azure AD B2C“资源”实现的。 可在单个 Azure 订阅中创建许多 Azure AD B2C“资源”以及其他 Azure 资源（例如 VM、数据存储和逻辑应用）。 转到与订阅关联到的 Azure AD 租户，即可查看该订阅中的所有资源。

需要使用有效的 Azure 订阅才能继续操作。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

首先必须创建要将订阅链接到的 [Azure AD B2C 租户](active-directory-b2c-get-started.md)。 如果已创建 Azure AD B2C 租户，则可跳过此步骤。

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>在 Azure AD 租户中打开 Azure 门户显示 Azure 订阅

导航到显示 Azure 订阅的 Azure AD 租户。 打开 [Azure 门户](https://portal.azure.com)，并切换到显示了想要使用的 Azure 订阅的 Azure AD 租户。

![切换到 Azure AD 租户](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>在 Azure Marketplace 中找到 Azure AD B2C

单击“创建资源”按钮。 在“搜索 Marketplace”字段中，输入 `B2C`。

![添加突出显示的按钮，并在“搜索 Marketplace”字段中添加文本“Azure AD B2C”](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

在结果列表中，选择“Azure AD B2C”。

![已在结果列表中选择“Azure AD B2C”](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

此时会显示有关 Azure AD B2C 的详细信息。 若要开始配置新的 Azure Active Directory B2C 租户，请单击“创建”按钮。

在资源创建屏幕中，选择“将现有的 Azure AD B2C 租户链接到我的 Azure 订阅”。

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>在 Azure 订阅中创建 Azure AD B2C 资源

在资源创建对话框中，从下拉列表中选择一个 Azure AD B2C 租户。 可以看到你是其全局管理员的所有租户，以及已链接到订阅的租户。

将预先选择与 Azure AD B2C 租户域名匹配的 Azure AD B2C 资源名称

对于“订阅”，请选择你是其管理员的活动 Azure 订阅。

选择资源组和资源组位置。 此处所做的选择不会对 Azure AD B2C 租户位置、性能或计费状态造成影响。

![创建 B2C 资源](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>管理 Azure AD B2C 租户资源

在 Azure 订阅中成功创建 Azure AD B2C 资源后，应会看到连同其他 Azure 资源一起添加了“B2C 租户”类型的新资源。

使用此资源可以：

- 导航到该订阅以查看计费信息。
- 转到 Azure AD B2C 租户
- 提交支持请求
- 将 Azure AD B2C 租户资源移到另一个 Azure 订阅或另一个资源组。

![B2C 资源设置](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>已知问题

### <a name="csp-subscriptions"></a>CSP 订阅

目前，Azure AD B2C 租户**无法**链接到 CSP 订阅。

### <a name="self-imposed-restrictions"></a>自我施加的限制

用户可能已针对 Azure 资源创建建立了区域限制。 此限制可能会阻止创建 Azure AD B2C 资源。 若要消除此问题，请放宽此限制。

## <a name="next-steps"></a>后续步骤

针对每个 Azure AD B2C 租户完成这些步骤后，会根据 Azure Direct 或企业协议详细信息在 Azure 订阅中计费。

可以在选定的 Azure 订阅中查看用量和计费详细信息。 还可以使用[用量报告 API](active-directory-b2c-reference-usage-reporting-api.md) 查看详细的每日用量报告。
