---
title: 重新配置 Azure-SSIS 集成运行时 | Microsoft Docs
description: 了解在预配 Azure-SSIS 集成运行时之后，如何在 Azure 数据工厂中对其进行重新配置。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454929"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>重新配置 Azure-SSIS 集成运行时
本文介绍如何重新配置现在的 Azure-SSIS 集成运行时。 若要在 Azure 数据工厂中创建 Azure-SSIS 集成运行时 (IR)，请参阅[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。  

## <a name="data-factory-ui"></a>数据工厂 UI 
可以使用数据工厂 UI 停止、编辑/重新配置或删除 Azure-SSIS IR。 

1. 在**数据工厂 UI** 中，切换到“编辑”选项卡。若要启动“数据工厂 UI”，请单击数据工厂主页上的“创作和监视”。
2. 在左窗格中，单击“连接”。
3. 在右窗格中，切换到“集成运行时”。 
4. 可以使用“操作”列中的按钮**停止**、**编辑**或**删除**集成运行时。 使用“操作”列中的“代码”按钮，可以查看与集成运行时关联的 JSON 定义。  
    
    ![Azure SSIS IR 的操作](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>重新配置 Azure-SSIS IR
1. 通过单击“操作”列中的“停止”停止集成运行时。 若要刷新列表视图，请单击工具栏上的“刷新”。 IR 停止后，将看到第一个操作让你启动 IR。 

    ![Azure SSIS IR 的操作 - 停止后](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. 通过单击“操作”列中的“编辑”按钮编辑/重新配置 IR。 在“集成运行时设置”窗口中，更改设置（例如，节点大小、节点数或每个节点的最大并行执行数）。 
3. 若要重新启动 IR，请单击“操作”列中的“启动”按钮。     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

预配并启动 Azure-SSIS 集成运行时的实例后，可以通过连续运行 `Stop` - `Set` - `Start` PowerShell cmdlet 序列来重新配置该实例。 例如，以下 PowerShell 脚本将分配给 Azure-SSIS 集成运行时实例的节点数更改为 5。

### <a name="reconfigure-an-azure-ssis-ir"></a>重新配置 Azure-SSIS IR

1. 首先，使用停止 Azure SSIS 集成运行时[停止 AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) cmdlet。 此命令释放该运行时的所有节点并停止计费。

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 接下来，通过使用来重新配置 AZURE-SSIS IR[集 AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet。 以下示例命令将 Azure-SSIS 集成运行时横向扩展到五个节点。

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. 然后，使用启动 Azure SSIS 集成运行时[开始 AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet。 此命令分配该运行时的所有节点来运行 SSIS 包。   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>删除 Azure-SSIS IR
1. 首先，列出数据工厂下的所有现有 Azure SSIS IR。

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. 接下来，停止数据工厂中的所有现有 Azure SSIS IR。

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. 接下来，逐个删除数据工厂中的所有现有 Azure SSIS IR。

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. 最后，删除数据工厂。

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. 如果已创建了新的资源组，请删除该资源组。

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>后续步骤
有关 Azure-SSIS 运行时的详细信息，请参阅以下主题： 

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 此文提供有关创建 Azure-SSIS IR，并使用 Azure SQL 数据库来承载 SSIS 目录的分步说明。 
- [如何：创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 本文是教程的拓展延伸，介绍了如何使用 Azure SQL 数据库托管实例以及如何将 IR 加入虚拟网络。 
- [将 Azure-SSIS IR 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。 此文提供有关将 Azure-SSIS IR 加入 Azure 虚拟网络的概念性信息。 此外，还介绍可以执行哪些步骤来使用 Azure 门户配置虚拟网络，以便 Azure-SSIS IR 能够加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。 
 
