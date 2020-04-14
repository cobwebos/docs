---
title: 公司工作帐户和合作伙伴中心
description: 如何检查您的公司是否设置了 Microsoft 的工作帐户、创建新的工作帐户或设置多个工作帐户以与合作伙伴中心一起使用。
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 3c0ee2f8d5291f7904435dea32d913adeaaf25c5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262360"
---
# <a name="company-work-accounts-and-partner-center"></a>公司工作帐户和合作伙伴中心

合作伙伴中心使用公司工作帐户（也称为 Azure 活动目录 （AD） 租户）来管理多个用户的帐户访问、控制权限、主机组和应用程序以及维护配置文件数据。 通过将公司的工作电子邮件帐户域链接到合作伙伴中心帐户，公司员工可以登录到合作伙伴中心，使用自己的工作帐户用户名和密码管理市场优惠。

## <a name="check-whether-your-company-already-has-a-work-account"></a>检查您的公司是否已拥有工作帐户

如果公司已订阅了 Microsoft 云服务（如 Azure、Microsoft Intune 或 Office 365），则您已有一个工作电子邮件帐户域（也称为 Azure 活动目录租户），可与合作伙伴中心一起使用。

按照以下步骤检查：
1. 登录到 中的https://portal.azure.comAzure 管理门户。
2. 从左侧导航菜单中选择**Azure 活动目录**，然后选择**自定义域名**。
3. 如果你已有工作帐户，门户将列出你的域名。

如果您的公司还没有工作帐户，将在合作伙伴中心注册过程中为您创建一个工作帐户。

## <a name="set-up-multiple-work-accounts"></a>设置多个工作帐户

在决定使用现有工作帐户之前，请考虑工作帐户中需要访问合作伙伴中心的用户数。 如果工作帐户中有不需要访问合作伙伴中心的用户，则可能需要考虑创建多个工作帐户，以便仅在特定帐户上表示需要访问合作伙伴中心的用户。

## <a name="create-a-new-work-account"></a>创建新的工作帐户

要为公司创建新的工作帐户，请按照以下步骤操作。 您可能需要向对公司的 Microsoft Azure 帐户具有管理权限的人员请求帮助。

1. 登录到[微软 Azure 门户](https://portal.azure.com)。
2. 从左侧导航菜单中，选择**Azure 活动目录** -> **用户**。
3. 选择 **"新建用户**"并通过输入名称和电子邮件地址创建新的 Azure 工作帐户。 确保**目录角色**设置为 **"用户**"，并在底部选择 **"显示密码**"复选框以查看并记下自动生成的密码。
4. 选择 **"创建**"以保存新用户。

用户帐户的电子邮件地址必须是目录中已验证的域名。 通过在左侧导航菜单中选择**Azure 活动目录** -> **自定义域名**，可以列出目录中的所有已验证域。

要了解有关在 Azure 活动目录中添加自定义域的更多内容，请参阅[在 Azure AD 中添加或关联域](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)。

## <a name="troubleshoot-work-email-sign-in"></a>排除工作电子邮件登录的故障

如果您在登录到工作帐户（也称为 Azure AD 租户）时遇到问题，请找到下图中最适合您的情况的方案，然后按照建议的步骤操作。

![用于排除工作帐户登录的故障排除图](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>后续步骤

- [在合作伙伴中心管理您的商业市场帐户](./manage-account.md) 
