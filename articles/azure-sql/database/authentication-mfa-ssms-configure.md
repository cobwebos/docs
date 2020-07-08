---
title: 配置多重身份验证
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: 了解如何在 SSMS 中对 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 使用多重身份验证。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: b96627bfdfad039ae23dd134fc76f368d59b7d59
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85984698"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>为 SQL Server Management Studio 和 Azure AD 配置多重身份验证
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

本文介绍如何使用 SQL Server Management Studio （SSMS）的 Azure Active Directory （Azure AD）多重身份验证（MFA）。 将 SSMS 或 SqlPackage.exe 连接到[AZURE Sql 数据库](sql-database-paas-overview.md)、 [azure Sql 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)和[azure Synapse ANALYTICS （以前称为 azure sql 数据仓库）](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)时，可以使用 Azure AD MFA。 有关多重身份验证的概述，请参阅[使用 SQL 数据库、sql 托管实例和 Azure Synapse 进行通用身份验证（对 MFA 的 SSMS 支持）](../database/authentication-mfa-ssms-overview.md)。

> [!IMPORTANT]
> Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse （以前称为 Azure SQL 数据仓库）中的数据库作为数据库在本文的其余部分统称为数据库，服务器引用承载 Azure SQL 数据库和 Azure Synapse 的数据库的[服务器](logical-servers.md)。

## <a name="configuration-steps"></a>配置步骤

1. **配置 Azure Active Directory** - 有关详细信息，请参阅[管理 Azure AD 目录](https://msdn.microsoft.com/library/azure/hh967611.aspx)、[将本地标识与 Azure Active Directory 集成](../../active-directory/hybrid/whatis-hybrid-identity.md)、[将自己的域名添加到 Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)和[使用 Windows PowerShell 管理 Azure AD](https://msdn.microsoft.com/library/azure/jj151815.aspx)。
2. **配置 MFA** - 有关分步说明，请参阅[什么是 Azure 多重身份验证？](../../active-directory/authentication/multi-factor-authentication.md)、[Azure SQL 数据库和数据仓库的条件性访问 (MFA)](conditional-access-configure.md)。 （完整条件访问需要高级 Azure Active Directory。 标准 Azure AD 提供有限 MFA。）
3. **配置 Azure AD 身份验证**-有关分步说明，请参阅[使用 Azure Active Directory 身份验证连接到 SQL 数据库、Sql 托管实例或 Azure Synapse](authentication-aad-overview.md)。
4. **下载 SSMS** - 在客户端计算机上，从[下载 SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 下载最新的 SSMS。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>使用通用身份验证搭配 SSMS 进行连接

以下步骤演示如何使用最新的 SSMS 进行连接。

1. 若要使用通用身份验证进行连接，请在 SQL Server Management Studio （SSMS）中的 "**连接到服务器**" 对话框中，选择 "Active Directory" "**具有 MFA 支持**"。 （如果看到“Active Directory 通用身份验证”****，则使用的不是最新版本的 SSMS。）

   ![1mfa-universal-connect](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. 采用格式 `user_name@domain.com`，使用 Azure Active Directory 凭据完成“用户名”**** 框。

   ![1mfa-universal-connect-user](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. 如果以来宾用户身份进行连接，则不再需要填写来宾用户的“AD 域名或租户 ID”字段，因为 SSMS 18.x 或更高版本会自动识别该字段。 有关详细信息，请参阅[Sql 数据库、sql 托管实例和 Azure Synapse 的通用身份验证（对 MFA 的 SSMS 支持）](../database/authentication-mfa-ssms-overview.md)。

   ![mfa-no-tenant-ssms](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   但是，如果要使用 SSMS 17.x 或更早版本以来宾用户身份进行连接，则必须单击“选项”****，然后在“连接属性”**** 对话框中，填写“AD 域名或租户 ID”**** 框。

   ![mfa-tenant-ssms](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. 选择 **"选项"** ，然后在 "**选项**" 对话框中指定数据库。 （如果连接的用户是来宾用户（即 joe@outlook.com ），则必须选中此框，并添加当前 AD 域名或租户 ID 作为选项的一部分。 请参阅[使用 SQL 数据库和 SQL 数据仓库（针对 MFA 的 SSMS 支持）进行通用身份验证](../database/authentication-mfa-ssms-overview.md)。 然后单击“连接”  。  
5. 显示“登录到帐户” **** 对话框时，提供 Azure Active Directory 标识的帐户和密码。 如果用户属于与 Azure AD 联合的域，则无需任何密码。

   ![2mfa-sign-in](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  

   > [!NOTE]
   > 如果使用不需要 MFA 的帐户进行通用身份验证，可以在此时连接。 对于需要 MFA 的用户，请继续执行以下步骤：
   >  

6. 可能会显示两个 MFA 设置对话框。 这个一次性操作根据 MFA 管理员设置而定，因此可能是可选的。 对于已启用 MFA 的域，有时会预定义此步骤（例如，域会要求用户使用智能卡和 PIN 码）。

   ![3mfa-setup](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
7. 通过第二个可能出现的一次性对话框，可以选择身份验证方法的详细信息。 可能的选项由管理员配置。

   ![4mfa-verify-1](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
8. Azure Active Directory 向你发送确认信息。 收到验证码后，将其输入到“输入验证码”**** 框中，然后单击“登录”****。

   ![5mfa-verify-2](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  

验证完成后，SSMS 便会正常连接（假设凭据和防火墙访问有效）。

## <a name="next-steps"></a>后续步骤

- 有关多重身份验证的概述，请参阅[使用 SQL 数据库、sql 托管实例和 Azure Synapse 进行通用身份验证（对 MFA 的 SSMS 支持）](../database/authentication-mfa-ssms-overview.md)。  
- 授予其他人对数据库的访问权限：[SQL 数据库身份验证和授权：授予访问权限](logins-create-manage.md)  
- 确保其他人可以通过防火墙进行连接：[使用 Azure 门户配置服务器级防火墙规则](https://docs.microsoft.com/azure/azure-sql/database/firewall-configure)  
- 使用 **Active Directory - 通用且具有 MFA** 进行身份验证时，可以使用以 [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 开头的 ADAL 跟踪。 在默认关闭的情况下，可在“ADAL 输出窗口跟踪级别”**中，** 使用“Azure 云”**** 的“Azure 服务”**** 下方“选项”**** 菜单中的“工具”****，打开 ADAL 跟踪，然后在“视图”**** 菜单中启用“输出”****。 选择“Azure Active Directory 选项”**** 时，可在输出窗口中使用跟踪。
