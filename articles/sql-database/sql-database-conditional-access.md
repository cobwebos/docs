---
title: 条件性访问
description: 了解如何配置 Azure SQL 数据库和数据仓库的条件性访问。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 03/29/2019
ms.openlocfilehash: 9b8c0dbe03e47d32d8194408663973f07a07b1b9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827164"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Azure SQL 数据库和数据仓库的条件性访问 (MFA)  

Azure [SQL Database](sql-database-technical-overview.md)、[托管实例](sql-database-managed-instance.md)和[SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)支持 Microsoft 条件性访问。 

> [!NOTE]
> 本主题适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

下列步骤演示如何配置 SQL 数据库以强制实施条件性访问策略。  

## <a name="prerequisites"></a>先决条件  
- 若要支持 Azure Active Directory 身份验证，必须配置 SQL 数据库或 SQL 数据仓库。 有关具体步骤，请参阅[使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)。  
- 启用多重身份验证后，必须使用受支持的工具（如最新版 SSMS）进行连接。 有关详细信息，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。  

## <a name="configure-ca-for-azure-sql-dbdw"></a>为 Azure SQL DB/DW 配置条件性访问  
1. 登录到门户，选择 " **Azure Active Directory**"，然后选择 "**条件访问**"。 有关详细信息，请参阅 [Azure Active Directory 条件性访问技术参考](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)。  
   ![条件访问边栏选项卡](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. 在“条件性访问策略”边栏选项卡中，单击“新建策略”，提供一个名称，然后单击“配置规则”。  
3. 在 "**分配**" 下，选择 "**用户和组**"，选中 "**选择用户和组**"，然后选择用于条件性访问的用户或组。 单击“选择”，然后单击“完成”应用所选内容。  
   ![选择用户和组](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. 选择“云应用”，单击“选择应用”。 你将看到可用于条件性访问的所有应用。 选择“Azure SQL 数据库”，单击底部的“选择”，然后单击“完成”。  
   ![选择 SQL 数据库](./media/sql-database-conditional-access/select-sql-database.png)  
   如果在以下第三个屏幕截图中找不到列出的**AZURE SQL 数据库**，请完成以下步骤：   
   - 通过 SSMS 使用 AAD 管理员帐户登录 Azure SQL DB/DW 实例。  
   - 执行 `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`。  
   - 登录 AAD，验证 Azure SQL 数据库和数据仓库已在 AAD 中的应用程序中列出。  

5. 选择“访问控制”，选择“授予”，然后勾选要应用的策略。 在此示例中选择“需要多重身份验证”。  
   ![选择授予访问权限](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>摘要  
所选的应用程序（Azure SQL 数据库）允许使用 Azure AD Premium 连接 Azure SQL DB/DW，在此前提下，现在强制应用所选的条件性访问策略，即“需要多重身份验证”  
若在多重身份验证方面对 Azure SQL 数据库和数据仓库存在疑问，请联系 MFAforSQLDB@microsoft.com。  

## <a name="next-steps"></a>后续步骤  

相关教程，请参阅[保护 Azure SQL 数据库](sql-database-security-tutorial.md)。
