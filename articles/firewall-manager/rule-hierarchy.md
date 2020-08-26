---
title: 使用 Azure 防火墙策略来定义规则层次结构
description: 了解如何使用 Azure 防火墙策略来定义规则层次结构并强制实施符合性。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: c290904c9f4bc7dba70dad9351dc45b676e0c236
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893669"
---
# <a name="use-azure-firewall-policy-to-define-a-rule-hierarchy"></a>使用 Azure 防火墙策略来定义规则层次结构

安全管理员需要管理防火墙并确保本地部署和云部署之间的符合性。 关键组件是使应用程序团队能够灵活地实现 CI/CD 管道以自动方式创建防火墙规则。

Azure 防火墙策略允许你定义规则层次结构并强制实施符合性：

- 提供了一个层次结构，用于在子应用程序团队策略之上覆盖中心基准策略。 基本策略具有较高的优先级，并在子策略之前运行。
- 使用自定义的基于角色的访问控制 (RBAC) 定义，以防止无意中删除策略，并提供对订阅或资源组中的规则收集组的选择性访问。 

## <a name="solution-overview"></a>解决方案概述

此示例的高级步骤如下：

1. 在安全团队资源组中创建基本防火墙策略。 
3. 在基本策略中定义特定于安全性的规则。 这会添加一组通用规则，以允许/拒绝流量。
4. 创建继承基本策略的应用程序团队策略。 
5. 在策略中定义特定于应用程序团队的规则。 还可以从预先存在的防火墙迁移规则。
6. 创建 Azure Active Directory 自定义角色，以提供对规则收集组的精细访问权限，并在防火墙策略范围内添加角色。 在下面的示例中，销售团队成员可以编辑 "销售团队防火墙" 策略的规则收集组。 这同样适用于数据库和工程团队。
7. 将策略关联到相应的防火墙。 Azure 防火墙只能有一个分配的策略。 这需要每个应用程序团队拥有自己的防火墙。



:::image type="content" source="media/rule-hierarchy/contoso-teams.png" alt-text="团队和要求" border="false":::

### <a name="create-the-firewall-policies"></a>创建防火墙策略

- 基本防火墙策略。

为每个应用程序团队创建策略：

- 销售防火墙策略。 销售防火墙策略会继承基本防火墙策略。
- 数据库防火墙策略。 数据库防火墙策略继承基本防火墙策略。
- 工程防火墙策略。 工程防火墙策略还继承基本防火墙策略。

:::image type="content" source="media/rule-hierarchy/policy-hierarchy.png" alt-text="策略层次结构" border="false":::

### <a name="create-custom-roles-to-access-the-rule-collection-groups"></a>创建自定义角色以访问规则集合组 

为每个应用程序团队定义了自定义角色。 角色定义操作和作用域。 允许应用程序团队编辑其各自应用程序的规则收集组。

使用以下高级过程来定义自定义角色：

1. 获取订阅：

   `Select-AzSubscription -SubscriptionId xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`
2. 运行下面的命令：

   `Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize`
3. 使用 AzRoleDefinition 命令以 JSON 格式输出读取者角色。 

   `Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json`
4. 在编辑器中打开文件上的 ReaderSupportRole.js。

   下面显示了 JSON 输出。 有关不同属性的信息，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。

```json
   { 
     "Name": "Reader", 
     "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7", 
     "IsCustom": false, 
     "Description": "Lets you view everything, but not make any changes.", 
     "Actions": [ 
      "*/read" 
     ], 
     "NotActions": [], 
     "DataActions": [], 
     "NotDataActions": [], 
     "AssignableScopes": [ 
       "/" 
     ] 
   } 
```
5. 编辑 JSON 文件，以将 

   `*/read", "Microsoft.Network/*/read", "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write` 

   操作属性的 **操作**   。 请确保在读取操作后包括一个逗号。 此操作允许用户创建和更新规则收集组。
6. 在 **AssignableScopes**中，添加以下格式的订阅 ID： 

   `/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx`

   必须添加显式的订阅 ID，否则将不允许将角色导入到订阅中。
7. 删除 **Id**   属性行，并将 **和 iscustom 设置**   属性更改为 true。
8. 将 " **名称**"   和 " **说明**"   属性更改为*AZFM 规则集合组作者*，*此角色中的用户可以编辑防火墙策略规则集合组*

JSON 文件应类似于以下示例：

```
{ 

    "Name":  "AZFM Rule Collection Group Author", 
    "IsCustom":  true, 
    "Description":  "Users in this role can edit Firewall Policy rule collection groups", 
    "Actions":  [ 
                    "*/read", 
                    "Microsoft.Network/*/read", 
                     "Microsoft.Network/firewallPolicies/ruleCollectionGroups/write" 
                ], 
    "NotActions":  [ 
                   ], 
    "DataActions":  [ 
                    ], 
    "NotDataActions":  [ 
                       ], 
    "AssignableScopes":  [ 
                             "/subscriptions/xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxx"] 
} 
```
9. 若要创建新的自定义角色，请使用 AzRoleDefinition 命令并指定 JSON 角色定义文件。 

   `New-AzRoleDefinition -InputFile "C:\CustomRoles\RuleCollectionGroupRole.json`

### <a name="list-custom-roles"></a>列出自定义角色

若要列出所有自定义角色，可以使用 AzRoleDefinition 命令：

   `Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom`

你还可以在 Azure 门户中查看自定义角色。 请访问订阅，选择 " **访问控制 (IAM) **，" **角色**"。

:::image type="content" source="media/rule-hierarchy/sales-app-policy.png" alt-text="SalesAppPolicy":::

:::image type="content" source="media/rule-hierarchy/sales-app-policy-read.png" alt-text="SalesAppPolicy 读取权限":::

有关详细信息，请参阅 [教程：使用 Azure PowerShell 创建 Azure 自定义角色](../role-based-access-control/tutorial-custom-role-powershell.md)。

### <a name="add-users-to-the-custom-role"></a>将用户添加到自定义角色

在门户中，可以将用户添加到 AZFM 规则集合组作者角色，并提供防火墙策略的访问权限。

1. 从门户中，选择 "应用程序" 团队防火墙策略 (例如 SalesAppPolicy) 。
2. 选择 " **访问控制**"。
3. 选择“添加角色分配”。
4. 添加用户/用户组 (例如，销售团队) 角色。

对其他防火墙策略重复此过程。

### <a name="summary"></a>总结

带有自定义 RBAC 的防火墙策略现在提供对防火墙策略规则集合组的选择性访问。

用户无权执行以下操作：
- 删除 Azure 防火墙或防火墙策略。
- 更新防火墙策略层次结构或 DNS 设置或威胁情报。
- 更新不是 AZFM 规则集合组作者组的成员的防火墙策略。

安全管理员可以使用基本策略强制实施 guardrails，并阻止特定类型的流量 (例如，其企业需要的 ICMP) 。 

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 防火墙策略](policy-overview.md)的详细信息。

