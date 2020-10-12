---
title: 如何在 Microsoft 合作伙伴中心添加 SaaS 产品/服务的技术详细信息
description: 了解如何为 Microsoft 商用 marketplace (SaaS) 产品/服务的服务提供技术详细信息。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 78a81a2d392363ac45979fd28138f838653a2358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89380679"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>如何添加 SaaS 产品/服务的技术详细信息

本文介绍如何输入有助于 Microsoft 商用 marketplace 连接到解决方案的技术详细信息。 此连接使我们能够为客户预配你的产品/服务，前提是他们选择获取并管理你的产品。 有关这些设置的详细信息，请参阅 [技术信息](plan-saas-offer.md#technical-information)。

> [!NOTE]
> 如果选择单独处理事务，则不会看到此选项。 相反，请跳到 [如何推销 SaaS 产品/服务](create-new-saas-offer-marketing.md)。

## <a name="technical-configuration"></a>技术配置

在 " **技术配置** " 选项卡上，你将定义商业应用商店用于与 SaaS 应用程序或解决方案通信的技术详细信息。 

- **登录页 URL** (必需的) –定义 SAAS 网站 URL (例如：在 `https://contoso.com/signup` 从商业市场获取产品/服务并从新创建的 SaaS 订阅触发配置过程后，客户将居住在其上的) 。

  > [!IMPORTANT]
  > 登录页应启动并运行24/7。 这是您在商业应用商店中购买的 SaaS 产品或产品/服务的配置请求的新购买通知。

- **连接 webhook** (必需的) –对于 Microsoft 需要发送给你的所有异步事件 (例如，已取消 SaaS 订阅) ，我们需要你提供连接 webhook URL。 我们将调用此 URL 来通知你事件。

  > [!IMPORTANT]
  > 你的 webhook 应启动并运行24/7，因为这是你将会收到有关通过 comercial marketplace 购买的客户 SaaS 订阅更新的通知。

- **Azure Active Directory 租户 id** (必需) –若要查找 Azure Active Directory (Azure AD 应用的租户 id，请在) 中转到 [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 边栏选项卡。 在 " **显示名称** " 列中，选择应用。 然后查找 (列出的 **目录 (租户) ID** 号，例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。

- **Azure Active Directory 应用程序 id** (必需) –若要查找 [应用程序 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)，请在 Azure Active Directory 中转到 [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 边栏选项卡。 在 " **显示名称** " 列中，选择应用。 然后查找 (列出的应用程序 (客户端) ID 号，例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。

选择 " **保存草稿** "，然后继续下一步选项卡：计划概述。

## <a name="next-steps"></a>后续步骤

- [如何为 SaaS 产品/服务创建计划](create-new-saas-offer-plans.md)。
