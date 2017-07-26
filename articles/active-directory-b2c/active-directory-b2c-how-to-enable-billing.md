---
title: "如何将 Azure 订阅链接到 Azure AD B2C | Microsoft 文档"
description: "在 Azure 订阅中启用 Azure AD B2C 租户计费的分步指南。"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.contentlocale: zh-cn
ms.lasthandoff: 05/23/2017


---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>将 Azure 订阅链接到 Azure B2C 租户以支付使用费

Azure Active Directory B2C (Azure AD B2C) 不断发生的使用费将在 Azure 订阅中计费。 创建 B2C 租户本身后，租户管理员必须将 Azure AD B2C 租户显式链接到 Azure 订阅。  这种链接是通过在目标 Azure 订阅中创建 Azure AD“B2C 租户”资源实现的。 许多 B2C 租户可链接到单个 Azure 订阅以及其他 Azure 资源（例如 VM、数据存储和 LogicApps）


> [!IMPORTANT]
> 有关 B2C 使用计费和定价的最新信息，请参阅以下网页：[Azure AD B2C 定价](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>步骤 1 - 创建 Azure AD B2C 租户
首先必须完成 B2C 租户的创建。 如果已创建目标 B2C 租户，则可跳过此步骤。 [Azure AD B2C 入门](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>步骤 2 - 在 Azure AD 租户中打开 Azure 门户显示 Azure 订阅
导航到 [Azure 门户](https://portal.azure.com)。 切换到 Azure AD 租户，显示想要使用的 Azure 订阅。 此 Azure AD 租户不同于 B2C 租户。 在 Azure 门户中，单击仪表板右上角的帐户名选择 Azure AD 租户。 需要使用 Azure 订阅才能继续操作。 [获取 Azure 订阅](https://account.windowsazure.com/signup?showCatalog=True)

![切换到 Azure AD 租户](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>步骤 3 - 在 Azure 应用商店中创建 B2C 租户资源
单击“应用商店”图标，或者选择仪表板左上角的绿色“+”符号，打开应用商店。  搜索并选择 Azure Active Directory B2C。 选择“创建”。

![选择“Marketplace”](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![搜索 AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

Azure AD B2C 资源创建对话框中包含以下参数：

1. Azure AD B2C 租户 – 从下拉列表中选择一个 Azure AD B2C 租户。  此时仅显示符合条件的 Azure AD B2C 租户。  符合条件的 B2C 租户是指满足以下条件：你是 B2C 租户的全局管理员，B2C 租户目前未关联到 Azure 订阅

2. Azure AD B2C 资源名称 - 已预先选定，与 B2C 租户的域名匹配

3. 订阅 - 你是其中的管理员或协同管理员的有效 Azure 订阅。  可将多个 Azure AD B2C 租户添加到一个 Azure 订阅

4. 资源组和资源组位置 - 此项目可帮助组织多个 Azure 资源。  选择此选项对 B2C 租户位置、性能或计费状态不会造成影响

5. 固定到仪表板可以十分方便地访问 B2C 租户计费信息和 B2C 租户设置 ![创建 B2C 资源](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>步骤 4 - 管理 B2C 租户资源（可选）
完成部署后，将在目标资源组和相关的 Azure 订阅中创建新的“B2C 租户”资源。  应会看到添加了一个“B2C 租户”类型的新资源以及其他 Azure 资源。

![创建 B2C 资源](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

单击 B2C 租户资源可以
- 单击“订阅名称”可以查看计费信息。 请参阅“计费和使用情况”。
- 单击“Azure AD B2C 设置”可在新的浏览器标签页中直接打开“B2C 租户设置”边栏选项卡
- 提交支持请求
- 将 B2C 租户资源移到另一个 Azure 订阅或另一个资源组。  此选项会更改产生使用费的 Azure 订阅。

![B2C 资源设置](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>已知问题
- B2C 租户删除。 如果创建、删除某个 B2C 租户，然后使用相同的域名创建它，请同时删除“链接”资源并使用相同的域名重新创建该资源。  可通过 Azure 门户在订阅租户中的“所有资源”下面找到此“链接”资源。
- 针对区域资源位置自我施加的限制。  在极少数情况下，用户可能已针对 Azure 资源创建建立了区域限制。  这种限制可能会阻止在 Azure 订阅与 B2C 租户之间创建链接。 若要消除此问题，请放宽此限制。

## <a name="next-steps"></a>后续步骤
针对每个 B2C 租户完成这些步骤后，将会根据 Azure Direct 或企业协议详细信息在 Azure 订阅中计费。
- 在选定的 Azure 订阅中查看使用情况和计费
- 通过[使用报告 API](active-directory-b2c-reference-usage-reporting-api.md) 查看详细的每日使用报告

