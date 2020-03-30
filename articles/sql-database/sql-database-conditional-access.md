---
title: 条件性访问
description: 了解如何为 Azure SQL 数据库和 Azure 突触配置条件访问。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sql-data-warehouse
ms.date: 02/06/2020
tag: azure-synpase
ms.openlocfilehash: cd56ccf2e6a4ceb0d81c25b5b9e795176be66b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124903"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-azure-synapse-analytics"></a>具有 Azure SQL 数据库和 Azure 突触分析的条件访问 （MFA）

Azure [SQL 数据库](sql-database-technical-overview.md)、[托管实例](sql-database-managed-instance.md)和[Azure 突触](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)支持 Microsoft 条件访问。 

> [!NOTE]
> 本主题适用于 Azure SQL 服务器，以及在 Azure SQL 服务器上创建的 SQL 数据库和 Azure 突触。 为简单起见，SQL 数据库在引用 SQL 数据库和 Azure 突触时使用。

下列步骤演示如何配置 SQL 数据库以强制实施条件性访问策略。  

## <a name="prerequisites"></a>先决条件  
- 必须在 Azure Synaps 中配置 SQL 数据库或 SQL 池以支持 Azure 活动目录身份验证。 有关特定步骤，请参阅[使用 SQL 数据库或 Azure 突触配置和管理 Azure 活动目录身份验证](sql-database-aad-authentication-configure.md)。  
- 启用多重身份验证后，必须使用受支持的工具（如最新版 SSMS）进行连接。 有关详细信息，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](sql-database-ssms-mfa-authentication-configure.md)。  

## <a name="configure-ca-for-azure-sql-dbdw"></a>为 Azure SQL DB/DW 配置条件性访问  
1. 登录到门户，选择 Azure**活动目录**，然后选择**条件访问**。 有关详细信息，请参阅 [Azure Active Directory 条件性访问技术参考](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)。  
   ![条件访问边栏选项卡](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2. 在“条件性访问策略”边栏选项卡中，单击“新建策略”，提供一个名称，然后单击“配置规则”。************  
3. 在 **"分配"** 下，选择 **"用户"和"组**"，选中 **"选择用户和组**"，然后为条件访问选择用户或组。 单击“选择”，然后单击“完成”应用所选内容。********  
   ![选择用户和组](./media/sql-database-conditional-access/select-users-and-groups.png)  

4. 选择“云应用”，单击“选择应用”。******** 您将看到所有可用于条件访问的应用。 选择“Azure SQL 数据库”，单击底部的“选择”，然后单击“完成”。************  
   ![选择 SQL 数据库](./media/sql-database-conditional-access/select-sql-database.png)  
   如果找不到以下第三个屏幕截图中列出的**Azure SQL 数据库**，则完成以下步骤：   
   - 通过 SSMS 使用 AAD 管理员帐户登录 Azure SQL DB/DW 实例。  
   - 执行 `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`。  
   - 登录到 AAD 并验证 Azure SQL 数据库和 Azure 突触是否列在 AAD 中的应用程序中。  

5. 选择“访问控制”，选择“授予”，然后勾选要应用的策略。******** 在此示例中选择“需要多重身份验证”。****  
   ![选择授予访问权限](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>总结  
所选的应用程序（Azure SQL 数据库）允许使用 Azure AD Premium 连接 Azure SQL DB/DW，在此前提下，现在强制应用所选的条件性访问策略，即“需要多重身份验证”****  
有关 Azure SQL 数据库和 Azure 突触有关多重身份验证的问题，请MFAforSQLDB@microsoft.com与 .  

## <a name="next-steps"></a>后续步骤  

相关教程，请参阅[保护 Azure SQL 数据库](sql-database-security-tutorial.md)。
