---
title: 条件性访问
description: 了解如何配置 Azure SQL 数据库、Azure SQL 托管实例和 Azure Synapse Analytics 的条件性访问。
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: ad80f68c1ab3b3583c5a22de49b77211571f345e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443976"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>使用 Azure SQL 数据库和 Azure Synapse Analytics 的条件性访问

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[AZURE Sql 数据库](sql-database-paas-overview.md)、 [azure Sql 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 支持 Microsoft 条件性访问。

以下步骤演示了如何将 Azure SQL 数据库、SQL 托管实例或 Azure Synapse 配置为强制 (CA) 策略的条件性访问。  

## <a name="prerequisites"></a>必备条件

- 你必须在 Azure Synapse 中配置 Azure SQL 数据库、Azure SQL 托管实例或 Azure SQL 池，以支持) 身份验证 Azure Active Directory (Azure AD。 有关具体步骤，请参阅 [通过 SQL 数据库或 Azure Synapse 配置和管理 Azure Active Directory 身份验证](authentication-aad-configure.md)。  
- 启用多重身份验证后，必须使用支持的工具（如最新 SQL Server Management Studio (SSMS) 进行连接。 有关详细信息，请参阅[配置 SQL Server Management Studio 的 Azure SQL 数据库多重身份验证](authentication-mfa-ssms-configure.md)。  

## <a name="configure-conditional-access"></a>配置条件访问

> [!NOTE]
> 下面的示例使用 Azure SQL 数据库，但应选择要配置条件性访问的相应产品。

1. 登录到 Azure 门户，选择 " **Azure Active Directory**"，然后选择 " **条件访问**"。 有关详细信息，请参阅 [Azure Active Directory 条件性访问技术参考](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)。  
   ![条件访问边栏选项卡](./media/conditional-access-configure/conditional-access-blade.png)

2. 在“条件性访问策略”边栏选项卡中，单击“新建策略”，提供一个名称，然后单击“配置规则”。************  
3. 在 " **分配**" 下，选择 " **用户和组**"，选中 " **选择用户和组**"，然后选择用于条件性访问的用户或组。 单击“选择”，然后单击“完成”应用所选内容。********  
   ![选择用户和组](./media/conditional-access-configure/select-users-and-groups.png)  

4. 选择“云应用”，单击“选择应用”。******** 你将看到可用于条件性访问的所有应用。 选择“Azure SQL 数据库”，单击底部的“选择”，然后单击“完成”。************  
   ![选择 SQL 数据库](./media/conditional-access-configure/select-sql-database.png)  
   如果在以下第三个屏幕截图中找不到列出的 **AZURE SQL 数据库** ，请完成以下步骤：
   - 在 Azure SQL 数据库中使用 Azure AD 管理员帐户的 SSMS 连接到数据库。  
   - 执行 `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`。  
   - 登录到 Azure AD 并验证 Azure SQL Database、SQL 托管实例或 Azure Synapse 是否列在 Azure AD 实例中的应用程序中。  

5. 选择“访问控制”，选择“授予”，然后勾选要应用的策略。******** 在此示例中选择“需要多重身份验证”。****  
   ![选择授予访问权限](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>总结

选定的应用程序 (使用 Azure AD Premium 的 Azure SQL 数据库) ，现在强制实施所选的条件性访问策略， **需要多重身份验证。**

有关 Azure SQL 数据库和 Azure Synapse 有关多重身份验证的问题，请联系 <MFAforSQLDB@microsoft.com> 。  

## <a name="next-steps"></a>后续步骤  

有关教程，请参阅 [保护 SQL 数据库中的数据库](secure-database-tutorial.md)。
