---
title: Enable Azure Active Directory for Azure-SSIS Integration Runtime
description: 本文介绍如何使用 Azure 数据工厂的托管标识启用 Azure Active Directory 身份验证，以创建 Azure-SSIS 集成运行时。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.date: 5/14/2019
ms.openlocfilehash: 6973e72b06d51241e883038936270fd0931365d7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217700"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>为 Azure-SSIS 集成运行时启用 Azure Active Directory 身份验证

This article shows you how to enable Azure Active Directory (Azure AD) authentication with the managed identity for your Azure Data Factory (ADF) and use it instead of conventional authentication methods (like SQL authentication) to:

- Create an Azure-SSIS Integration Runtime (IR) that will in turn provision SSIS catalog database (SSISDB) in Azure SQL Database server/Managed Instance on your behalf.

- Connect to various Azure resources when running SSIS packages on Azure-SSIS IR.

For more info about the managed identity for your ADF, see [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  In this scenario, Azure AD authentication with the managed identity for your ADF is only used in the creation and subsequent starting operations of your SSIS IR that will in turn provision and connect to SSISDB. For SSIS package executions, your SSIS IR will still connect to SSISDB using SQL authentication with fully managed accounts that are created during SSISDB provisioning.
>-  If you have already created your SSIS IR using SQL authentication, you can not reconfigure it to use Azure AD authentication via PowerShell at this time, but you can do so via Azure portal/ADF app. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>在 Azure SQL 数据库上启用 Azure AD

Azure SQL 数据库服务器支持使用 Azure AD 用户创建数据库。 首先，需要创建一个 Azure AD 组，其中 ADF 的托管标识作为成员。 接下来，需要将 Azure AD 用户设置为 Azure SQL 数据库服务器的 Active Directory 管理员，然后使用该用户在 SQL Server Management Studio (SSMS) 上连接到该服务器。 最后，需要创建一个代表 Azure AD 组的内含用户，因此 Azure-SSIS IR 可以使用 ADF 的托管标识代表你创建 SSISDB。

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>创建一个 Azure AD 组，其中 ADF 的托管标识作为成员

可以使用现有的 Azure AD 组，或使用 Azure AD PowerShell 创建新组。

1.  安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模块。

2.  使用  `Connect-AzureAD` 登录，运行以下 cmdlet 来创建组，并将该组保存在变量中：

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    结果如以下示例所示，此示例还显示变量值：

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  将 ADF 的托管标识添加到该组。 You can follow the article [Managed identiy for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) to get the principal Managed Identity Object ID (e.g. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, but do not use Managed Identity Application ID for this purpose).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    之后还可以检查组成员身份。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>为 Azure SQL 数据库服务器配置 Azure AD 身份验证

可使用以下步骤 [通过 SQL 配置和管理 Azure AD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)：

1.  在 Azure 门户中，从左侧导航栏中选择“所有服务” -> “SQL 服务器”。

2.  选择要使用 Azure AD 身份验证配置的 Azure SQL 数据库服务器。

3.  在边栏选项卡的“设置”部分中，选择“Active Directory 管理员”。

4.  在命令栏中，选择“设置管理员”。

5.  Select an Azure AD user account to be made administrator of the server, and then select **Select.**

6.  在命令栏中，选择“保存”。

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>在 Azure SQL 数据库服务器中创建代表 Azure AD 组的内含用户

在下一步骤中，需要使用  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1. 启动 SSMS。

2. In the **Connect to Server** dialog, enter your Azure SQL Database server name in the **Server name** field.

3. In the **Authentication** field, select **Active Directory - Universal with MFA support** (you can also use the other two Active Directory authentication types, see [Configure and manage Azure AD authentication with SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)).

4. In the **User name** field, enter the name of Azure AD account that you set as the server administrator, e.g. testuser@xxxonline.com.

5. select **Connect** and complete the sign-in process.

6. 在“对象资源管理器”中，展开“数据库” -> “系统数据库”文件夹。

7. 右键单击 master 数据库并选择“新建查询”。

8. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   命令应会成功完成，并创建内含用户来表示组。

9. 清除查询窗口，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   命令应会成功完成，并授予该内含用户创建数据库的权限 (SSISDB)。

10. If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

11. In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    命令应会成功完成，并创建内含用户来表示组。

12. 清除查询窗口，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    命令应会成功完成，并授予该内含用户访问 SSISDB 的权限。

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例上启用 Azure AD

Azure SQL 数据库托管实例支持直接使用 ADF 的托管标识创建数据库。 无需将 ADF 的托管标识加入 Azure AD 组，也无需在托管实例中创建代表该组的内含用户。

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置 Azure AD 身份验证

Follow the steps in [Provision an Azure Active Directory administrator for your Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例中以用户身份添加 ADF 的托管标识

在下一步骤中，需要使用  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1.  启动 SSMS。

2.  Connect to your Managed Instance using a SQL Server account that is a **sysadmin**. This is a temporary limitation that will be removed once Azure AD server principals (logins) for Azure SQL Database Managed Instance becomes GA. You will see the following error if you try to use an Azure AD admin account to create the login: Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.

3.  在“对象资源管理器”中，展开“数据库” -> “系统数据库”文件夹。

4.  右键单击 master 数据库并选择“新建查询”。

5.  In the query window, execute the following T-SQL script to add the managed identity for your ADF as a user

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    命令应会成功完成，并授予 ADF 的托管标识创建数据库的权限 (SSISDB)。

6.  If your SSISDB was created using SQL authentication and you want to switch to use Azure AD authentication for your Azure-SSIS IR to access it, right-click on **SSISDB** database and select **New query**.

7.  In the query window, enter the following T-SQL command, and select **Execute** on the toolbar.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    命令应会成功完成，并授予 ADF 的托管标识访问 (SSISDB) 的权限。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>在 Azure 门户/ADF 应用中预配 Azure-SSIS IR

在 Azure 门户/ADF 应用中预配 Azure-SSIS IR 时，在“SQL 设置”页上选择“对 ADF 的托管标识使用 AAD 身份验证”选项。 以下屏幕截图显示了采用承载 SSISDB 的 Azure SQL 数据库服务器的 IR 的设置。 对于承载 SSISDB 的托管实例的 IR，“目录数据库服务层”和“允许 Azure 服务访问”设置不适用，而其他设置相同。

有关如何创建 Azure-SSIS IR 的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

![Azure-SSIS 集成运行时的设置](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>通过 PowerShell 预配 Azure-SSIS IR

若要通过 PowerShell 预配 Azure-SSIS IR，请执行以下操作：

1.  安装 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)  模块。

2.  在脚本中，不要设置 `CatalogAdminCredential` 参数。 例如：

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Run SSIS Packages with Managed Identity Authentication

When you run SSIS packages on Azure-SSIS IR, you can use managed identity authentication to connect to various Azure resources. Currently we have already supported managed identity authentication in the following connection managers.

- [OLE DB Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
