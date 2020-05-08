---
title: 将静态组成员身份更改为动态 Azure AD |Microsoft Docs
description: 如何创建成员资格规则以自动填充组和规则引用。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aff304b0866092badc515df7201fb4873acc298e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582928"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>在 Azure Active Directory 中将静态组成员资格更改为动态

可以在 Azure Active Directory (Azure AD) 中将组的成员资格从静态更改为动态（反之亦然）。 Azure AD 在系统中保留相同的组名和 ID，因此对该组的所有现有引用仍然有效。 如果创建新的组，将需要更新这些引用。 动态组成员资格消除了添加和删除用户的管理开销。 本文介绍如何使用 Azure AD 管理中心或 PowerShell cmdlet 将现有组从静态成员资格转换为动态成员资格。

> [!WARNING]
> 将现有静态组更改为动态组时，会从组中删除所有现有成员，然后会处理成员资格规则以添加新成员。 如果使用组来控制对应用或资源的访问，则请注意，在完全处理成员资格规则前，原始成员可能无法进行访问。
>
> 建议事先测试新的成员身份规则，确保组中的新成员身份符合预期。

## <a name="change-the-membership-type-for-a-group"></a>更改组的成员资格类型

1. 使用 Azure AD 组织中的全局管理员或用户管理员帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择**组**。
3. 在“所有组”**** 列表中，打开要更改的组。
4. 选择“属性”  。
5. 在组的“属性”页中，选择“已分配(静态)”、“动态用户”或“动态设备”作为“成员身份类型”，具体取决于所需的成员身份类型。******** 对于动态成员资格，可以使用规则生成器来选择适用于简单规则的选项，也可以自行编写一项成员资格规则。 

以下步骤以示例的方式说明了如何将某个组的用户从静态成员身份更改为动态成员身份。

1. 在所选组的“属性”页中，选择“动态用户”作为“成员身份类型”，然后在说明对组成员身份所做更改的对话框中选择“是”，以便继续操作。************ 
  
   ![选择“动态用户”作为“成员身份类型”](./media/groups-change-type/select-group-to-convert.png)
  
2. 选择“添加动态查询”，然后提供规则。****
  
   ![输入动态组的规则](./media/groups-change-type/enter-rule.png)
  
3. 创建规则之后，选择页面底部的“添加查询”****。
4. 在组的“属性”页上选择“保存”，以便保存所做的更改。******** 组列表中组的“成员身份类型”会立即进行更新。****

> [!TIP]
> 如果所输入的成员资格规则不正确，组转换操作可能会失败。 通知显示在门户的右上角；它包含系统无法接受规则的原因说明。 请仔细阅读，了解如何调整规则才能使其生效。 有关规则语法的示例以及成员资格规则支持的属性、运算符和值的完整列表，请参阅 [Azure Active Directory 中的组的动态成员资格规则](groups-dynamic-membership.md)。

## <a name="change-membership-type-for-a-group-powershell"></a>更改组的成员资格类型 (PowerShell)

> [!NOTE]
> 若要更改动态组属性，需要使用[Azure AD PowerShell 版本 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)的**预览版本中的**cmdlet。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureADPreview)安装预览版。

下面是在现有组中切换成员身份管理的函数示例。 在此示例中，必须小心正确地操作 GroupTypes 属性，并保留与动态成员身份无关的任何值。

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
将组设为静态：

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

将组设为动态：

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>后续步骤

以下文章提供了有关 Azure Active Directory 中的组的更多信息。

* [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)
* [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](groups-dynamic-membership.md)
