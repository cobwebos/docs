---
title: 重新配置 Azure-SSIS 集成运行时
description: 了解在预配 Azure-SSIS 集成运行时之后，如何在 Azure 数据工厂中对其进行重新配置。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: f609cfb0945d79cfa8ae21b786a5761b92b9dabb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84324617"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>重新配置 Azure-SSIS 集成运行时

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何重新配置现在的 Azure-SSIS 集成运行时。 若要在 Azure 数据工厂中创建 Azure-SSIS 集成运行时 (IR)，请参阅[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。  

## <a name="data-factory-ui"></a>数据工厂 UI 
可以使用数据工厂 UI 停止、编辑/重新配置或删除 Azure-SSIS IR。 

1. 通过选择数据工厂主页上的“创作和监视”磁贴，打开数据工厂 UI。
2. 选择“主页”、“编辑”和“监视”中心下方的“管理”中心，以显示“连接”窗格。

### <a name="to-reconfigure-an-azure-ssis-ir"></a>重新配置 Azure-SSIS IR
在“管理”中心的“连接”窗格中，切换到“集成运行时”页，然后选择“刷新”   。 

   ![“连接”窗格](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   可以通过选择 Azure-SSIS IR 的名称来对其进行编辑/重新配置。 此外，还可以选择相应的按钮来监视/启动/停止/删除 Azure-SSIS IR，通过“执行 SSIS 包”活动自动生成 ADF 管道以在 Azure-SSIS IR 上运行，并查看 Azure-SSIS IR 的 JSON 代码/有效负载。  只能在 Azure-SSIS IR 停止时对其执行编辑/删除操作。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

预配并启动 Azure-SSIS 集成运行时的实例后，可以通过连续运行 `Stop` - `Set` - `Start` PowerShell cmdlet 序列来重新配置该实例。 例如，以下 PowerShell 脚本将分配给 Azure-SSIS 集成运行时实例的节点数更改为 5。

### <a name="reconfigure-an-azure-ssis-ir"></a>重新配置 Azure-SSIS IR

1. 首先，使用 [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) cmdlet 停止 Azure-SSIS Integration Runtime。 此命令释放该运行时的所有节点并停止计费。

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. 接下来，使用 [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet 重新配置 Azure-SSIS IR。 以下示例命令将 Azure-SSIS 集成运行时横向扩展到五个节点。

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. 然后，使用 [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet 启动 Azure-SSIS Integration Runtime。 此命令分配该运行时的所有节点来运行 SSIS 包。   

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
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 此文以分步说明的方式介绍了如何创建 Azure-SSIS IR 并使用 Azure SQL 数据库来托管 SSIS 目录。 
- [如何：创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 此文延伸了本教程的内容，介绍了如何使用 Azure SQL 托管实例以及如何将 IR 加入虚拟网络。 
- [将 Azure-SSIS IR 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。 此文提供有关将 Azure-SSIS IR 加入 Azure 虚拟网络的概念性信息。 此外，还介绍可以执行哪些步骤来使用 Azure 门户配置虚拟网络，以便 Azure-SSIS IR 能够加入虚拟网络。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。
