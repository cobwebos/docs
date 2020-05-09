---
title: 组过期策略快速入门  - Azure AD | Microsoft Docs
description: Office 365 组的过期 - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 616910eda33b3ddc49fa6233ccb3989c5e4214e2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582843"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>快速入门：将 Azure Active Directory 中的 Office 365 组设置为过期

本快速入门介绍如何设置 Office 365 组的过期策略。 当用户可以设置自己的组时，未使用的组可能会成倍增加。 若要管理未使用的组，一种方式是将这些组设置为过期，以便减少手动删除组的维护工作量。

过期策略很简单：

- 具有用户活动的组在快要过期时自动续订
- 系统通知组所有者续订要过期的组
- 未续订的组会被删除
- 已删除的 Office 365 组可以由组所有者或 Azure AD 管理员在 30 天内还原

> [!NOTE]
> 现在，组使用 Azure AD 智能，根据是否在最近使用而自动续订。 此续订决策基于 Office 365 服务（如 Outlook、SharePoint、Teams、Yammer 等）中组的用户活动。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisite"></a>先决条件

 设置组过期所需的最低特权角色是组织的用户管理员。

## <a name="turn-on-user-creation-for-groups"></a>为组启用创建用户的功能

1. 使用用户管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 依次选择“组”、“常规”。  
  
   ![自助组设置页](./media/groups-quickstart-expiration/self-service-settings.png)

3. 将“用户可创建 Office 365 组”设置为“是”。  

4. 设置完成后，选择“保存”以保存组设置  。

## <a name="set-group-expiration"></a>设置组过期

1. 登录到 [Azure 门户](https://portal.azure.com)，选择“Azure Active Directory” > “组” > “过期”打开过期设置    。
  
   ![组过期设置页](./media/groups-quickstart-expiration/expiration-settings.png)

2. 设置过期时间间隔。 选择一个预设值，或者输入一个超过 31 天的自定义值。 

3. 提供当组没有所有者时用于发送过期通知的电子邮件地址。

4. 在本快速入门中，请将“为这些 Office 365 组启用过期”设置为“所有”。  

5. 设置完成后，选择“保存”来保存过期设置  。

就这么简单！ 在本快速入门中，你已成功设置所选 Office 365 组的过期策略。

## <a name="clean-up-resources"></a>清理资源

### <a name="to-remove-the-expiration-policy"></a>删除过期策略

1. 确保已使用 Azure AD 组织的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”   >   “组” >   “过期”。
3. 将“为这些 Office 365 组启用过期”设置为“无”。  

### <a name="to-turn-off-user-creation-for-groups"></a>为组禁用创建用户的功能

1. 选择“Azure Active Directory”   >   “组” >   “常规”。 
2. 将“用户可以在 Azure 门户中创建 Office 365 组”设置为“否”。  

## <a name="next-steps"></a>后续步骤

有关到期的详细信息（包括 PowerShell 说明和技术约束），请参阅以下文章：

> [!div class="nextstepaction"]
> [过期策略 PowerShell](groups-lifecycle.md)
