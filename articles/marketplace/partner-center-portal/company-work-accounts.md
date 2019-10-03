---
title: 公司工作帐户和合作伙伴中心
description: 如何检查你的公司是否有设置了 Microsoft 的工作帐户、创建新的工作帐户, 或设置了多个工作帐户以与合作伙伴中心一起使用。
author: ChJenk
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 744b4e4975c5d4cec5c705e639e9cc8d252a7dd6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194278"
---
# <a name="company-work-accounts-and-partner-center"></a>公司工作帐户和合作伙伴中心

合作伙伴中心使用公司工作帐户 (也称为 Azure Active Directory (AD) 租户) 管理对多个用户、控制权限、主机组和应用程序的帐户访问, 并维护配置文件数据。 通过将公司的工作电子邮件帐户域链接到合作伙伴中心帐户, 公司的员工可以使用自己的工作帐户用户名和密码登录到合作伙伴中心来管理 marketplace 产品/服务。

## <a name="check-whether-your-company-already-has-a-work-account"></a>检查你的公司是否已拥有工作帐户

如果你的公司已订阅 Azure、Microsoft Intune 或 Office 365 等 Microsoft 云服务, 则你已具有可与合作伙伴中心一起使用的工作电子邮件帐户域 (也称为 Azure Active Directory 租户)。

请按照以下步骤检查:
1. 登录到 Azure 管理门户, 网址 https://portal.azure.com 为。
2. 从左侧导航菜单中选择 " **Azure Active Directory** ", 然后选择 "**自定义域名**"。
3. 如果你已有工作帐户, 则将列出你的域名。

如果你的公司尚未拥有工作帐户, 则会在合作伙伴中心注册过程中为你创建一个。

## <a name="set-up-multiple-work-accounts"></a>设置多个工作帐户

在决定使用现有工作帐户之前, 请考虑工作帐户中有多少用户需要访问合作伙伴中心。 如果你的工作帐户中有不需要访问合作伙伴中心的用户, 则可能需要考虑创建多个工作帐户, 以便仅在特定帐户上显示将需要访问合作伙伴中心的那些用户。

## <a name="create-a-new-work-account"></a>创建新的工作帐户

若要为你的公司创建新的工作帐户, 请按照以下步骤操作。 你可能需要请求对你的公司的 Microsoft Azure 帐户具有管理权限的人员的帮助。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。
2. 从左侧导航菜单中, 选择 " **Azure Active Directory** -> **用户**"。
3. 通过输入名称和电子邮件地址, 选择 "**新建用户**" 并创建新的 Azure 工作帐户。 确保将 "**目录角色**" 设置为 "**用户**", 并选择底部的 "**显示密码**" 复选框, 以查看并记下自动生成的密码。
4. 选择 "**创建**" 以保存新用户。

用户帐户的电子邮件地址必须是目录中已验证的域名。 可以通过在左侧导航菜单中选择 " **Azure Active Directory** -> **自定义域名**" 来列出目录中所有已验证的域。

若要详细了解如何在 Azure Active Directory 中添加自定义域, 请参阅[在 Azure AD 中添加或关联域](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)。

## <a name="troubleshoot-work-email-sign-in"></a>排查工作电子邮件登录问题

如果在登录到工作帐户 (也称为 Azure AD 租户) 时遇到问题, 请在下面的关系图中查找最符合你的情况的方案, 并执行建议的步骤。

![工作帐户登录疑难解答示意图](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>后续步骤

- [在合作伙伴中心管理你的商业应用商店帐户](./manage-account.md) 
