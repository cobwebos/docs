---
title: 使用动态组成员身份添加或删除用户 - 教程 - Azure Active Directory
description: 本教程介绍了如何将组与用户成员身份规则配合使用，以便自动添加或删除用户
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3883ddcad1c41e131d52016e4fa94a3e668adcd1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209721"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>教程：自动添加或删除组成员

在 Azure Active Directory (Azure AD) 中，可以自动地在安全组或 Office 365 组中添加或删除用户，因此不必总是手动执行该操作。 当用户或设备的任何属性更改时，Azure AD 会评估租户中的所有动态组规则，看该更改是否会导致成员的添加或删除。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 在特定的合作伙伴公司中创建自动填充的来宾用户组
> * 向特定于合作伙伴的功能所对应的组分配许可证，方便来宾用户访问
> * 额外内容：通过删除来宾用户来实现特定的功能（例如允许成员用户访问内部站点），从而确保“所有用户”组的安全

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

此功能要求充当租户全局管理员的你使用一个 Azure AD Premium 许可证。 如果没有该许可证，请在 Azure AD 中选择“许可证” > “产品” > “试用/购买”。

不需向用户分配许可证即可让用户成为动态组中的成员。 只需在租户中提供最低数目的 Azure AD Premium P1 许可证来涵盖全部此类用户即可。 

## <a name="create-a-group-of-guest-users"></a>创建包含来宾用户的组

首先，需为来宾用户创建一个组，这些用户全都来自某个合作伙伴公司。 他们需要特别许可，因此通常情况下，为此创建一个组会更为有效。

1. 使用一个其身份为租户全局管理员的帐户登录到 Azure 门户 (https://portal.azure.com)。
2. 选择“Azure Active Directory” > “组” > “新建组”。
  ![选择“新建组”命令](./media/groups-dynamic-tutorial/new-group.png)
3. 在“组”边栏选项卡上执行以下操作：
  
  * 选择“安全性”作为组类型
  * 输入 `Guest users Contoso` 作为组的名称和说明
  * 将“成员身份类型”更改为“动态用户”
  * 选择“添加动态查询”
  
4. 选择“高级规则”，然后在“高级规则”框中输入 `(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`
5. 选择“添加查询”，以便关闭边栏选项卡。
6. 在“组”边栏选项卡中，选择“创建”以创建该组。

## <a name="assign-licenses"></a>分配许可证

有了新组以后，即可应用这些合作伙伴用户需要的许可证。

1. 在 Azure AD 中选择“许可证”，选择一个或多个许可证，然后选择“分配”。
2. 选择“用户和组”，接着选择“来宾用户 Contoso”组，然后保存所做的更改。
3. 可以通过“分配选项”来打开或关闭服务计划，包括已选择的许可证。 进行更改时，请确保单击“确定”以保存所做的更改。
4. 若要完成分配，请在“分配许可证”窗格上，单击该窗格底部的“分配”。

## <a name="remove-guests-from-all-users-group"></a>从“所有用户”组中删除来宾

也许你最终的管理计划是将所有来宾用户按公司分配到他们自己的组。 也可现在就更改“所有用户”组，使其成为一个保留组，仅供你租户中的成员用户使用。 然后，可以用它来分配特定于你所在组织的应用和许可证。

   ![将“所有用户”组更改为仅限成员的组](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>清理资源

**删除来宾用户组**

1. 使用一个其身份为租户全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “组”。 依次选择“来宾用户 Contoso”组、省略号 (...)、“删除”。 删除组时，会删除任何已分配的许可证。

**还原“所有用户”组**
1. 选择“Azure Active Directory” > “组”。 选择“所有用户”组的名称，以便打开该组。
1. 选择“动态成员身份规则”，清除规则中的所有文本，然后选择“保存”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：
> [!div class="checklist"]
> * 创建包含来宾用户的组
> * 将许可证分配给新组
> * 将“所有用户”组更改为仅限成员的组

请继续学习下一篇文章，详细了解基于组的许可基础知识。
> [!div class="nextstepaction"]
> [组许可基础知识](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



