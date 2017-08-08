---
title: "管理 Azure Analysis Services 中的数据库角色和用户| Microsoft Docs"
description: "了解如何在 Azure 中管理 Analysis Services 服务器上的数据库角色和用户。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 06/26/2016
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: c1a8321c3c03dc9022048e39b3c8614ae683b955
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---
# <a name="manage-database-roles-and-users"></a>管理数据库角色和用户

在模型数据库级别，所有用户都必须属于一个角色。 角色可针对模型数据库定义具有特定权限的用户。 添加到角色的任何用户或安全组都必须在与服务器相同的订阅的 Azure AD 租户中具有一个帐户。

定义角色的方式根据使用的工具有所差异，但效果却是相同的。

角色权限包括：
*  管理员 - 用户对数据库具有完全的权限。 具有管理员权限的数据库角色不同于服务器管理员。
*  进程 - 用户可以连接到数据库并对其执行处理操作，分析模型数据库数据。
*  读取 - 用户可以使用客户端应用程序连接到模型数据库数据并进行分析。

如果创建表格模型项目，请使用 SSDT 中的角色管理器创建角色并向这些角色添加用户或组。 如果向服务器部署，请使用 SSMS、[Analysis Services PowerShell cmdlet](https://msdn.microsoft.com/library/hh758425.aspx) 或 [表格模型脚本语言](https://msdn.microsoft.com/library/mt614797.aspx) \(TMSL) 添加或删除角色和用户成员。

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>在 SSDT 中添加或管理角色和用户  
  
1.  在 SSDT 的表格模型资源管理器中，右键单击“角色”。  
  
2.  在“角色管理器”中单击“新建”。  
  
3.  键入角色名称。  
  
     默认情况下，对于每个新的角色，默认角色名称以递增方式进行编号。 建议键入可明确标识成员类型的名称，例如，财务经理或人力资源专员。  
  
4.  选择以下权限之一：  
  
    |权限|说明|  
    |----------------|-----------------|  
    |**无**|成员无法修改模型架构，也无法查询数据。|  
    |**读取**|成员可以（基于行筛选器）查询数据，但无法修改模型架构。|  
    |读取和处理|成员可以（基于行级筛选器）查询数据并运行“处理”和“全部处理”操作，但无法修改模型架构。|  
    |**过程**|成员可以运行“处理”和“全部处理”操作。 无法修改模型架构，也无法查询数据。|  
    |管理员|成员可以修改模型架构并查询所有数据。|   
  
5.  如果正在创建的角色具有“读取”或“读取和处理”权限，可以使用 DAX 公式添加行筛选器。 单击“行筛选器”选项卡，选择表，再单击“DAX 筛选器”字段，然后键入一个 DAX 公式。
  
6.  单击“成员” > “添加外部成员”  
  
8.  在“添加外部成员”中，按电子邮件地址输入租户 Azure AD 中的用户或组。 单击“确定”并关闭角色管理器后，角色和角色成员将显示在表格模型资源管理器中。 
 
     ![表格模型资源管理器中的角色和用户](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. 部署到 Azure Analysis Services 服务器。


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>在 SSMS 中添加或管理角色和用户
若要向部署模型数据库添加角色和用户，必须以服务器管理员身份连接到服务器，或已经是具有管理员权限的数据库角色的成员。

1. 在对象资源管理器中，右击“角色” > “新建角色”。

2. 在“创建角色”中，输入角色名称和描述。

3. 选择权限。
   |权限|说明|  
   |----------------|-----------------|  
   |完全控制（管理员）|成员可以修改模型架构，处理并查询所有数据。| 
   |处理数据库|成员可以运行“处理”和“全部处理”操作。 无法修改模型架构，也无法查询数据。|  
   |**读取**|成员可以（基于行筛选器）查询数据，但无法修改模型架构。|  
  
4. 单击“成员资格”，然后按电子邮件地址在租户 Azure AD 中输入用户或组。

     ![添加用户](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. 如果正在创建的角色具有“读取”权限，可以使用 DAX 公式添加行筛选器。 单击“行筛选器”，选择表，然后在“DAX 筛选器”字段中输入 DAX 公式。 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>使用 TMSL 脚本添加角色和用户
可在 SSMS 中的 XMLA 窗口中运行 TMSL 脚本或使用 PowerShell。 使用 [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) 命令和 [Roles](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) 对象。

**示例 TMSL 脚本**

在此示例中，B2B 外部用户和组添加到了具有 SalesBI 数据库读取权限的 Analyst 角色中。 外部用户和组必须均位于相同的租户 Azure AD 中。

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>使用 PowerShell 添加角色和用户
[SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) 模块提供任务特定的数据库管理 cmdlet，以及接受表格模型脚本语言 (TMSL) 查询或脚本的通用 Invoke-ASCmd cmdlet。 以下 cmdlet 用于管理数据库角色和用户。
  
|Cmdlet|说明|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|向数据库角色添加成员。| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|从数据库角色删除成员。|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|执行 TMSL 脚本。|

## <a name="row-filters"></a>行筛选器  
行筛选器定义特定角色的成员可以查询表中的哪些行。 可使用 DAX 公式为模型中的每个表定义行筛选器。  
  
可仅为具有“读取”和“读取和处理”权限的角色定义行筛选器。 默认情况下，如果没有为特定表定义行筛选器，除非交叉筛选其他表中的适用项，否则成员可以查询表中的所有行。
  
 行筛选器需要 DAX 公式，该公式的求值结果必须为 TRUE/FALSE，以定义该特定角色的成员可以查询的行。 无法查询未包含在 DAX 公式中的行。 例如，Customers 表的以下行筛选器表达式，=Customers [Country] = “USA”，Sales 角色的成员只能查看美国境内的客户。  
  
行筛选器适用于指定的行和相关行。 如果表具有多个关系，筛选器将对处于活动状态的关系应用安全性。 行筛选器与为相关表定义的其他行筛选器相交，示例如下：  
  
|表|DAX 表达式|  
|-----------|--------------------|  
|区域|=Region[Country]=”USA”|  
|ProductCategory|=ProductCategory[Name]=”Bicycles”|  
|事务|=Transactions[Year]=2016|  
  
 净效果是指成员可以查询若干行数据，其中客户位于美国，产品类别为自行车，年份是 2016 年。 用户无法查询美国之外的事务，不是自行车事务或非 2016 年的事务，除非它们属于授予这些权限的另一角色。
  
 可以使用筛选器 =FALSE() 拒绝访问整个表的所有行。

## <a name="next-steps"></a>后续步骤
  [管理服务器管理员](analysis-services-server-admins.md)   
  [使用 PowerShell 管理 Azure Analysis Services](analysis-services-powershell.md)  
  [表格模型脚本语言 (TMSL) 参考](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)


