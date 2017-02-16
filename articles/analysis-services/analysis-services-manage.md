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
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 17df0dfc32078fc08d2e744d4e83f1a1d77a9da1
ms.openlocfilehash: d6fbb7febc05548e1e89e12394bbb7064e5da1c9


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

## <a name="server-administrators-and-database-users"></a>服务器管理员和数据库用户
Azure Analysis Services 中存在两种类型的用户，即服务器管理员和数据库用户。 这两种类型的用户必须存在于 Azure Active Directory 中，且必须由组织电子邮件地址或 UPN 指定。 这不同于通过 Windows 域用户名支持服务器管理员和数据库用户的本地表格模型数据库。 有关详细信息，请参阅[管理 Azure Analysis Services 中的用户](analysis-services-manage-users.md)。

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




<!--HONumber=Jan17_HO3-->


