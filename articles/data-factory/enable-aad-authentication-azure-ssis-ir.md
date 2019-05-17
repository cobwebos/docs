---
title: 为 Azure-SSIS 集成运行时启用 Azure Active Directory 身份验证 | Microsoft Docs
description: 本文介绍如何使用 Azure 数据工厂的托管标识启用 Azure Active Directory 身份验证，以创建 Azure-SSIS 集成运行时。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 5/14/2019
author: swinarko
ms.author: sawinark
manager: craigg
ms.openlocfilehash: a67436f09d6e28db8d19679e446ac4cf98383709
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65593805"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>为 Azure-SSIS 集成运行时启用 Azure Active Directory 身份验证

本文介绍如何启用 Azure Active Directory (Azure AD) 身份验证托管标识在 Azure 数据工厂 (ADF) 并使用它而不是 SQL 身份验证来创建将反过来预配 Azure SSIS 集成运行时 (IR)SSIS 目录数据库 (SSISDB) 中 Azure SQL 数据库服务器/托管实例代表用户。

有关在 ADF 的托管标识的详细信息，请参阅[数据工厂的托管标识](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)。

> [!NOTE]
>-  在此方案中，在创建仅使用托管标识为在 ADF 的 Azure AD 身份验证和后续的起始操作，将在 SSIS IR 的启用预配和连接到 SSISDB。 为 SSIS 包执行将 SSIS IR 仍会将连接到 SSISDB 与完全托管的 SSISDB 预配期间创建的帐户使用 SQL 身份验证。
>-  如果已创建将 SSIS IR 使用 SQL 身份验证，不可以重新配置它以在此期间，使用 PowerShell 通过 Azure AD 身份验证，但可以通过 Azure 门户/ADF 应用进行操作。 

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

3.  将 ADF 的托管标识添加到该组。 可以按照本文[数据工厂的托管标识](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)若要获取的主体的托管标识对象 ID (例如 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc，但请不要用于此目的的托管标识应用程序 ID)。

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

5.  选择 Azure AD 用户帐户进行的服务器的管理员，然后选择**选择。**

6.  在命令栏中，选择“保存”。

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>在 Azure SQL 数据库服务器中创建代表 Azure AD 组的内含用户

在下一步骤中，需要使用  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1. 启动 SSMS。

2. 在中**连接到服务器**对话框中，输入您的 Azure SQL 数据库服务器名称**服务器名称**字段。

3. 在中**身份验证**字段中，选择**Active Directory-通用且具有 MFA 支持**(您还可以使用其他两个 Active Directory 身份验证类型，请参阅[配置和管理Azure AD 身份验证与 SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure))。

4. 在中**用户名**字段中，输入作为服务器管理员，例如设置的 Azure AD 帐户的名称testuser@xxxonline.com。

5. 选择**Connect**并完成登录过程。

6. 在“对象资源管理器”中，展开“数据库” -> “系统数据库”文件夹。

7. 右键单击 master 数据库并选择“新建查询”。

8. 在查询窗口中，输入以下 T-SQL 命令，并选择**Execute**工具栏上。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   命令应会成功完成，并创建内含用户来表示组。

9. 清除查询窗口，输入以下 T-SQL 命令，然后在工具栏中选择“执行”。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   命令应会成功完成，并授予该内含用户创建数据库的权限 (SSISDB)。

10. 如果你 SSISDB 使用 SQL 身份验证创建并且想要切换到使用 Azure SSIS ir 的 Azure AD 身份验证来访问它，右键单击**SSISDB**数据库并选择**新查询**。

11. 在查询窗口中，输入以下 T-SQL 命令，并选择**Execute**工具栏上。

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

1.   在 Azure 门户中，从左侧导航栏中选择“所有服务” -> “SQL 服务器”。

2.   选择要使用 Azure AD 身份验证配置的托管实例。

3.   在边栏选项卡的“设置”部分中，选择“Active Directory 管理员”。

4.   在命令栏中，选择“设置管理员”。

5.   选择要设为服务器管理员的 Azure AD 用户帐户，然后选择“选择”。

6.   在命令栏中，选择“保存”。

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例中以用户身份添加 ADF 的托管标识

在下一步骤中，需要使用  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1.  启动 SSMS。

2.  使用 SQL/Active Directory 管理员帐户连接到托管实例。

3.  在“对象资源管理器”中，展开“数据库” -> “系统数据库”文件夹。

4.  右键单击 master 数据库并选择“新建查询”。

5.  获取 ADF 的托管标识。 可以按照本文[数据工厂的托管标识](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)到获取主体管理标识应用程序 ID （但请不要用于此目的的托管标识对象 ID）。

6.  在查询窗口中，执行以下 T-SQL 脚本，将 ADF 的托管标识转换为二进制类型：

    ```sql
    DECLARE @applicationId uniqueidentifier = '{your Managed Identity Application ID}'
    select CAST(@applicationId AS varbinary)
    ```
    
    命令应会成功完成，并以二进制形式显示 ADF 的托管标识。

7.  清除查询窗口，执行以下 T-SQL 脚本，以用户身份添加 ADF 的托管标识

    ```sql
    CREATE LOGIN [{a name for the managed identity}] FROM EXTERNAL PROVIDER with SID = {your Managed Identity Application ID as binary}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{the managed identity name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{the managed identity name}]
    ```
    
    命令应会成功完成，并授予 ADF 的托管标识创建数据库的权限 (SSISDB)。

8.  如果你 SSISDB 使用 SQL 身份验证创建并且想要切换到使用 Azure SSIS ir 的 Azure AD 身份验证来访问它，右键单击**SSISDB**数据库并选择**新查询**。

9.  在查询窗口中，输入以下 T-SQL 命令，并选择**Execute**工具栏上。

    ```sql
    CREATE USER [{the managed identity name}] FOR LOGIN [{the managed identity name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{the managed identity name}]
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
