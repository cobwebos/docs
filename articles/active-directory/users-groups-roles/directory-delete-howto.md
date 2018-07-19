---
title: 删除 Azure Active Directory 租户目录 | Microsoft Docs
description: 介绍如何为删除 Azure AD 租户目录做准备
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/13/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 44781c976afa94ebe3c8df8c7fd70c3e16956916
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37870241"
---
# <a name="delete-an-azure-active-directory-tenant"></a>删除 Azure Active Directory 租户
删除租户时，也会删除包含在该租户中的所有资源。 最小化租户及其关联的资源，做好准备后再将其删除。 只有 Azure Active Directory (Azure AD) 全局管理员可从门户中删除 Azure AD 租户。

## <a name="prepare-the-tenant-for-deletion"></a>准备租户以便进行删除

除非 Azure AD 中的租户通过了相应检查，否则无法将其删除。 这些检查可以降低删除租户后对用户访问造成负面影响（例如，影响登录 Office 365 或访问 Azure 中的资源）的风险。 例如，如果意外删除了与订阅关联的租户，则用户将无法访问该订阅的 Azure 资源。 下面介绍了检查的条件：

* 租户中除存在一个全局管理员以删除租户外，可以不存在任何用户。 只有在删除所有其他用户后，才能删除该租户。 如果用户是从本地同步的，则必须关闭同步，并且必须使用 Azure 门户或 Azure PowerShell cmdlet 从云租户中删除这些用户。 
* 该租户中不能有任何应用程序。 只有在删除所有应用程序后，才能删除该租户。
* 不能有任何多重身份验证提供程序链接到该租户。
* 与该租户相关联的任何 Microsoft Online Services（例如 Microsoft Azure、Office 365 或 Azure AD Premium）不能存在任何订阅。 例如，如果在 Azure 中创建了一个默认租户，并且 Azure 订阅仍然依赖于此租户进行身份验证，则不能删除此租户。 类似地，如果其他用户已将订阅与某个租户相关联，则无法删除该租户。 

## <a name="delete-an-azure-ad-tenant"></a>删除 Azure AD 租户

1. 使用租户的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。

2. 选择“Azure Active Directory”。

3. 切换到要删除的租户。
  
  ![“删除目录”按钮](./media/directory-delete-howto/delete-directory-command.png)

4. 选择“删除目录”。
  
  ![“删除目录”按钮](./media/directory-delete-howto/delete-directory-list.png)

5. 如果租户未通过一个或多个检查，系统会提供相关链接，其中介绍了有关如何通过的详细信息。 通过所有检查后，选择“删除”以完成此过程。

## <a name="i-have-an-expired-subscription-but-i-cant-delete-the-tenant"></a>我的订阅已过期，但无法删除租户

如果配置了 Azure Active Directory 租户，你可能已为组织激活了基于许可证的订阅，例如 Azure Active Directory Premium P2、Office 365 商业高级版或企业移动性 + 安全性 E5。 除非完全删除这些订阅，否则它们会阻止删除目录，以避免意外的数据丢失。 订阅必须处于“已取消预配”状态才能删除租户。 “已过期”或“已取消”订阅移动到“已禁用”状态后，最后一个阶段就是“已取消预配”状态。 

有关 Office 365 订阅试用到期后需执行的操作（不包括付费合作伙伴/CSP、企业协议或批量许可），请参阅下表。 如需深入了解 Office 365 数据保留期和订阅生命周期，请参阅 [Office 365 商业版订阅结束后我的数据和访问会出现什么情况？](https://support.office.com/article/what-happens-to-my-data-and-access-when-my-office-365-for-business-subscription-ends-4436582f-211a-45ec-b72e-33647f97d8a3)。 

订阅状态 | 数据 | 对数据的访问
----- | ----- | -----
活动（30 天试用）  | 所有用户均可访问数据    | <li>用户可正常访问 Office 365 文件或应用<li>管理员可正常访问 Office 365 管理中心和资源 
已到期（30 天）   | 所有用户均可访问数据    | <li>用户可正常访问 Office 365 文件或应用<li>管理员可正常访问 Office 365 管理中心和资源
已禁用（30 天） | 仅管理员可访问数据  | <li>用户无法访问 Office 365 文件或应用<li>管理员可以访问 Office 365 管理中心，但无法向用户分配许可证，也无法更新用户
已取消预配（禁用后 30 天） | 已删除数据（没有使用其他服务时自动删除） | <li>用户无法访问 Office 365 文件或应用<li>管理员可以访问 Office 365 管理中心购买和管理其他订阅 

可将订阅置于“已取消预配”状态，通过适用于企业的 Microsoft Store 管理中心在 3 天后删除。 Office 365 管理中心也即将推出此功能。

1. 使用租户的全局管理员帐户登录到[适用于企业的 Microsoft Store 管理中心](https://businessstore.microsoft.com/en-us/manage/)。 如果尝试删除具有初始默认域 contoso.onmicrosoft.com 的“Contoso”租户，请使用 UPN（例如 admin@contoso.onmicrosoft.com）登录。

2. 转到“管理”选项卡，选择“产品和服务”然后选择想要取消的订阅。 单击“取消”后，刷新该页面。
  
  ![删除用于删除订阅的链接](./media/directory-delete-howto/delete-command.png)
  
3. 选择“删除”，删除订阅并接受条款和条件。 所有数据将在三天内永久删除。 如果改变心意，可在这三天时间内重新激活订阅。
  
  ![条款和条件](./media/directory-delete-howto/delete-terms.png)

4. 现在订阅状态已改变，订阅已被标记以进行删除。 订阅在 72 小时后会进入“已取消预配”状态。

5. 已删除租户上的订阅并且时间已过 72 后，可再次登录 Azure AD 管理中心，这里应没有必需的操作，也不会有订阅阻止你删除租户。 你应可以成功删除 Azure AD 租户。
  
  ![在删除屏幕通过订阅检查](./media/directory-delete-howto/delete-checks-passed.png)

## <a name="next-steps"></a>后续步骤
[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory/)
