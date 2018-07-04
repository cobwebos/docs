---
title: 在 Azure 安全中心内实现租户级公开范围 | Microsoft Docs
description: 了解如何在 Azure 安全中心内实现租户级公开范围。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 2c95b06ce34b850d1bfaf60e47d6e5fede148a38
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025109"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>在 Azure 安全中心内实现租户级公开范围
本文介绍了如何执行一些操作来最大限度地利用 Azure 安全中心提供的优势，从而帮助读者入门。 执行这些操作，可以在所有与 Azure Active Directory 租户相关联的 Azure 订阅中实现公开范围，并能以聚合方式跨多个订阅应用安全策略，从而大规模、有效地管理组织的安全状态。

## <a name="management-groups"></a>管理组
借助 Azure 管理组，可以对各组订阅高效管理访问、策略和报告，并能对根管理组执行操作，从而有效管理整个 Azure 资产。 每个 Azure AD 租户都指定有一个顶级管理组，称为“根管理组”。 此根管理组内置在层次结构中，包含其所有下级管理组和订阅。 借助此组，可以在目录一级应用全局策略和 RBAC 分配。 

执行以下任何操作时，都会自动创建根管理组： 
1. 通过在 [Azure 门户](https://portal.azure.com)中转到“管理组”，选择使用 Azure 管理组。
2. 通过 API 调用来创建管理组。
3. 通过 PowerShell 创建管理组。

有关管理组的详细概述，请参阅[使用 Azure 管理组整理资源](../azure-resource-manager/management-groups-overview.md)一文。

## <a name="create-a-management-group-in-the-azure-portal"></a>在 Azure 门户中创建管理组
可以将订阅整理到管理组中，并向管理组应用治理策略。 管理组中的所有订阅都会自动继承应用于管理组的策略。 虽然不一定要在安全中心内创建管理组，但强烈建议至少创建一个管理组，以便创建根管理组。 创建管理组后，Azure AD 租户下的所有订阅都会与它关联。 有关 PowerShell 说明以及更多信息，请参阅[创建管理组以管理资源和组织](../azure-resource-manager/management-groups-create.md)。

 
1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 选择“所有服务” > “管理组”。
3. 在主页上，选择“新建管理组”。 

    ![主要组](./media/security-center-management-groups/main.png) 
4.  填写管理组 ID 字段。 
    - “管理组 ID”是用来在此管理组上提交命令的目录唯一标识符。 此标识符一旦创建便无法再编辑，因为它用来在整个 Azure 系统中标识这个组。 
    - 显示名称字段是在 Azure 门户中显示的名称。 创建管理组时，单独的显示名称是一个可选字段，并且可以随时更改。  

      ![创建](./media/security-center-management-groups/create_context_menu.png)  
5.  选择“保存”

### <a name="view-management-groups-in-the-azure-portal"></a>在 Azure 门户中查看管理组
1. 登录 [Azure 门户](http://portal.azure.com)。
2. 若要查看管理组，请选择 Azure 主菜单下的“所有服务”。
3. 选择“通用”下的“管理组”。

    ![创建管理组](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>授予租户级公开范围和策略分配权限

若要了解在 Azure AD 租户中注册的所有订阅的安全状态，必须对根管理组分配拥有足够读取权限的 RBAC 角色。

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>提升 Azure Active Directory 全局管理员的访问权限
Azure Active Directory 租户管理员无权直接访问 Azure 订阅。 不过，作为目录管理员，他们有权将自身提升为拥有访问权限的角色。 Azure AD 租户管理员必须将自身提升为根管理组级用户访问管理员，才能分配 RBAC 角色。 有关 PowerShell 说明以及更多信息，请参阅[提升 Azure Active Directory 全局管理员的访问权限](../role-based-access-control/elevate-access-global-admin.md)。 


1. 登录 [Azure 门户](https://portal.azure.com)或 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

2. 在导航列表中，单击“Azure Active Directory”，然后单击“属性”。

   ![Azure AD 属性 - 屏幕截图](./media/security-center-management-groups/aad-properties.png)

3. 在“全局管理员可管理 Azure 订阅和管理组”下，将开关设置为“是”。

   ![全局管理员可管理 Azure 订阅和管理组 - 屏幕截图](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - 如果将开关设置为“是”，全局管理员帐户（当前登录用户）会在根范围 (`/`) 内添加到 Azure RBAC 的用户访问管理员角色，此操作可授予你查看和报告与 Azure AD 租户关联的所有 Azure 订阅的访问权限。

   - 如果将开关设置为“否”，全局管理员帐户（当前登录用户）会从 Azure RBAC 的用户访问管理员角色中删除。 你无法看到与 Azure AD 租户关联的所有 Azure 订阅，并且只能查看和管理已被授予访问权限的 Azure 订阅。

4. 单击“保存”，保存设置。

    - 此设置不是全局属性，仅适用于当前登录用户。

5. 执行需要在提升的访问权限下完成的任务。 完成后，将开关设置回“否”。

### <a name="assign-rbac-roles-to-users"></a>向用户分配 RBAC 角色
拥有提升的访问权限后，租户管理员便可以在根管理组一级向相关用户分配 RBAC 角色。 建议分配的角色是[读者](../role-based-access-control/built-in-roles.md#reader)。 必须有此角色，才能授予租户级公开范围。 分配的角色会自动传播到根管理组下的所有管理组和订阅。 若要详细了解 RBAC 角色，请参阅[可用角色](../active-directory/active-directory-assign-admin-roles-azure-portal.md#available-roles)。

1. 安装 [Azure PowerShell](/powershell/azure/install-azurerm-ps)。
2. 运行以下命令： 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. 当出现提示时，请使用全局管理员凭据登录。 

    ![登录提示屏幕截图](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. 运行下面的命令，授予读者角色权限：

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. 若要删除角色，请运行下面的命令： 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>撤消提升的访问权限 
向用户分配 RBAC 角色后，租户管理员应将自己从用户访问管理员角色中删除。

1. 登录 [Azure 门户](https://portal.azure.com)或 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

2. 在导航列表中，单击“Azure Active Directory”，然后单击“属性”。

3. 在“全局管理员可管理 Azure 订阅和管理组”下，将开关设置为“否”。

4. 单击“保存”，保存设置。

### <a name="open-or-refresh-security-center"></a>打开或刷新安全中心
分配 RBAC 角色后，立即打开或刷新 Azure 安全中心，验证能否查看 Azure AD 租户下的所有订阅。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 主菜单下，依次选择“所有服务”和“安全中心”。
3. 在“概述”中，没有订阅覆盖率图表。 
    ![订阅覆盖率图表屏幕截图](./media/security-center-management-groups/security-center-subscription-coverage.png)
4. 单击“覆盖率”，查看所覆盖的订阅列表。 
    ![订阅覆盖率列表屏幕截图](./media/security-center-management-groups/security-center-coverage.png)

## <a name="adding-subscriptions-to-a-management-groups"></a>向管理组添加订阅
可以向创建的管理组添加订阅。 这些步骤不是实现租户级公开范围以及全局策略和访问管理的必需步骤。

1. 在“管理组”下，选择要向其中添加订阅的管理组。

    ![选择要向其中添加订阅的管理组](./media/security-center-management-groups/management-group-subscriptions.png)

2. 添加“添加现有”。

    ![添加现有](./media/security-center-management-groups/add-existing.png)

3. 在“添加现有资源”下输入订阅，并单击“保存”。

4. 重复执行第 1 步到第 3 步，直到已添加范围内的所有订阅。

 > [!NOTE]
 > 管理组可以包含订阅和子管理组。 向父管理组分配拥有 RBAC 角色的用户时，子管理组的订阅继承访问权限。 子管理组还继承在父管理组设置的策略。 

## <a name="next-steps"></a>后续步骤
本文介绍了如何在 Azure 安全中心内实现租户级公开范围。 若要详细了解安全中心，请参阅以下文章：

> [!div class="nextstepaction"]
> [在 Azure 安全中心进行安全运行状况监视](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [在 Azure 安全中心内管理和响应安全警报](security-center-managing-and-responding-alerts.md)

