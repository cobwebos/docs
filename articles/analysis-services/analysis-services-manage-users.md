---
title: "管理 Azure Analysis Services 中的用户| Microsoft Docs"
description: "了解如何在 Azure 中管理 Analysis Services 服务器上的用户。"
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
ms.date: 01/18/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>管理 Azure Analysis Services 中的用户
Azure Analysis Services 中存在两种类型的用户，即服务器管理员和数据库用户。 

## <a name="server-administrators"></a>服务器管理员
可以为 Azure 门户中的服务器使用控制边栏选项卡中的“Analysis Services 管理员”，或使用 SSMS 中的“服务器属性”来管理服务器管理员。 Analysis Services 管理员是数据库服务器管理员，有权执行常用数据库管理任务，例如添加和删除数据库以及管理用户。 默认情况下，在 Azure 门户中创建服务器的用户将自动添加为 Analysis Services 管理员。

![Azure 门户中的服务器管理员](./media/analysis-services-manage-users/aas-manage-users-admins.png)

还应了解：

* Windows Live ID 不是 Azure Analysis Services 支持的标识类型。  
* Analysis Services 管理员必须是有效的 Azure Active Directory 用户。
* 如果通过 Azure Resource Manager 模板创建 Azure Analysis Services 服务器，则 Analysis Services 管理员将获取应作为管理员添加的用户的 JSON 数组。

Analysis Services 管理员可与管理 Azure 订阅资源的 Azure 资源管理员不同。 这可保持与 Analysis Services 中现有 XMLA 和 TMSL 管理行为的兼容性，允许不同人员负责 Azure 资源管理和 Analysis Services 数据库管理。 若要查看 Azure Analysis Services 资源的所有角色和访问类型，请在控制边栏选项卡上使用访问控制 (IAM)。

### <a name="to-add-administrators-using-azure-portal"></a>使用 Azure 门户添加管理员
1. 在服务器的控制边栏选项卡中，单击“Analysis Services 管理员”。
2. 在“\<servername> - Analysis Services 管理员”边栏选项卡中，单击“添加”。
3. 在“添加服务器管理员”边栏选项卡中，选择要添加的用户帐户。

## <a name="database-users"></a>数据库用户
必须将数据库用户添加至数据库角色。 角色定义具有相同数据库权限的用户和组。 默认情况下，表格模型数据库具有默认用户角色，它包含读取权限。 若要了解详细信息，请参阅[表格模型中的角色](https://msdn.microsoft.com/library/hh213165.aspx)。

Azure Analysis Services 模型数据库用户必须位于 Azure Active Directory 中。 所指定的用户名必须是组织电子邮件地址或 UPN。 这不同于通过 Windows 域用户名支持用户的本地表格模型数据库。 

可以创建数据库角色、添加用户和组到角色，并在 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 中配置行级安全性。 还可以使用 [Analysis Services PowerShell cmdlet](https://msdn.microsoft.com/library/hh758425.aspx) 或[表格模型脚本语言](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) 向角色添加或删除用户。

**示例 TMSL 脚本**

在此示例中，会将一个用户和一个组添加到 SalesBI 数据库的用户角色中。

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
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
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

订阅管理员可以在控制边栏选项卡中使用“访问控制”来配置角色。 这与服务器管理员或数据库用户不同，如上所述它们是以服务器或数据库级别配置的。 

![Azure 门户中的访问控制](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

角色适用于需要执行可在门户中完成或使用 Azure Resource Manager 模板完成的任务的用户或帐户。 若要了解详细信息，请参阅[基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)。

## <a name="next-steps"></a>后续步骤
如果尚未将表格模型部署到服务器，现在正是一个好时机。 有关详细信息，请参阅[部署到 Azure Analysis Services](analysis-services-deploy.md)。

如果已将模型部署到服务器，可使用客户端或浏览器连接到该模型。 有关详细信息，请参阅[从 Azure Analysis Services 获取数据](analysis-services-connect.md)。


