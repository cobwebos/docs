---
title: "为 Azure Active Directory 中的 Office 应用程序启用或禁用 LinkedIn 集成 | Microsoft Docs"
description: "介绍如何为 Azure Active Directory 中的 Microsoft 应用启用或禁用 LinkedIn 集成"
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/28/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: cdfb5458b020e9d3a3f33cecbeb0ee7b9a48909d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="linkedin-integration-for-office-applications"></a>Office 应用程序的 LinkedIn 集成
本文介绍如何将用户访问权限限制给 Azure Active Directory (Azure AD) 中提供了 LinkedIn 集成的用户。 将 LinkedIn 集成添加到租户时，默认会启用该集成，这样，用户便可以在其某些 Microsoft 应用中访问公开的 LinkedIn 数据。 每个用户可以独立地选择将其工作或学校帐户连接到其 LinkedIn 帐户。

> [!IMPORTANT]
> 不会同时将 LinkedIn 集成部署到所有 Azure AD 租户。 将 LinkedIn 集成推出到 Azure 租户后，默认会启用该集成。 LinkedIn 集成 不可用于本地化租户、主权租户和政府租户。 有关推出信息的最新视图，请参阅 [Office 365 路线图](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc)页。

## <a name="linkedin-integration-from-the-user-perspective"></a>从用户的角度来看 LinkedIn 集成
当组织中的用户将其 LinkedIn 帐户连接到其工作或学校帐户时，即表示[他们允许 LinkedIn 提供所需的数据](https://www.linkedin.com/help/linkedin/answer/84077)，以便在组织提供的 Microsoft 应用和服务中使用。 [用户可以断开连接帐户](https://www.linkedin.com/help/linkedin/answer/85097)，这样，便会删除 LinkedIn 与 Microsoft 共享数据的权限。 LinkedIn 集成使用公开的 LinkedIn 配置文件信息。 用户可以使用 LinkedIn 隐私设置（包括是否可以在 Microsoft 应用中查看其个人资料），来[控制其自己的 LinkedIn 个人资料的查看方式](https://www.linkedin.com/help/linkedin/answer/83)。

## <a name="privacy-considerations"></a>隐私注意事项
在 Azure AD 中启用 LinkedIn 集成可让 Microsoft 应用和服务访问用户的一些 LinkedIn 信息。 在 Azure AD 中启用 LinkedIn 集成时，阅读 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement/)，了解有关隐私注意事项的详细信息。 

## <a name="manage-linkedin-integration"></a>管理 LinkedIn 集成
在 Azure AD 中，面向企业的 LinkedIn 集成默认情况下已启用。 启用 LinkedIn 集成可让组织中的所有用户使用 Microsoft 服务中的 LinkedIn 功能，例如，在 Outlook 中查看 LinkedIn 个人资料。 禁用 LinkedIn 集成会从 Microsoft 应用和服务中删除 LinkedIn 功能，并停止通过 Microsoft 服务在 LinkedIn 与组织之间共享数据的功能。

### <a name="enable-or-disable-linkedin-integration-for-your-organization-in-the-azure-portal"></a>在 Azure 门户中为组织启用或禁用 LinkedIn 集成

1. 使用 Azure AD 租户的全局管理员帐户登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)。
2. 选择“用户”。
3. 在“用户”边栏选项卡中，选择“用户设置”。
4. 在“LinkedIn 集成”下，选择“是”或“否”，以启用或禁用 LinkedIn 集成。
   ![启用 LinkedIn 集成](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="enable-or-disable-linkedin-integration-for-your-organizations-office-2016-apps-using-group-policy"></a>使用组策略为组织的 Office 2016 应用启用或禁用 LinkedIn 集成

1. 下载 [Office 2016 管理模板文件 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. 提取 **ADMX** 文件，并将其复制到**中心存储库**。
3. 打开组策略管理。
4. 使用以下设置创建一个组策略对象：“用户配置” > “管理模板” > “Microsoft Office 2016” > “杂项” > “允许 LinkedIn 集成”。
5. 选择“已启用”或“已禁用”。
  * 如果该策略为“已启用”，则会启用 Office 2016“选项”对话框中的“在 Office 应用程序中显示 LinkedIn 功能”。 这也意味着，组织中的用户可在其 Office 应用程序中使用 LinkedIn 功能。
  * 如果该策略为“已禁用”，则 Office 2016“选项”对话框中的“在 Office 应用程序中显示 LinkedIn 功能”将设置为禁用状态，并且最终用户无法更改此设置。 组织中的用户无法在其 Office 2016 应用程序中使用 LinkedIn 功能。 

此组策略只会影响本地计算机上的 Office 2016 应用。 即使用户在其 Office 2016 应用中禁用了 LinkedIn，也能在整个 Office 365 的个人资料卡片中看到 LinkedIn 功能。 

### <a name="learn-more"></a>了解详细信息 
* [Microsoft 应用中的 LinkedIn 信息和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 帮助中心](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>后续步骤
使用以下链接查看 Azure 门户中的当前 LinkedIn 集成设置：

[配置 LinkedIn 集成](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 