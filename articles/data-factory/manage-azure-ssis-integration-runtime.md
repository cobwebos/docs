---
title: "重新配置 Azure-SSIS 集成运行时 | Microsoft Docs"
description: "了解在预配 Azure-SSIS 集成运行时之后，如何在 Azure 数据工厂中对其进行重新配置。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: spelluru
ms.openlocfilehash: c1743a0d06f911122ed0aba586aec837f81c578c
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2018
---
# <a name="manage-an-azure-ssis-integration-runtime"></a>管理 Azure-SSIS 集成运行时
[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)一文介绍了如何使用 Azure 数据工厂创建 Azure-SSIS 集成运行时 (IR)。 本文提供了有关重新配置现有 Azure-SSIS 集成运行时的信息。  

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [数据工厂版本 1 文档](v1/data-factory-introduction.md)。

预配并启动 Azure-SSIS 集成运行时的实例后，可以通过连续运行 `Stop` - `Set` - `Start` PowerShell cmdlet 序列来重新配置该实例。 例如，以下 PowerShell 脚本将分配给 Azure-SSIS 集成运行时实例的节点数更改为 5。

## <a name="reconfigure-an-azure-ssis-ir"></a>重新配置 Azure-SSIS IR

1. 首先，使用 [Stop-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/stop-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet 停止 Azure-SSIS 集成运行时。 此命令释放该运行时的所有节点并停止计费。

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 接下来，使用 [Set-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet 重新配置 Azure-SSIS IR。 以下示例命令将 Azure-SSIS 集成运行时横向扩展到五个节点。

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. 然后，使用 [Start-AzureRmDataFactoryV2IntegrationRuntime](/powershell/module/azurerm.datafactoryv2/start-azurermdatafactoryv2integrationruntime?view=azurermps-4.4.1) cmdlet 启动 Azure-SSIS 集成运行时。 此命令分配该运行时的所有节点来运行 SSIS 包。   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="delete-an-azure-ssis-ir"></a>删除 Azure-SSIS IR
1. 首先，列出数据工厂下的所有现有 Azure SSIS IR。

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. 接下来，停止数据工厂中的所有现有 Azure SSIS IR。

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. 接下来，逐个删除数据工厂中的所有现有 Azure SSIS IR。

    ```powershell
    Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. 最后，删除数据工厂。

    ```powershell
    Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. 如果已创建了新的资源组，请删除该资源组。

    ```powershell
    Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>后续步骤
有关 Azure-SSIS 运行时的详细信息，请参阅以下主题： 

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-deploy-ssis-packages-azure.md)。 此文提供有关创建 Azure-SSIS IR，并使用 Azure SQL 数据库来承载 SSIS 目录的分步说明。 
- [如何创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 此文延伸了教程的内容，提供有关使用 Azure SQL 托管实例（人预览版）以及将 IR 加入 VNet 的说明。 
- [将 Azure-SSIS IR 加入 VNet](join-azure-ssis-integration-runtime-virtual-network.md)。 此文提供有关将 Azure-SSIS IR 加入 Azure 虚拟网络 (VNet) 的概念性信息。 此外，介绍可以执行哪些步骤来使用 Azure 门户配置 VNet，以便 Azure-SSIS IR 能够加入 VNet。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。 
 
