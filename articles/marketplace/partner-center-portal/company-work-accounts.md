---
title: 公司工作帐户和合作伙伴中心
description: 如何检查你的公司是否具有一个工作帐户与 Microsoft 设置、 创建新的工作帐户，或将多个工作帐户设置为使用与合作伙伴中心。
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: how-to
ms.date: 05/30/2019
ms.openlocfilehash: df8ab370e8874e07f8985ecfb3a772848a2e2b21
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806276"
---
# <a name="company-work-accounts-and-partner-center"></a>公司工作帐户和合作伙伴中心

合作伙伴中心使用公司工作帐户，也称为 Azure Active Directory (AD) 租户，来管理帐户访问多个用户、 控制权限、 主机组和应用程序，以及维护配置文件数据。 通过将你公司的工作电子邮件帐户域链接到合作伙伴中心帐户，公司的员工可以登录到合作伙伴中心以管理 marketplace 产品/服务使用其自己的工作帐户用户名和密码。

## <a name="check-whether-your-company-already-has-a-work-account"></a>检查你的公司是否已有工作帐户

如果贵公司已订阅 Azure、 Microsoft Intune 或 Office 365 等 Microsoft 云服务，然后你已有的工作电子邮件帐户域 （也称为 Azure Active Directory 租户） 可用于合作伙伴中心。

请按照以下步骤检查操作：
1. 登录到 Azure 管理门户 https://portal.azure.com。
2. 选择**Azure Active Directory**从左侧导航菜单，然后选择**自定义域名**。
3. 如果已有工作帐户，将列出你的域名。

如果你的公司还没有工作帐户，将会创建一个为你在合作伙伴中心注册过程。

## <a name="set-up-multiple-work-accounts"></a>设置多个工作帐户

在决定使用现有工作帐户之前, 请考虑将需要多少用户工作帐户来访问合作伙伴中心。 如果不需要访问合作伙伴中心的工作帐户中的用户，可能想要考虑创建多个工作帐户，以便只有这些用户将需要访问合作伙伴中心表示上一个特定帐户。

## <a name="create-a-new-work-account"></a>创建新的工作帐户

若要为你的公司创建新的工作帐户，请执行以下步骤。 您可能需要的任何人在你公司的 Microsoft Azure 帐户上具有管理权限请求协助。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。
2. 从左侧的导航菜单中选择**Azure Active Directory** -> **用户**。
3. 选择**新用户**并通过输入姓名和电子邮件地址创建一个新的 Azure 工作帐户。 絋粄**目录角色**设置为**用户**，然后选择**显示密码**底部查看并记下自动生成密码的复选框。
4. 选择**创建**以保存新的用户。

用户帐户的电子邮件地址必须是目录中已验证的域名。 可以通过选择你的目录中列出所有已验证的域**Azure Active Directory** -> **自定义域名**左侧导航菜单中。

若要了解有关 Azure Active Directory 中添加自定义域的详细信息，请参阅[添加或关联 Azure AD 中的域](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)。

## <a name="troubleshoot-work-email-sign-in"></a>工作电子邮件登录故障排除

如果你无法登录到你的工作帐户 （也称为 Azure AD 租户），查找最下面关系图上的方案匹配你的情况，并按照建议的步骤。

![关系图进行故障排除工作帐户登录](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>后续步骤

- [管理在合作伙伴中心商业 Marketplace 帐户](./manage-account.md) 
