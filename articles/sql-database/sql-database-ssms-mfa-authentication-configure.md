---
title: 配置多重身份验证 - Azure SQL | Microsoft Docs
description: 了解如何将多重身份验证与 SSMS 搭配用于 SQL 数据库和 SQL 数据仓库。
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 09/27/2017
ms.author: mireks
ms.openlocfilehash: 7b74cd6b62686fd03d9f42316701f44daf99eed8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>为 SQL Server Management Studio 和 Azure AD 配置多重身份验证

本主题演示如何将 Azure Active Directory 多重身份验证 (MFA) 用于 SQL Server Management Studio。 将 SSMS 或 SqlPackage.exe 连接到 Azure SQL 数据库和 Azure SQL 数据仓库时可以使用 Azure AD MFA。

有关 Azure SQL 数据库多重身份验证的概述，请参阅 [SQL 数据库和 SQL 数据仓库的通用身份验证（对 MFA 的 SSMS 支持）](sql-database-ssms-mfa-authentication.md)。

## <a name="configuration-steps"></a>配置步骤

1. **配置 Azure Active Directory** - 有关详细信息，请参阅[管理 Azure AD 目录](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[将本地标识与 Azure Active Directory 集成](../active-directory/active-directory-aadconnect.md)、[将自己的域名添加到 Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)和[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。
2. **配置 MFA** - 有关分步说明，请参阅[什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)、[Azure SQL 数据库和数据仓库的条件性访问 (MFA)](sql-database-conditional-access.md)。 （完整条件性访问需要高级 Azure Active Directory (Azure AD)。 标准 Azure AD 提供有限 MFA。）
3. **配置 SQL 数据库或 SQL 数据仓库以进行 Azure AD 身份验证** - 有关分步说明，请参阅[使用 Azure Active Directory 身份验证连接到 SQL 数据库或 SQL 数据仓库](sql-database-aad-authentication.md)。
4. **下载 SSMS** - 在客户端计算机上，从[下载 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下载最新的 SSMS。 对于本主题中的所有功能，请至少使用 2017 年 7 月的版本 17.2。  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>使用 SSMS 通用身份验证进行连接

以下步骤演示如何使用最新的 SSMS 连接到 SQL 数据库或 SQL 数据仓库。

1. 若要使用通用身份验证进行连接，请在“连接到服务器”对话框中选择“Active Directory - 通用且具有 MFA 支持”。 （如果看到“Active Directory 通用身份验证”，则使用的不是最新版本的 SSMS。）  
   ![1mfa-universal-connect][1]  
2. 采用格式 `user_name@domain.com`，使用 Azure Active Directory 凭据完成“用户名”框。  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. 如果以来宾用户身份进行连接，则必须单击“选项”，然后在“连接属性”对话框中，完成“AD 域名或租户 ID”框。 有关详细信息，请参阅 [SQL 数据库和 SQL 数据仓库的通用身份验证（对 MFA 的 SSMS 支持）](sql-database-ssms-mfa-authentication.md)。
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. 像往常一样，必须对 SQL 数据库和 SQL 数据仓库单击“选项”，然后在“选项”对话框中指定数据库。 （如果连接的用户是来宾用户（如 joe@outlook.com），则必须选中该框并在“选项”中添加当前 AD 域名或租户 ID。 请参阅 [SQL 数据库和 SQL 数据仓库的通用身份验证（对 MFA 的 SSMS 支持）]()(sql-database-ssms-mfa-authentication.md。 然后单击“连接”。  
5. 显示“**登录到帐户**”对话框时，提供 Azure Active Directory 标识的帐户和密码。 如果用户属于与 Azure AD 联合的域，则无需任何密码。  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > 如果使用不需要 MFA 的帐户进行通用身份验证，可以在此时连接。 对于需要 MFA 的用户，请继续执行以下步骤：
   >  
   
6. 可能会显示两个 MFA 设置对话框。 这个一次性操作根据 MFA 管理员设置而定，因此可能是可选的。 对于已启用 MFA 的域，有时会预定义此步骤（例如，域会要求用户使用智能卡和 PIN 码）。  
   ![3mfa-setup][3]  
7. 通过第二个可能出现的一次性对话框，可以选择身份验证方法的详细信息。 可能的选项由管理员配置。  
   ![4mfa-verify-1][4]  
8. Azure Active Directory 将向你发送确认信息。 收到验证码后，将其输入到“**输入验证码**”框中，并单击“**登录**”。  
   ![5mfa-verify-2][5]  

验证完成后，SSMS 便会正常连接（假设凭据和防火墙访问有效）。

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库多重身份验证的概述，请参阅 [SQL 数据库和 SQL 数据仓库的通用身份验证（对 MFA 的 SSMS 支持）](sql-database-ssms-mfa-authentication.md)。  
- 向其他人员授予数据库访问权限：[SQL 数据库身份验证和授权：授予访问权限](sql-database-manage-logins.md)  
- 确保其他人员可以通过防火墙进行连接：[使用 Azure 门户配置 Azure SQL 数据库服务器级防火墙规则](sql-database-configure-firewall-settings.md)  
- 使用 **Active Directory - 通用且具有 MFA** 进行身份验证时，可以使用以 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开头的 ADAL 跟踪。 在默认关闭的情况下，可在“ADAL 输出窗口跟踪级别”**中，**使用“Azure 云”的“Azure 服务”下方“选项”菜单中的“工具”，打开 ADAL 跟踪，然后在“视图”菜单中启用“输出”。 选择“Azure Active Directory 选项”时，可在输出窗口中使用跟踪。   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

