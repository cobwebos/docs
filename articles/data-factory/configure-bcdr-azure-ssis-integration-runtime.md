---
title: 为 SQL 数据库故障转移配置 Azure-SSIS Integration Runtime | Microsoft Docs
description: 本文介绍了如何针对 Azure SQL 数据库异地复制和 SSISDB 数据库的故障转移配置 Azure-SSIS Integration Runtime。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: dea0153b9ca6d8e751fd94cc558abd44b2591907
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66120432"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>针对 Azure SQL 数据库异地复制和故障转移配置 Azure-SSIS Integration Runtime

本文介绍了如何针对 Azure SQL 数据库异地复制和 SSISDB 数据库配置 Azure-SSIS Integration Runtime。 发生故障转移时，你可以确保 Azure-SSIS IR 使用辅助数据库保持工作。

异地复制和 SQL 数据库的故障转移的详细信息，请参阅[概述：活动异地复制和自动故障转移组](../sql-database/sql-database-geo-replication-overview.md)。

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

  OR

- Azure SQL 数据库服务器配置了虚拟网络服务终结点规则。

  OR

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

## <a name="next-steps"></a>后续步骤

考虑 Azure-SSIS IR 的以下其他配置选项：

- [配置 Azure-SSIS 集成运行时以实现高性能](configure-azure-ssis-integration-runtime-performance.md)

- [自定义 Azure-SSIS 集成运行时的设置](how-to-configure-azure-ssis-ir-custom-setup.md)

- [预配 Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
