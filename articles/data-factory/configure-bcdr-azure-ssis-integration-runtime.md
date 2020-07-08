---
title: 为 SQL 数据库故障转移配置 Azure-SSIS 集成运行时
description: 本文介绍了如何针对 Azure SQL 数据库异地复制和 SSISDB 数据库的故障转移配置 Azure-SSIS 集成运行时
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
ms.openlocfilehash: e1b70e0e3eb54253972afded1bd37363d1a868e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84195715"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-sql-database-geo-replication-and-failover"></a>针对 SQL 数据库异地复制和故障转移配置 Azure-SSIS 集成运行时

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

本文介绍了如何针对 Azure SQL 数据库异地复制和 SSISDB 数据库配置 Azure-SSIS 集成运行时 (IR)。 发生故障转移时，你可以确保 Azure-SSIS IR 使用辅助数据库保持工作。

有关 SQL 数据库的异地复制和故障转移的详细信息，请参阅[概述：活动异地复制和自动故障转移组](../sql-database/sql-database-geo-replication-overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-a-sql-managed-instance"></a>使用 SQL 托管实例进行 Azure-SSIS IR 故障转移

### <a name="prerequisites"></a>先决条件

Azure SQL 托管实例使用数据库主密钥 (DMK) 来帮助保护存储在数据库中的数据、凭据和连接信息。 为启用 DMK 的自动解密，将通过服务器主密钥 (SMK) 对某个密钥副本进行加密。 

SMK 不会在故障转移组中复制。 故障转移后，需要在主实例和辅助实例上添加用于 DMK 解密的密码。

1. 在主实例上为 SSISDB 运行以下命令。 此步骤将添加新的加密密码。

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. 在 SQL 托管实例上创建故障转移组。

3. 使用新的加密密码在辅助实例上运行 sp_control_dbmasterkey_password。

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>应用场景 1：Azure-SSIS IR 指向读/写侦听器终结点

如果希望 Azure-SSIS IR 指向读/写侦听器终结点，则需要首先指向主服务器终结点。 将 SSISDB 放入故障转移组后，可以切换到读/写侦听器终结点，然后重启 Azure-SSIS IR。

#### <a name="solution"></a>解决方案

发生故障转移时，请执行以下步骤：

1. 停止主要区域中的 Azure-SSIS IR。

2. 使用与辅助实例上自定义安装相关的新区域、虚拟网络和共享访问签名 (SAS) URI 信息对 Azure-SSIS IR 进行编辑。 由于 Azure-SSIS IR 指向读/写侦听器且终结点对 Azure-SSIS IR 是透明的，因此不需要编辑终结点。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重启 Azure-SSIS IR。

### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>应用场景 2：Azure-SSIS IR 指向主服务器终结点

如果 Azure-SSIS IR 指向主服务器终结点，则此方案适用。

#### <a name="solution"></a>解决方案

发生故障转移时，请执行以下步骤：

1. 停止主要区域中的 Azure-SSIS IR。

2. 使用辅助实例的新区域、终结点和虚拟网络信息来编辑 Azure-SSIS IR。

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database endpoint" `
                    -CatalogAdminCredential "Azure SQL Database admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
        ```

3. Restart the Azure-SSIS IR.

### Scenario 3: Azure-SSIS IR is pointing to a public endpoint of a SQL Managed Instance

This scenario is suitable if the Azure-SSIS IR is pointing to a public endpoint of a Azure SQL Managed Instance and it doesn't join to a virtual network. The only difference from scenario 2 is that you don't need to edit virtual network information for the Azure-SSIS IR after failover.

#### Solution

When failover occurs, take the following steps:

1. Stop the Azure-SSIS IR in the primary region.

2. Edit the Azure-SSIS IR with the new region and endpoint information for the secondary instance.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重启 Azure-SSIS IR。

### <a name="scenario-4-attach-an-existing-ssisdb-instance-ssis-catalog-to-a-new-azure-ssis-ir"></a>方案 4：将现有 SSISDB 实例（SSIS 目录）附加到新的 Azure-SSIS IR

如果希望 SSISDB 在当前区域中发生 Azure 数据工厂或 Azure-SSIS IR 灾难时在新区域中使用新的 Azure-SSIS IR，则此方案适用。

#### <a name="solution"></a>解决方案

发生故障转移时，请执行以下步骤。

> [!NOTE]
> 使用 PowerShell 执行步骤 4（创建 IR）。 如果不执行，Azure 门户将报告一条错误，指出“SSISDB 已存在”。

1. 停止主要区域中的 Azure-SSIS IR。

2. 运行存储过程以更新 SSISDB 中的元数据，从而接受来自 \<new_data_factory_name\> 和 \<new_integration_runtime_name\> 的连接 。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 在新区域中创建名为 \<new_data_factory_name\> 的新数据工厂。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    有关此 PowerShell 命令的详细信息，请参阅[使用 PowerShell 创建 Azure 数据工厂](quickstart-create-data-factory-powershell.md)。

4. 使用 Azure PowerShell 在新区域中创建名为 \<new_integration_runtime_name\> 的新 Azure-SSIS IR。

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

    有关此 PowerShell 命令的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。



## <a name="azure-ssis-ir-failover-with-sql-database"></a>使用 SQL 数据库进行 Azure-SSIS IR 故障转移

### <a name="scenario-1-azure-ssis-ir-is-pointing-to-a-readwrite-listener-endpoint"></a>应用场景 1：Azure-SSIS IR 指向读/写侦听器终结点

此方案适用于以下情况：

- Azure-SSIS IR 指向故障转移组的读/写侦听器终结点。
- SQL 数据库服务器未配置虚拟网络服务终结点规则。

如果希望 Azure-SSIS IR 指向读/写侦听器终结点，则需要首先指向主服务器终结点。 将 SSISDB 放入故障转移组后，可以更改为读/写侦听器终结点，然后重启 Azure-SSIS IR。

#### <a name="solution"></a>解决方案

发生故障转移时，它对 Azure-SSIS IR 是透明的。 Azure-SSIS IR 会自动连接到故障转移组的新的主数据库。 

若要更新 Azure-SSIS IR 中的区域或其他信息，可将其停止，对其进行编辑，然后重启。


### <a name="scenario-2-azure-ssis-ir-is-pointing-to-a-primary-server-endpoint"></a>应用场景 2：Azure-SSIS IR 指向主服务器终结点

如果 Azure-SSIS IR 指向主服务器终结点，则此方案适用。

#### <a name="solution"></a>解决方案

发生故障转移时，请执行以下步骤：

1. 停止主要区域中的 Azure-SSIS IR。

2. 使用辅助实例的新区域、终结点和虚拟网络信息来编辑 Azure-SSIS IR。

    ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                        -CatalogServerEndpoint "Azure SQL Database endpoint" `
                        -CatalogAdminCredential "Azure SQL Database admin credentials" `
                        -VNetId "new VNet" `
                        -Subnet "new subnet" `
                        -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. 重启 Azure-SSIS IR。

### <a name="scenario-3-attach-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>应用场景 3：将现有 SSISDB（SSIS 目录）附加到新的 Azure-SSIS IR

若要在次要区域中预配新的 Azure-SSIS IR，则此方案适用。 如果希望 SSISDB 在当前区域中发生 Azure 数据工厂或 Azure-SSIS IR 灾难时继续在新区域中使用新的 Azure-SSIS IR，则此方案也适用。

#### <a name="solution"></a>解决方案

发生故障转移时，请执行以下步骤。

> [!NOTE]
> 使用 PowerShell 执行步骤 4（创建 IR）。 如果不执行，Azure 门户将报告一条错误，指出“SSISDB 已存在”。

1. 停止主要区域中的 Azure-SSIS IR。

2. 运行存储过程以更新 SSISDB 中的元数据，从而接受来自 \<new_data_factory_name\> 和 \<new_integration_runtime_name\> 的连接 。
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. 在新区域中创建名为 \<new_data_factory_name\> 的新数据工厂。

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    有关此 PowerShell 命令的详细信息，请参阅[使用 PowerShell 创建 Azure 数据工厂](quickstart-create-data-factory-powershell.md)。

4. 使用 Azure PowerShell 在新区域中创建名为 \<new_integration_runtime_name\> 的新 Azure-SSIS IR。

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

    有关此 PowerShell 命令的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。


## <a name="next-steps"></a>后续步骤

考虑 Azure-SSIS IR 的以下其他配置选项：

- [配置 Azure-SSIS 集成运行时以实现高性能](configure-azure-ssis-integration-runtime-performance.md)

- [自定义 Azure-SSIS 集成运行时的设置](how-to-configure-azure-ssis-ir-custom-setup.md)

- [预配 Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
