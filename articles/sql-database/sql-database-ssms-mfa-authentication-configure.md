---
title: "配置多重身份验证 - Azure SQL | Microsoft Docs"
description: "将 Multi-Factored Authentication 与 SSMS 搭配用于 SQL 数据库和 SQL 数据仓库。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/08/2017
ms.author: rickbyh
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 1815ea909e35a02b82b4c836d7baaf6390d20bdd
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证

本主题介绍如何配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证。 

有关 Azure SQL 数据库多重身份验证的概述，请参阅 [SQL 数据库和 SQL 数据仓库的通用身份验证（对 MFA 的 SSMS 支持）](sql-database-ssms-mfa-authentication.md)。

## <a name="configuration-steps"></a>配置步骤

1. **配置 Azure Active Directory** - 有关详细信息，请参阅[将本地标识与 Azure Active Directory 集成](../active-directory/active-directory-aadconnect.md)、[将自己的域名添加到 Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[管理 Azure AD 目录](https://msdn.microsoft.com/library/azure/hh967611.aspx)和[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。
2. **配置 MFA** - 有关分步说明，请参阅 [Azure SQL 数据库和数据仓库的条件性访问 (MFA)](sql-database-conditional-access.md)。 
3. **配置 SQL 数据库或 SQL 数据仓库以进行 Azure AD 身份验证** - 有关分步说明，请参阅[使用 Azure Active Directory 身份验证连接到 SQL 数据库或 SQL 数据仓库](sql-database-aad-authentication.md)。
4. **下载 SSMS** - 在客户端计算机上，从[下载 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下载最新的 SSMS（至少是 2016 年 8 月版）。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>使用 SSMS 通用身份验证进行连接

以下步骤演示如何使用最新的 SSMS 连接到 SQL 数据库或 SQL 数据仓库。

1. 若要使用通用身份验证进行连接，请在“**连接到服务器**”对话框中选择“**Active Directory 通用身份验证**”。

   ![1mfa-universal-connect][1]
2. 像往常一样，必须对 SQL 数据库和 SQL 数据仓库单击“选项”，然后在“选项”对话框中指定数据库。 然后单击“**连接**”。
3. 显示“**登录到帐户**”对话框时，提供 Azure Active Directory 标识的帐户和密码。

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > 如果使用不需要 MFA 的帐户进行通用身份验证，可以在此时连接。 对于需要 MFA 的用户，请继续执行以下步骤：
   > 
   > 
4. 可能会显示两个 MFA 设置对话框。 这个一次性操作根据 MFA 管理员设置而定，因此可能是可选的。 对于已启用 MFA 的域，有时会预定义此步骤（例如，域会要求用户使用智能卡和 PIN 码）。  

   ![3mfa-setup][3]
5. 通过第二个可能出现的一次性对话框，可以选择身份验证方法的详细信息。 可能的选项由管理员配置。

   ![4mfa-verify-1][4]
6. Azure Active Directory 向你发送确认信息。 收到验证码后，将其输入到“**输入验证码**”框中，然后单击“**登录**”。

   ![5mfa-verify-2][5]

验证完成后，SSMS 便会正常连接（假设凭据和防火墙访问有效）。

## <a name="next-steps"></a>后续步骤

* 有关 Azure SQL 数据库多重身份验证的概述，请参阅 [SQL 数据库和 SQL 数据仓库的通用身份验证（对 MFA 的 SSMS 支持）](sql-database-ssms-mfa-authentication.md)。
* 向其他人员授予数据库访问权限：[SQL 数据库身份验证和授权：授予访问权限](sql-database-manage-logins.md)  
确保其他人员可以通过防火墙进行连接：[使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md)

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


