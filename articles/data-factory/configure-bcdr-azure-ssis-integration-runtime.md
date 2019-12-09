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
ms.date: 08/14/2018
ms.openlocfilehash: 92f7d25a9c19409b220b6a71fba87da91e51a415
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928498"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>针对 Azure SQL 数据库异地复制和故障转移配置 Azure-SSIS Integration Runtime

本文介绍了如何针对 Azure SQL 数据库异地复制和 SSISDB 数据库配置 Azure-SSIS Integration Runtime。 发生故障转移时，你可以确保 Azure-SSIS IR 使用辅助数据库保持工作。

有关 SQL 数据库的异地复制和故障转移的详细信息，请参阅[概述：活动异地复制和自动故障转移组](../sql-database/sql-database-geo-replication-overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>方案 1 - Azure-SSIS IR 指向读写侦听器终结点

### <a name="conditions"></a>条件

当以下条件成立时本部分内容适用：

- Azure-SSIS IR 指向故障转移组的读写侦听器终结点。

  AND

- SQL 数据库服务器“未”配置虚拟网络服务终结点规则。

### <a name="solution"></a>解决方案

发生故障转移时，它对 Azure-SSIS IR 是透明的。 Azure-SSIS IR 会自动连接到故障转移组的新的主数据库。

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>方案 2 - Azure-SSIS IR 指向主服务器终结点

### <a name="conditions"></a>条件

当以下条件之一成立时本部分内容适用：

- Azure-SSIS IR 指向故障转移组的主服务器终结点。 发生故障转移时，此终结点更改。

  或

- Azure SQL 数据库服务器配置了虚拟网络服务终结点规则。

  或

- 数据库服务器是配置有虚拟网络的 SQL 数据库托管实例。

### <a name="solution"></a>解决方案

发生故障转移时，您必须执行以下操作：

1. 停止 Azure-SSIS IR。

2. 重新配置 IR 以指向新的主终结点并指向新区域中的虚拟网络。

3. 重启 IR。

以下各部分更详细地说明了这些步骤。

### <a name="prerequisites"></a>必备组件

- 确保为 Azure SQL 数据库服务器启用灾难恢复，以防该服务器同时发生服务中断。 有关详细信息，请参阅[使用 Azure SQL 数据库确保业务连续性的相关概述](../sql-database/sql-database-business-continuity.md)。

- 如果在当前区域中使用虚拟网络，则需要在新区域中使用另一个虚拟网络连接到 Azure-SSIS 集成运行时。 有关详细信息，请参阅[将 Azure-SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。

- 如果使用自定义设置，可能需要为存储自定义设置脚本和关联文件的 Blob 容器准备另一个 SAS URI，以便在中断期间可以继续访问该容器。 有关详细信息，请参阅[在 Azure-SSIS 集成运行时中配置自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="steps"></a>Steps

遵循以下步骤停止 Azure-SSIS IR，切换到新区域，然后再次启动该 IR。

1. 在原始区域中停止 IR。

2. 在 PowerShell 中调用以下命令来使用新设置更新 IR。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    有关此 PowerShell 命令的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)

3. 再次启动 IR。

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>方案 3-将现有 SSISDB （SSIS 目录）附加到新 Azure-SSIS IR

当当前区域发生 ADF 或 Azure-SSIS IR 灾难时，可以使 SSISDB 在新区域中继续使用新的 Azure-SSIS IR。

### <a name="prerequisites"></a>必备组件

- 如果在当前区域中使用虚拟网络，则需要在新区域中使用另一个虚拟网络连接到 Azure-SSIS 集成运行时。 有关详细信息，请参阅[将 Azure-SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。

- 如果使用自定义设置，可能需要为存储自定义设置脚本和关联文件的 Blob 容器准备另一个 SAS URI，以便在中断期间可以继续访问该容器。 有关详细信息，请参阅[在 Azure-SSIS 集成运行时中配置自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。

### <a name="steps"></a>Steps

遵循以下步骤停止 Azure-SSIS IR，切换到新区域，然后再次启动该 IR。

1. 执行存储过程，使 SSISDB 附加到 **\<new_data_factory_name\>** 或 **\<new_integration_runtime_name\>** 。
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. 在新区域中创建名为 **\<new_data_factory_name\>** 的新数据工厂。 有关详细信息，请参阅创建数据工厂。

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    有关此 PowerShell 命令的详细信息，请参阅[使用 PowerShell 创建 Azure 数据工厂](quickstart-create-data-factory-powershell.md)

3. 使用 Azure PowerShell 在新区域中创建一个名为\<的新 Azure-SSIS IR **new_integration_runtime_name\>** 。

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

4. 再次启动 IR。

## <a name="next-steps"></a>后续步骤

考虑 Azure-SSIS IR 的以下其他配置选项：

- [配置 Azure-SSIS 集成运行时以实现高性能](configure-azure-ssis-integration-runtime-performance.md)

- [自定义 Azure-SSIS 集成运行时的设置](how-to-configure-azure-ssis-ir-custom-setup.md)

- [预配 Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
