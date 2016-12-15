---
title: "管理 Azure Analysis Services | Microsoft Docs"
description: "了解如何在 Azure 中管理 Analysis Services 服务器。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/31/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 55a016a0943885a3aaa636316808939777afb0f8


---
# <a name="manage-analysis-services"></a>管理 Analysis Services
在 Azure 中创建 Analysis Services 服务器后，可能会有一些需要立即执行或在稍后操作期间执行的管理任务。 例如，处理刷新数据、控制谁有权访问服务器上的模型，或者监视服务器的运行状况。 某些管理任务仅可在 Azure 门户中执行，而其他一些可在 SQL Server Management Studio (SSMS) 中执行，还有部分在两者中均可执行。

## <a name="azure-portal"></a>Azure 门户
在 [Azure 门户](http://portal.azure.com/)中，可创建和删除服务器、监控服务器资源和更改大小，并可管理有权访问服务器的人员。  如果遇到问题，可提交支持请求。

![在 Azure 中获取服务器名称](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
就像连接到自身组织中的服务器实例一样，在 Azure 中连接到服务器。 可在 SSMS 中执行许多相同的任务，例如处理数据或创建处理脚本、管理角色和使用 PowerShell。

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 最大的区别之一在于用于连接到服务器的身份验证。 若要连接到 Azure Analysis Services 服务器，需要选择“Active Directory 密码身份验证”。

 使用 SSMS 时，在首次连接到服务器之前，需确保用户名已包含在 Analysis Services 管理员组中。 有关详细信息，请参阅本文后面的[服务器管理员](#server-administrators)。

### <a name="to-connect-with-ssms"></a>连接 SSMS
1. 在连接之前，需要获取服务器名称。 在 **Azure 门户**中，单击“服务器”>“概述” > “服务器名称”，然后复制服务器名称。
   
    ![在 Azure 中获取服务器名称](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. 在 SSMS >“对象资源管理器”中，单击“连接” > “Analysis Services”。
3. 在“连接到服务器”对话框中，粘贴服务器名称，然后在“身份验证”中选择以下选项之一：
   
    **Active Directory 集成身份验证**，其使用 Active Directory 到 Azure Active Directory 联合身份验证的单一登录。
   
    **Active Directory 密码身份验证**，其使用组织帐户。 例如，从未加入域的计算机进行连接时。
   
    注意：如果没有看到 Active Directory 身份验证，则可能需要在 SSMS 中[启用 Azure Active Directory 身份验证](#enable-azure-active-directory-authentication)。
   
    ![在 SSMS 中连接](./media/analysis-services-manage/aas-manage-connect-ssms.png)

由于在 Azure 中使用 SSMS 管理服务器与管理本地服务器大体相同，因此不在此处详细介绍。 所需的全部帮助均可参阅 MSDN 上的 [Analysis Services 实例管理](https://msdn.microsoft.com/library/hh230806.aspx)。

## <a name="server-administrators"></a>服务器管理员
可查找 Azure 门户或 SSMS 中服务器的控制边栏选项卡，使用“Analysis Services 管理员”管理服务器管理员。 Analysis Services 管理员是数据库服务器管理员，有权执行常用数据库管理任务，例如添加和删除数据库以及管理用户。 默认情况下，在 Azure 门户中创建服务器的用户将自动添加为 Analysis Services 管理员。

还应了解：

* Windows Live ID 不是 Azure Analysis Services 支持的标识类型。  
* Analysis Services 管理员必须是有效的 Azure Active Directory 用户。
* 如果通过 Azure Resource Manager 模板创建 Azure Analysis Services 服务器，则 Analysis Services 管理员将获取应作为管理员添加的用户的 JSON 数组。

Analysis Services 管理员可与管理 Azure 订阅资源的 Azure 资源管理员不同。 这可保持与 Analysis Services 中现有 XMLA 和 TSML 管理行为的兼容性，允许不同人员负责 Azure 资源管理和 Analysis Services 数据库管理。

若要查看 Azure Analysis Services 资源的所有角色和访问类型，请在控制边栏选项卡上使用访问控制 (IAM)。

## <a name="database-users"></a>数据库用户
Azure Analysis Services 模型数据库用户必须位于 Azure Active Directory 中。 为模型数据库指定的用户名必须是组织电子邮件地址或 UPN。 这不同于通过 Windows 域用户名支持用户的本地模型数据库。

可采用以下方式添加用户：在 Azure Active Directory 中使用[角色分配](../active-directory/role-based-access-control-configure.md)或在 SQL Server Management Studio 中使用[表格模型脚本语言](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL)。

**示例 TMSL 脚本**

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

## <a name="enable-azure-active-directory-authentication"></a>启用 Azure Active Directory 身份验证
若要在注册表中启用 SSMS 的 Azure Active Directory 身份验证功能，请创建一个名为 EnableAAD.reg 的文本文件，然后复制粘贴以下内容：

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

保存然后运行该文件。

## <a name="troubleshooting-connection-problems"></a>连接问题疑难解答
使用 SSMS 连接到服务器时，如果（在步骤 3 中）尝试使用非联合帐户或非 Azure Active Directory 中的帐户登录，并且无法连接，则可能需要清除登录缓存。 请先关闭 SSMS，再执行下列步骤。

1. 在文件资源管理器中，导航到 `C:\Users\<user_name>\AppData\Local\`。
2. 删除 **AADCacheOM** 文件夹。
3. 在**本地**文件夹中搜索以 **omlibs-tokens-cache** 名称开头的 .dat 文件。 请删除找到的任何文件。
4. 打开 SSMS 并重复前文[连接 SSMS](#to-connect-with-ssms)中的步骤。

## <a name="next-steps"></a>后续步骤
如果尚未将表格模型部署到新服务器，现在正是一个好时机。 有关详细信息，请参阅[部署到 Azure Analysis Services](analysis-services-deploy.md)。

如果已将模型部署到服务器，可使用客户端或浏览器连接到该模型。 有关详细信息，请参阅[从 Azure Analysis Services 获取数据](analysis-services-connect.md)。




<!--HONumber=Nov16_HO3-->


