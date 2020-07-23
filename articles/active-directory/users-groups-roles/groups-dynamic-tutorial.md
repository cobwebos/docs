---
title: 向动态组添加用户 - 教程 - Azure AD | Microsoft Docs
description: 本教程介绍了如何将组与用户成员身份规则配合使用，以便自动添加或删除用户
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee5fa52f59ea2ef3332fe66c81c24ff44c64e81
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582874"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>教程：自动添加或删除组成员

在 Azure Active Directory (Azure AD) 中，可以自动地在安全组或 Office 365 组中添加或删除用户，因此不必总是手动执行该操作。 当用户或设备的任何属性更改时，Azure AD 会评估 Azure AD 组织中的所有动态组规则，以了解该更改是否会添加或删除成员。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 在合作伙伴公司中创建自动填充的来宾用户组
> * 向特定于合作伙伴的功能所对应的组分配许可证，方便来宾用户访问
> * 额外内容：通过删除来宾用户来实现特定的功能（例如允许成员用户访问内部站点），从而确保“所有用户”组的安全 

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

此功能要求组织全局管理员使用一个 Azure AD Premium 许可证。 如果没有该许可证，请在 Azure AD 中选择“许可证”   > “产品”   >   “试用/购买”。

不需向用户分配许可证即可让用户成为动态组中的成员。 只需在组织中提供最低数目的 Azure AD Premium P1 许可证即可涵盖所有此类用户。 

## <a name="create-a-group-of-guest-users"></a>创建包含来宾用户的组

首先，需为来宾用户创建一个组，这些用户全都来自某个合作伙伴公司。 他们需要特别许可，因此通常情况下，为此创建一个组会更为有效。

1. 使用组织的全局管理员帐户登录到 Azure 门户 (https://portal.azure.com) 。
2. 选择“Azure Active Directory”   > “组”   >   “新建组”。
   ![选择命令以启动新组](./media/groups-dynamic-tutorial/new-group.png)
3. 在“组”  边栏选项卡上执行以下操作：
  
   * 选择“安全性”作为组类型  。
   * 输入 `Guest users Contoso` 作为组的名称和说明。
   * 将“成员身份类型”更改为“动态用户”   。
   
4. 选择“所有者”  ，然后在“添加所有者”  边栏选项卡中搜索任何所需的所有者。 单击所需的所有者以添加到选择项中。
5. 单击“选择”  以关闭“添加所有者”  边栏选项卡。  
6. 在“动态用户成员”  框中选择“编辑动态查询”  。
7. 在“动态成员身份规则”  边栏选项卡上：

   * 在“属性”  字段中，单击现有值并选择 **userType**。 
   * 验证“运算符”  字段是否已选择“等于”  。  
   * 选择“值”  字段并输入 **Guest**。 
   * 单击“添加表达式”  超链接以添加另一行。
   * 在“并且/或”  字段中，选择“并且”  。
   * 在“属性”  字段中，选择 **companyName**。
   * 验证“运算符”  字段是否已选择“等于”  。
   * 在“值”  字段中，输入 **Contoso**。
   * 单击“保存”  以关闭“动态成员身份规则”  边栏选项卡。
   
8. 在“组”边栏选项卡中，选择“创建”以创建该组。  

## <a name="assign-licenses"></a>分配许可证

有了新组以后，即可应用这些合作伙伴用户需要的许可证。

1. 在 Azure AD 中选择“许可证”，选择一个或多个许可证，然后选择“分配”。  
2. 选择“用户和组”  ，接着选择“来宾用户 Contoso”组  ，然后保存所做的更改。
3. 可以通过“分配选项”来打开或关闭服务计划，包括已选择的许可证。  进行更改时，请确保单击“确定”  以保存所做的更改。
4. 若要完成分配，请在“分配许可证”  窗格上，单击该窗格底部的“分配”  。

## <a name="remove-guests-from-all-users-group"></a>从“所有用户”组中删除来宾

也许你最终的管理计划是将所有来宾用户按公司分配到他们自己的组。 现在，还可以更改“所有用户”  组，仅为组织中的成员用户保留该组。 然后，可以用它来分配特定于你所在组织的应用和许可证。

   ![将“所有用户”组更改为仅限成员的组](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>清理资源

**删除来宾用户组**

1. 使用组织的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”   >   “组”。 依次选择“来宾用户 Contoso”组、  省略号 (...)、“删除”。  删除组时，会删除任何已分配的许可证。

**还原“所有用户”组**
1. 选择“Azure Active Directory”   >   “组”。 选择“所有用户”组的名称，以便打开该组。 
1. 选择“动态成员身份规则”，清除规则中的所有文本，然后选择“保存”。  

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：
> [!div class="checklist"]
> * 创建包含来宾用户的组
> * 将许可证分配给新组
> * 将“所有用户”组更改为仅限成员的组

请继续学习下一篇文章，详细了解基于组的许可基础知识。
> [!div class="nextstepaction"]
> [组许可基础知识](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



