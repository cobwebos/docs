---
title: 托管的测试驱动器 |Azure Marketplace
description: 如何设置和维护市场托管体验版
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2ce43402fb283cce09687bc7226770731f1bae0d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827519"
---
# <a name="hosted-test-drive"></a>托管体验版

托管体验版消除了 Microsoft 托管设置以及用于执行体验版用户预配和取消预配的服务的维护工作的复杂性。 本文面向在 AppSource 中发布了套餐，或者正在生成新的套餐并想要提供可与 Dynamics 365 for Customer Engagement、Dynamics 365 for Finance and Operations 或 Dynamics 365 Business Central 实例连接的托管体验版的发布者。

## <a name="how-to-publish-a-test-drive"></a>如何发布体验版

导航到现有套餐，或创建新套餐。

在边侧菜单中选择“体验版”选项。

对“启用体验版”选项选择“是”。

填写“详细信息”部分中的以下字段。

- **说明**：提供体验版的概述。 预配体验版时，会向用户显示此文本。 若要提供带格式的内容，可在此字段中输入 HTML 内容。
- **用户手册**：上传详细的用户手册（.pdf 类型的文件），以帮助体验版用户了解如何使用你的应用。
- **体验版演示视频**：（可选）上传一个视频用于展示应用。

遵照[此处](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)所述的说明，授予在租户中预配和取消预配体验版用户的 AppSource 权限。

此步骤将生成下面所述的“Azure AD 应用 ID”和“Azure AD 应用密钥”值。

填写“技术配置”部分中的以下字段：

- **体验版类型**：选择“Microsoft 托管(例如 Dynamics 365 for Customer Engagement)”选项。 这表示 Microsoft 将托管和维护用于执行体验版用户预配和取消预配的服务。
- **最大并发体验版数**：请将此字段设置为在任意给定时间点可以使用活动体验版的并发用户数。 当每个用户的体验版处于活动状态时，该用户将使用一个 Dynamics 许可证，因此，你需要确保至少有这么多的 Dynamics 许可证可供体验版用户使用。 建议的值为 3-5。
- **体验版持续时间(小时)** ：请将此字段设置为用户体验版处于活动状态的小时数。 此小时数过后，将从租户中取消预配该用户。 建议的值为 2-24 小时，具体取决于应用的复杂性。 如果用户已耗尽时间并想要再次访问体验版，始终可以请求另一个体验版。
- **实例 URL**：提供体验版用户在启动体验版时最初导航到的 URL。 这通常是应用和示例数据所安装到的 Dynamics 365 实例的 URL。 示例值： https：\//testdrive.crm.dynamics.com
- **Azure AD 租户 ID**：提供 Dynamics 365 实例的 Azure 租户 ID。 若要检索此值，请登录到 Azure 门户并导航到“Azure Active Directory”\'\' 从菜单边栏选项卡中选择“属性”- - 复制“目录 ID”。 示例值：72f988bf-86f1-41af-91ab-2d7cd0111234
- **Azure AD 应用 ID**：在步骤 7 中创建的 Azure AD 应用的 ID。\示例值：53852862-a2ae-4e43-9461-faa49650a096
- **Azure AD 应用密钥**：在步骤 7 中创建的 Azure AD 应用的机密。\示例值：IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Azure AD 租户名称**：提供 Dynamics 365 实例的 Azure 租户名称。 使用 \<tenantname.\>onmicrosoft.com 格式。 示例值：testdrive.onmicrosoft.com
- **实例 Web API URL**：提供 Dynamics 365 实例的 Web API URL。 可以登录到 Microsoft Dynamics 365 实例，并导航“设置”-\>“自定义”-\>“开发人员资源”-\>“实例 Web API”（复制此 URL）来检索此值。 示例值： https：\//testdrive.crm.dynamics.com/api/data/v9。0 
- **角色名称**：提供为体验版创建的自定义 Dynamics 365 安全角色的名称。 这是在用户使用体验版期间为其分配的角色。 示例值： testdriverole

## <a name="next-steps"></a>后续步骤

准备好**发布**套餐时，在应用通过认证后，你将获得套餐的**预览版**。 请在 UI 中启动体验版，并验证体验版是否正常运行。 如果对预览版套餐感到满意，现在就可以将它**上线**！
