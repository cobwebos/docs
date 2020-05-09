---
title: 配置 SQL 数据库故障转移 Azure-SSIS Integration Runtime
description: 本文介绍了如何针对 Azure SQL 数据库异地复制和 SSISDB 数据库的故障转移配置 Azure-SSIS Integration Runtime。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592081"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>针对 Azure SQL 数据库异地复制和故障转移配置 Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

本文介绍了如何针对 Azure SQL 数据库异地复制和 SSISDB 数据库配置 Azure-SSIS Integration Runtime。 发生故障转移时，你可以确保 Azure-SSIS IR 使用辅助数据库保持工作。

有关 SQL 数据库的异地复制和故障转移的详细信息，请参阅[概述：活动异地复制和自动故障转移组](../sql-database/sql-database-geo-replication-overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR 故障转移与 Azure SQL 数据库托管实例

### <a name="prerequisites"></a>先决条件

Azure SQL 数据库托管实例使用**数据库主密钥（DMK）** 来帮助保护存储在数据库中的数据、凭据和连接信息。 若要启用 DMK 的自动解密，请使用**服务器主密钥（SMK）** 对密钥的副本进行加密。 但 SMK 不会复制到故障转移组中，因此，在故障转移后，需要在主要和辅助实例上添加额外的密码进行 DMK 解密。

1. 在主实例上的 SSISDB 上执行以下命令。 此步骤将添加新的加密密码。

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Azure SQL 数据库托管实例上创建故障转移组。

3. 使用新的加密密码，在辅助实例上运行**sp_control_dbmasterkey_password** 。

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>方案 1 - Azure-SSIS IR 指向读写侦听器终结点

如果希望 Azure-SSIS IR 点到读写侦听器终结点，则需要首先指向主服务器终结点。 将 SSISDB 放到故障转移组后，可以更改为读写侦听器终结点，并 Azure-SSIS IR 重启。

#### <a name="solution"></a>解决方案

发生故障转移时，您必须执行以下操作：

1. 在主要区域上停止 Azure-SSIS IR。

2. 利用辅助实例的新区域、VNET 和自定义安装 SAS URI 信息编辑 Azure-SSIS IR。 由于 Azure-SSIS IR 指向读写侦听器并且终结点对 Azure-SSIS IR 是透明的，因此不需要编辑终结点。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新启动 Azure-SSIS IR。

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>方案 2 - Azure-SSIS IR 指向主服务器终结点

如果 Azure-SSIS IR 指向主服务器终结点，则此方案适用。

#### <a name="solution"></a>解决方案

发生故障转移时，您必须执行以下操作：

1. 在主要区域上停止 Azure-SSIS IR。

2. 利用辅助实例的新区域、终结点和 VNET 信息编辑 Azure-SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新启动 Azure-SSIS IR。

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>方案 3-Azure-SSIS IR 指向 Azure SQL 数据库托管实例的公共终结点

如果 Azure-SSIS IR 指向 Azure SQL 数据库托管实例的公共终结点，并且它不联接到 VNET，则此方案适用。 方案2与这种情况之间唯一的区别是，无需在故障转移后编辑 Azure-SSIS IR 的 VNET 信息。

#### <a name="solution"></a>解决方案

发生故障转移时，您必须执行以下操作：

1. 在主要区域上停止 Azure-SSIS IR。

2. 利用辅助实例的新区域和终结点信息编辑 Azure-SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新启动 Azure-SSIS IR。

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>方案 4-将现有 SSISDB （SSIS 目录）附加到新 Azure-SSIS IR

此方案适用于以下情况：你需要在次要区域预配新的 Azure-SSIS IR，或希望 SSISDB 在当前区域中发生 ADF 或 Azure-SSIS IR 灾难时继续使用新区域中的新 Azure-SSIS IR。

#### <a name="solution"></a>解决方案

发生故障转移时，您必须执行以下操作：

> [!NOTE]
> 步骤4（创建 IR）需要通过 PowerShell 来完成。 Azure 门户将报告一条错误，指出 SSISDB 已经存在。

1. 在主要区域上停止 Azure-SSIS IR。

2. 执行存储过程以更新 SSISDB 中的元数据，以接受来自** \<new_data_factory_name\> **和** \<new_integration_runtime_name\>** 的连接。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 在新区域中创建名** \<为\> new_data_factory_name**的新数据工厂。 有关详细信息，请参阅“创建数据工厂”。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    有关此 PowerShell 命令的详细信息，请参阅[使用 PowerShell 创建 Azure 数据工厂](quickstart-create-data-factory-powershell.md)

4. 使用 Azure PowerShell 在新区域中创建新的名为** \<new_integration_runtime_name\> **的 Azure-SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    有关此 PowerShell 命令的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR Azure SQL 数据库故障转移

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>方案 1 - Azure-SSIS IR 指向读写侦听器终结点

此方案适用 Azure-SSIS IR 指向故障转移组的读写侦听器终结点，并且*未*配置虚拟网络服务终结点规则的 SQL 数据库服务器。 如果希望 Azure-SSIS IR 点到读写侦听器终结点，则需要首先指向主服务器终结点。 将 SSISDB 放到故障转移组后，可以更改为读写侦听器终结点，并 Azure-SSIS IR 重启。

#### <a name="solution"></a>解决方案

发生故障转移时，它对 Azure-SSIS IR 是透明的。 Azure-SSIS IR 会自动连接到故障转移组的新的主数据库。 如果要在 Azure-SSIS IR 中更新区域或其他信息，可以将其停止，然后编辑并重新启动。


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>方案 2 - Azure-SSIS IR 指向主服务器终结点

如果 Azure-SSIS IR 指向主服务器终结点，则此方案适用。

#### <a name="solution"></a>解决方案

发生故障转移时，您必须执行以下操作：

1. 在主要区域上停止 Azure-SSIS IR。

2. 利用辅助实例的新区域、终结点和 VNET 信息编辑 Azure-SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重新启动 Azure-SSIS IR。

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>方案 3 - 将现有的 SSISDB（SSIS 目录）附加到新的 Azure-SSIS IR

此方案适用于以下情况：你需要在次要区域预配新的 Azure-SSIS IR，或希望 SSISDB 在当前区域中发生 ADF 或 Azure-SSIS IR 灾难时继续使用新区域中的新 Azure-SSIS IR。

#### <a name="solution"></a>解决方案

> [!NOTE]
> 步骤4（创建 IR）需要通过 PowerShell 来完成。 Azure 门户将报告一条错误，指出 SSISDB 已经存在。

1. 在主要区域上停止 Azure-SSIS IR。

2. 执行存储过程以更新 SSISDB 中的元数据，以接受来自** \<new_data_factory_name\> **和** \<new_integration_runtime_name\>** 的连接。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 在新区域中创建名** \<为\> new_data_factory_name**的新数据工厂。 有关详细信息，请参阅“创建数据工厂”。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    有关此 PowerShell 命令的详细信息，请参阅[使用 PowerShell 创建 Azure 数据工厂](quickstart-create-data-factory-powershell.md)

4. 使用 Azure PowerShell 在新区域中创建新的名为** \<new_integration_runtime_name\> **的 Azure-SSIS IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    有关此 PowerShell 命令的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)


## <a name="next-steps"></a>后续步骤

考虑 Azure-SSIS IR 的以下其他配置选项：

- [配置 Azure-SSIS 集成运行时以实现高性能](configure-azure-ssis-integration-runtime-performance.md)

- [自定义 Azure-SSIS 集成运行时的设置](how-to-configure-azure-ssis-ir-custom-setup.md)

- [预配 Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
