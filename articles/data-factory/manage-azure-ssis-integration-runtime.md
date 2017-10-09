---
title: "管理 Azure-SSIS 集成运行时 | Microsoft Docs"
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
ms.date: 09/25/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 780e5673b5ed159a7f170373d54fea7c1713a910
ms.contentlocale: zh-cn
ms.lasthandoff: 09/28/2017

---

# <a name="manage-an-azure-ssis-integration-runtime"></a>管理 Azure-SSIS 集成运行时
[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)一文介绍了如何使用 Azure 数据工厂创建 Azure-SSIS 集成运行时。 本文对上述文章做了补充，提供有关如何停止、启动、重新配置或删除 Azure-SSIS 集成运行时的信息。  


## <a name="stop"></a>停止 
停止 Azure-SSIS 集成运行时。 此命令释放该运行时的所有节点并停止计费。

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
```

## <a name="start"></a>开始 
启动 Azure-SSIS 集成运行时。 此命令分配该运行时的所有节点并开始计费。   

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
```

## <a name="reconfigure"></a>重新配置
预配并启动 Azure-SSIS 集成运行时的实例后，可以通过连续运行 `Stop` - `Set` - `Start` PowerShell cmdlet 序列来重新配置该实例。 例如，以下 PowerShell 脚本将分配给 Azure-SSIS 集成运行时实例的节点数更改为 5。

1. 首先，运行以下命令停止 Azure-SSIS 集成运行时：

    ```powershell
    Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. 接下来，将 Azure-SSIS 集成运行时扩展为五个节点。

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. 然后，启动 Azure-SSIS 集成运行时。 这样，便可以分配该运行时的所有节点来运行 SSIS 包。   

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

## <a name="remove"></a>删除
若要删除现有的 Azure-SSIS 集成运行时，请运行以下命令： 

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
```

## <a name="next-steps"></a>后续步骤
有关 Azure-SSIS 运行时的详细信息，请参阅以下主题： 

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-deploy-ssis-packages-azure.md)。 此文提供有关创建 Azure-SSIS IR，并使用 Azure SQL 数据库来承载 SSIS 目录的分步说明。 
- [如何创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 此文延伸了教程的内容，提供有关使用 Azure SQL 托管实例（人预览版）以及将 IR 加入 VNet 的说明。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。 
- [将 Azure-SSIS IR 加入 VNet](join-azure-ssis-integration-runtime-virtual-network.md)。 此文提供有关将 Azure-SSIS IR 加入 Azure 虚拟网络 (VNet) 的概念性信息。 此外，介绍可以执行哪些步骤来使用 Azure 门户配置 VNet，以便 Azure-SSIS IR 能够加入 VNet。 

