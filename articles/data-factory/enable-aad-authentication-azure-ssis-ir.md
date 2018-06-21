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
ms.date: 06/04/2018
ms.author: douglasl
ms.openlocfilehash: 5fce1a3b8370ce49a522f41749795362e1bf1f9b
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757271"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>为 Azure-SSIS 集成运行时启用 Azure Active Directory 身份验证

本文展示了如何使用 Azure 数据工厂标识创建 Azure-SSIS IR。 通过 Azure-SSIS 集成运行时的托管服务标识 (MSI) 进行 Azure Active Directory (Azure AD) 身份验证允许你使用数据工厂 MSI 而非 SQL 身份验证来创建 Azure-SSIS 集成运行时。

有关数据工厂 MSI 的详细信息，请参阅 [Azure 数据工厂服务标识](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity)。

> [!NOTE]
> 如果已创建了使用 SQL 身份验证的 Azure-SSIS 集成运行时，则此时无法通过 PowerShell 将 IR 重新配置为使用 Azure AD 身份验证。

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>在 Azure AD 中创建一个组，并使数据工厂 MSI 成为该组的成员

可以使用现有的 Azure AD 组，使用 Azure AD PowerShell 创建新组。

1.  安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) 模块。

2.  使用 `Connect-AzureAD` 进行登录，运行以下命令来创建组，并将其保存在一个变量中：

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

3.  将数据工厂 MSI 添加到该组。 可以根据 [Azure Data Factory 服务标识](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity)中的说明获取服务标识 ID（例如 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc）。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    之后，你还可以检查组成员身份。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>在 Azure SQL 数据库上启用 Azure AD

Azure SQL 数据库支持使用 Azure AD 用户创建数据库。 因此，你可以将 Azure AD 用户设置为 Active Directory 管理员，然后使用该 Azure AD 用户登录到 SSMS。 然后，你可以为 Azure AD 组创建一个内含用户来使 IR 能够在服务器上创建 SQL Server Integration Services (SSIS) 目录。

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>为 Azure SQL 数据库启用 Azure AD 身份验证

可以使用以下步骤[为 SQL 数据库配置 Azure AD 身份验证](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure)：

1.  在 Azure 门户中，从左侧导航栏中选择“所有服务” -> “SQL 服务器”。

2.  选择要启用 Azure AD 身份验证的 SQL 数据库。

3.  在边栏选项卡的“设置”部分中，选择“Active Directory 管理员”。

4.  在命令栏中，选择“设置管理员”。

5.  选择要设为服务器管理员的 Azure AD 用户帐户，然后选择“选择”。

6.  在命令栏中，选择“保存”。

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>在数据库中创建一个代表 Azure AD 组的包含的用户

在下一步骤中，需要使用 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。

1.  启动 SQL Server Management Studio。

2.  在“连接到服务器”对话框的“服务器名称”字段中，输入 SQL 服务器名称。

3.  在“身份验证”字段中，选择“Active Directory - 通用且具有 MFA 支持”。 （也可以使用其他两个 Active Directory 身份验证类型。 请参阅[使用 SQL 数据库、托管实例配置和管理 Azure Active Directory 身份验证](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure)。）

4.  在“用户名”字段中，输入已设为服务器管理员的 Azure AD 帐户的名称，例如 testuser@xxxonline.com。

5.  选择“连接”。 完成登录过程。

6.  在“对象资源管理器”中，展开“数据库”->“系统数据库”文件夹。

7.  右键单击 **master** 数据库并选择“新建查询”。

8.  在查询窗口中输入以下行，在工具栏中选择“执行”：

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

Azure SQL 数据库托管实例不支持使用 AD 管理员之外的任何 Azure AD 用户创建数据库。因此，你必须将 Azure AD 组设置为 Active Directory 管理员。你不需要创建内含用户。

可以使用以下步骤[为 SQL 数据库托管实例服务器配置 Azure AD 身份验证](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure)：

7.  在 Azure 门户中，从左侧导航栏中选择“所有服务” -> “SQL 服务器”。

8.  选择要启用 Azure AD 身份验证的 SQL 服务器。

9.  在边栏选项卡的“设置”部分中，选择“Active Directory 管理员”。

10. 在命令栏中，选择“设置管理员”。

11. 搜索并选择 Azure AD 组（例如 SSISIrGroup），然后选择“选择”。

12. 在命令栏中，选择“保存”。

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>在门户中预配 Azure-SSIS IR

使用 Azure 门户预配 Azure-SSIS IR 时，在“SQL 设置”页上选中“为 ADF MSI 使用 AAD 身份验证”选项。 （以下屏幕截图显示了采用 Azure SQL 数据库的 IR 的设置。 对于采用托管实例的 IR，“目录数据库服务层”属性不可用，其他设置相同。）

有关如何创建 Azure-SSIS 集成运行时的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)。

![Azure-SSIS 集成运行时的设置](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>通过 PowerShell 预配 Azure-SSIS IR

若要通过 PowerShell 预配 Azure-SSIS IR，请执行以下操作：

1.  安装 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 模块。

2.  在脚本中，不要设置 *CatalogAdminCredential* 参数。 例如：

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Type Managed `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -Description $AzureSSISDescription `
                                               -Edition $AzureSSISEdition `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
