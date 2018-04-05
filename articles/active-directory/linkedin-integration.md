---
title: 为 Azure Active Directory 中的 Microsoft 应用和服务启用 LinkedIn 连接 | Microsoft Docs
description: 介绍如何为 Azure Active Directory 中的 Microsoft 应用启用或禁用 LinkedIn 帐户连接
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/22/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 33e3305288edc3990ed88b39c819293a8adc2dfe
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="linkedin-account-connections-for-microsoft-apps-and-services"></a>Microsoft 应用和服务的 LinkedIn 帐户连接
本文介绍如何在 Azure Active Directory (Azure AD) 管理中心管理租户的 LinkedIn 帐户连接。 

> [!IMPORTANT]
> 我们目前正在推出适用于 Azure AD 租户的 LinkedIn 帐户连接功能。 针对你的租户推出此功能后，默认会启用此功能。 此功能不适用于美国政府客户，以及使用托管在澳大利亚、加拿大、中国、法国、德国、印度、韩国、英国、日本和南非的 Exchange Online 邮箱的组织。 对这些邮箱位置的支持即将推出。  有关推出信息的最新视图，请参阅 [Office 365 路线图](https://products.office.com/business/office-365-roadmap?filters=%26freeformsearch=linkedin#abc)页。

## <a name="how-linkedin-account-connections-appear-to-the-user"></a>如何向用户显示 LinkedIn 帐户连接
用户可以使用 LinkedIn 帐户连接在其某些 Microsoft 应用中查看公开的 LinkedIn 个人资料信息。 租户中的用户可以选择连接其 LinkedIn 和 Microsoft 工作或学校帐户，以查看更多的 LinkedIn 个人资料信息。 有关详细信息，请参阅 [Microsoft 应用和服务中的 LinkedIn 信息与功能](https://go.microsoft.com/fwlink/?linkid=850740)。

当组织中的用户连接其 LinkedIn 和 Microsoft 工作或学校帐户时，可以做出两种选择： 
* 授予在两个帐户之间共享数据的权限。 这意味着，他们可以向 LinkedIn 帐户授予与 Microsoft 工作或学校帐户共享数据的权限，以及向 Microsoft 工作或学校帐户授予与 LinkedIn 帐户共享数据的权限。 与 LinkedIn 共享的数据会脱离联机服务。 
* 授予仅限在 LinkedIn 帐户中向 Microsoft 工作和学校帐户共享数据的权限

有关在用户 LinkedIn 帐户与 Microsoft 工作或学校帐户之间共享的数据的详细信息，请参阅 [Microsoft 工作或学校应用程序中的 LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077)。 
* [用户随时可以断开帐户连接](https://www.linkedin.com/help/linkedin/answer/85097)并删除数据共享权限。 
* [用户可以控制其自己的 LinkedIn 个人资料的查看方式](https://www.linkedin.com/help/linkedin/answer/83)，包括是否可以在 Microsoft 应用中查看其个人资料。

## <a name="privacy-considerations"></a>隐私注意事项
启用 LinkedIn 帐户连接可让 Microsoft 应用和服务访问用户的某些 LinkedIn 信息。 请参阅 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement/)，详细了解在 Azure AD 中启用 LinkedIn 帐户连接时的隐私注意事项。 

## <a name="manage-linkedin-account-connections"></a>管理 LinkedIn 帐户连接
默认已经为整个租户启用了 LinkedIn 帐户连接功能。 可以选择针对整个租户禁用 LinkedIn，或者针对租户中的选定用户启用 LinkedIn 帐户连接。 

### <a name="enable-or-disable-linkedin-account-connection-for-your-tenant-in-the-azure-portal"></a>在 Azure 门户中为租户启用或禁用 LinkedIn 帐户连接

1. 使用 Azure AD 租户的全局管理员帐户登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)。
2. 选择“用户”。
3. 在“用户”边栏选项卡中，选择“用户设置”。
4. 在“LinkedIn 帐户连接”下：
  * 选择“是”，为租户中的所有用户启用 LinkedIn 帐户连接
  * 选择“选定”，只为选定的租户用户启用 LinkedIn 帐户连接
  * 选择“否”，针对所有用户禁用 LinkedIn 帐户连接 ![启用 LinkedIn 帐户连接](./media/linkedin-integration/LinkedIn-integration.png)
5. 设置完成后，选择“保存”来保存设置。

### <a name="enable-or-disable-linkedin-account-connections-for-your-organizations-office-2016-apps-using-group-policy"></a>使用组策略为组织的 Office 2016 应用启用或禁用 LinkedIn 帐户连接

1. 下载 [Office 2016 管理模板文件 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
2. 提取 ADMX 文件然后将其复制到中央存储。
3. 打开组策略管理。
4. 使用以下设置创建组策略对象：“用户配置” > “管理模板” > “Microsoft Office 2016” > “杂项” > “在 Office 应用程序中显示 LinkedIn 的功能”。
5. 选择“已启用”或“已禁用”。
  * 如果该策略为“已启用”，则会启用 Office 2016“选项”对话框中的“在 Office 应用程序中显示 LinkedIn 功能”。 这也意味着，组织中的用户可在其 Office 应用程序中使用 LinkedIn 功能。
  * 如果该策略为“已禁用”，则 Office 2016“选项”对话框中的“在 Office 应用程序中显示 LinkedIn 功能”将设置为禁用状态，并且最终用户无法更改此设置。 组织中的用户无法在其 Office 2016 应用程序中使用 LinkedIn 功能。 

此组策略只会影响本地计算机上的 Office 2016 应用。 即使用户在其 Office 2016 应用中禁用了 LinkedIn，也能在整个 Office 365 的个人资料卡片中看到 LinkedIn 功能。 

### <a name="learn-more"></a>了解详细信息 
* [Microsoft 应用中的 LinkedIn 信息和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 帮助中心](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>后续步骤
使用以下链接查看 Azure 门户中的当前 LinkedIn 帐户连接设置：

[配置 LinkedIn 帐户连接](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 