---
title: 如何在 Microsoft 商业应用商店中创建 SaaS 产品/服务
description: 了解如何使用 Microsoft 合作伙伴中心的 "商用 Marketplace 门户"，在 Microsoft AppSource、Azure Marketplace 或通过云解决方案提供商 (CSP) 程序中创建新的软件即服务 (SaaS) 产品/服务。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 93b47d2147a7438ebd38a5ae36363950639ba414
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380677"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>如何在商业应用商店中创建 SaaS 产品/服务

作为一家商业市场发布商，你可以创建一个软件即服务 (SaaS) 产品/服务，因此潜在客户可以购买基于 SaaS 的技术解决方案。 本文介绍为 Microsoft 商业应用商店创建 SaaS 产品/服务的过程。

## <a name="before-you-begin"></a>准备阶段

如果尚未这样做，请阅读 [为商业应用商店计划 SaaS 产品/服务](plan-saas-offer.md)。 它将说明 SaaS 应用的技术要求，以及创建产品/服务时所需的信息和资产。 除非你计划发布简单的列表 (**与我联系** 列表选项) 在商业市场中，否则 SaaS 应用程序必须满足有关身份验证的技术要求。

## <a name="create-a-new-saas-offer"></a>创建新的 SaaS 产品/服务

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左侧导航菜单中，选择 "**商业市场**  >  **概述**"。
1. 在 "**概述**" 选项卡上，选择 " **+ 新建产品**/  >  **服务软件**"。

   :::image type="content" source="media/new-offer-saas.png" alt-text="说明左侧导航菜单和新产品/服务列表。":::

1. 在 " **新建产品/服务** " 对话框中，输入 **产品 ID**。 此 ID 在商业市场列表和 Azure 资源管理器模板的 URL 中可见（如果适用）。 例如，如果在此框中输入 " **测试/服务-1** "，则 "产品/服务" 网址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。
   + 帐户中的每个提议都必须具有唯一的产品 ID。
   + 只使用小写字母和数字。 其中可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。
   + 选择 " **创建**" 后，无法更改产品/服务 ID。

1. 输入“产品/服务别名”。 这是在合作伙伴中心内用于产品/服务的名称。

   + 此名称在商业应用商店中不可见，并且不同于向客户显示的产品/服务名称和其他值。
   + 选择 " **创建**" 后，不能更改该产品/服务别名。
1. 若要生成产品/服务并继续，请选择 " **创建**"。

## <a name="configure-your-saas-offer-setup-details"></a>配置你的 SaaS 产品/服务设置详细信息

在 " **产品/服务设置** " 选项卡上的 " **设置详细信息**" 下，你将选择是通过 Microsoft 销售产品/服务还是单独管理你的交易。 通过 Microsoft 提供的产品/服务称为 _事务产品/服务_，这意味着 microsoft 简化了发布者软件许可证的资金兑换。 有关这些选项的详细信息，请参阅 [列出选项](plan-saas-offer.md#listing-options) 和 [确定发布选项](determine-your-listing-type.md)。

1. 若要通过 Microsoft 销售并让我们为你提供事务，请选择 **"是"**。 继续 [启用测试驱动器](#enable-a-test-drive-optional)。

1. 若要通过商业应用商店列出你的产品/服务，请选择 " **否**"，然后执行以下操作之一：
   + 若要为你的产品/服务提供免费订阅，请选择 " **立即获取" (免费) **。 然后在显示的 " **提供 url** " 框中输入 URL， (以 *http* 或 *https*) 开头，客户可以 [使用 Azure Active Directory (Azure AD) 通过一次单击身份验证 ](marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)获得试用。 例如，`https://contoso.com/saas-app`。
   + 若要提供30天的免费试用版，请选择 " **免费试用**版"，然后在显示的 " **试用 url** " 框中输入 URL， (以 *http* 或 *https*) 开头，客户可以 [使用 Azure Active Directory (Azure AD) 通过一键式身份验证 ](marketplace-saas-applications-technical-publishing-guide.md#using-azure-active-directory-to-enable-trials)访问免费试用版。 例如，`https://contoso.com/trial/saas-app`。
   + 若要让潜在客户联系你购买产品/服务，请选择 " **与我联系**"。

### <a name="enable-a-test-drive-optional"></a> (可选) 启用测试驱动器

测试驱动器是向潜在客户展示您的产品/服务的一种很好的方式，通过在固定的小时数内提供对预配置环境的访问权限。 提供一个测试驱动器将导致转换速率增加，并生成高度合格的潜在顾客。 若要了解有关测试驱动器的详细信息，请参阅 [什么是测试驱动器？](partner-center-portal/test-drive.md)。

> [!TIP]
> 测试驱动器与免费试用版不同。 可以提供测试驱动器、免费试用版或同时提供两者。 它们都向客户提供固定时间段的解决方案。 但在实际实施方案中，一个测试驱动器还包括一项针对您的产品关键功能的动手自学教程。

**启用测试驱动器**
1.  在 " **测试驱动器**" 下，选中 " **启用测试驱动器** " 复选框。
1.  从显示的列表中选择 "测试驱动器" 类型。

### <a name="configure-lead-management"></a>配置潜在客户管理

将客户关系管理 (CRM) 系统与你的商业 marketplace 产品/服务相连接，以便在客户表达兴趣或部署你的产品时可以接收客户联系信息。 你可以在创建产品/服务的过程中或之后随时修改此连接。

> [!NOTE]
> 如果你通过 Microsoft 销售产品/服务，或者选择了 " **联系我** " 列表选项，则必须配置 "潜在客户管理"。 有关详细指南，请参阅 [商业应用商店的潜在客户管理](lead-management-for-cloud-marketplace.md)。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>在合作伙伴中心配置连接详细信息

1.  在 " **客户主管**" 下，选择 " **连接** " 链接。
1. 在 " **连接详细信息** " 对话框中，从列表中选择潜在顾客目标。
1. 填写显示的字段。 有关详细步骤，请参阅以下文章：

   - [将你的产品/服务配置为将潜在顾客发送到 Azure 表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [将你的产品/服务配置为将潜在顾客发送到 dynamics 365 客户参与](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (以前的 Dynamics CRM Online) 
   - [配置你的产品/服务以将潜在客户发送到 HTTPS 终结点](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [配置你的产品/服务以将潜在顾客发送到 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [配置你的产品/服务以将潜在顾客发送到 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要验证提供的配置，请选择 " **验证** " 链接。
1. 若要关闭对话框，请选择 **"确定"**。

## <a name="next-steps"></a>后续步骤

- [如何配置 SaaS 产品/服务属性](create-new-saas-offer-properties.md)
