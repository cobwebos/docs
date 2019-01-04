---
title: 为 Azure-SSIS 集成运行时启用 Azure Active Directory 身份验证 | Microsoft Docs
description: 本文介绍了如何对 Azure SSIS 集成运行时进行配置以启用使用 Azure Active Directory 身份验证的连接。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: d2000e626166304e92556e3c965df175a27046ad
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321061"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>为 Azure-SSIS 集成运行时启用 Azure Active Directory 身份验证

本文展示了如何使用 Azure 数据工厂标识创建 Azure-SSIS IR。 可以针对 Azure 数据工厂的托管标识使用 Azure Active Directory (Azure AD) 身份验证而不是 SQL 身份验证，以创建 Azure-SSIS 集成运行时。

有关数据工厂的托管标识的详细信息，请参阅 [Azure 数据工厂服务标识](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)。

> [!NOTE]
> 如果已创建了使用 SQL 身份验证的 Azure-SSIS 集成运行时，则此时无法通过 PowerShell 将 IR 重新配置为使用 Azure AD 身份验证。

## <a name="enable-azure-ad-on-azure-sql-database"></a>在 Azure SQL 数据库上启用 Azure AD

Azure SQL 数据库支持使用 Azure AD 用户创建数据库。 因此，你可以将 Azure AD 用户设置为 Active Directory 管理员，然后使用该 Azure AD 用户登录到 SQL Server Management Studio (SSMS)。 然后，可为 Azure AD 组创建一个包含的用户，使 IR 能够在服务器上创建 SQL Server Integration Services (SSIS) 目录。

### <a name="create-a-group-in-azure-ad-and-make-the-managed-identity-for-your-data-factory-a-member-of-the-group"></a>在 Azure AD 中创建一个组，并使数据工厂的托管标识成为该组的成员

可以使用现有的 Azure AD 组，使用 Azure AD PowerShell 创建新组。

1.  安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模块。

2.  使用  `Connect-AzureAD` 登录，运行以下命令来创建组，并将该组保存在变量中：

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    输出如以下示例所示，此示例还检查变量的值：

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  将数据工厂的托管标识添加到该组。 可以根据 [Azure Data Factory 服务标识](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)获取主体服务标识 ID（例如 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc，但不要将服务标识应用程序 ID 用于此目的）。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    之后，你还可以检查组成员身份。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>为 Azure SQL 数据库启用 Azure AD 身份验证

可使用以下步骤 [为 SQL 数据库配置 Azure AD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)：

1.  在 Azure 门户中，从左侧导航栏中选择“所有服务” -> “SQL 服务器”。 **** 

2.  选择要启用 Azure AD 身份验证的 SQL 数据库。

3.  在边栏选项卡的“设置”部分中，选择“Active Directory 管理员”。 ****  ****

4.  在命令栏中，选择“设置管理员” ****。

5.  选择要设为服务器管理员的 Azure AD 用户帐户，然后选择“选择”。 ****

6.  在命令栏中，选择“保存”。 ****

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>在数据库中创建一个代表 Azure AD 组的包含的用户

在下一步骤中，需要使用  [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1.  启动 SQL Server Management Studio。

2.  在“连接到服务器”对话框的“服务器名称”字段中，输入 SQL 服务器名称。 ****  **** 

3.  在“身份验证”字段中，选择“Active Directory - 通用且具有 MFA 支持”。 ****  **** （也可以使用其他两个 Active Directory 身份验证类型。 请参阅[使用 SQL 数据库、托管实例配置和管理 Azure Active Directory 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)。）

4.  在“用户名”字段中，输入已设为服务器管理员的 Azure AD 帐户的名称，例如 testuser@xxxonline.com。 **** 

5.  选择“连接”。 **** 完成登录过程。

6.  在“对象资源管理器”中，展开“数据库”->“系统数据库”文件夹。 **** ****

7.  右键单击 **master** 数据库并选择“新建查询”。 ****

8.  在查询窗口中输入以下行，在工具栏中选择“执行”： **** 

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    命令应会成功完成，并创建组的包含的用户。

9.  清除查询窗口，输入以下行，在工具栏中选择“执行”：

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    命令应会成功完成，并授予该内含用户创建数据库的权限。

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例上启用 Azure AD

Azure SQL 数据库托管实例支持直接使用 MSI 创建数据库。 你不需要将数据工厂 MSI 加入到 AD 组，也不需要在 MI 中创建包含的用户。

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例启用 Azure AD 身份验证

1.   在 Azure 门户中，从左侧导航栏中选择“所有服务” -> “SQL 服务器”。

1.   选择要启用 Azure AD 身份验证的 SQL 服务器。

1.   在边栏选项卡的“设置”部分中，选择“Active Directory 管理员”。

1.   在命令栏中，选择“设置管理员”。

1.   选择要设为服务器管理员的 Azure AD 用户帐户，然后选择“选择”。

1.   在命令栏中，选择“保存”。

### <a name="add-data-factory-msi-as-a-user-to-the-azure-sql-database-managed-instance"></a>将数据工厂 MSI 作为用户添加到 Azure SQL 数据库托管实例

1.  启动 SQL Server Management Studio。

2.  以 SQL 管理员帐户或 Active Directory 管理员帐户进行登录。

3.  在“对象资源管理器”中，展开“数据库”->“系统数据库”文件夹。

4.  右键单击 master 数据库并选择“新建查询”。

5.  可以按照 [Azure 数据工厂服务标识](data-factory-service-identity.md)一文来获取主体服务标识应用程序 ID。 （对于此用途，请不要使用服务标识 ID。）

6.  在查询窗口中，运行以下脚本将服务标识应用程序 ID 转换为二进制类型：

    ```sql
    DECLARE @applicationId uniqueidentifier = {your service identity application id}
    select CAST(@applicationId AS varbinary)
    ```

7.  可以从结果窗口中获取值。

8.  清除查询窗口，并运行以下脚本：

    ```sql
    CREATE LOGIN [{MSI name}] FROM EXTERNAL PROVIDER with SID ={your service identity application id in binary type}, TYPE = E
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{MSI name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{MSI name}]
    ```

9.  命令已成功完成。

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>在门户中预配 Azure-SSIS IR

使用 Azure 门户预配 Azure-SSIS IR 时，在“SQL 设置”页上选中“对 ADF 的托管标识使用 AAD 身份验证”选项。 （以下屏幕截图显示了采用 Azure SQL 数据库的 IR 的设置。 对于采用托管实例的 IR，“目录数据库服务层”属性不可用，其他设置相同。）

有关如何创建 Azure-SSIS 集成运行时的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

![Azure-SSIS 集成运行时的设置](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>通过 PowerShell 预配 Azure-SSIS IR

若要通过 PowerShell 预配 Azure-SSIS IR，请执行以下操作：

1.  安装 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 模块。

2.  在脚本中，不要设置 *CatalogAdminCredential* 参数。 例如：

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
