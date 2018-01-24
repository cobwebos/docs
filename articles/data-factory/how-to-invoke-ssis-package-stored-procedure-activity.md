---
title: "使用 Azure 数据工厂调用 SSIS 包 - 存储过程活动 | Microsoft Docs"
description: "本文介绍如何使用存储过程活动从 Azure 数据工厂管道调用 SQL Server Integration Services (SSIS) 包。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: 713e9ad7a76c15cbde912954e00991a80b995683
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用存储过程活动调用 SSIS 包
本文介绍如何使用存储过程活动从 Azure 数据工厂管道中调用 SSIS 包。 

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（正式版 (GA)），请参阅[在版本 1 中使用存储过程活动调用 SSIS 包](v1/how-to-invoke-ssis-package-stored-procedure-activity.md)。

## <a name="prerequisites"></a>先决条件

### <a name="azure-sql-database"></a>Azure SQL 数据库 
本文中的演练使用托管 SSIS 目录的 Azure SQL 数据库。 还可使用 Azure SQL 托管实例（个人预览版）。

## <a name="create-an-azure-ssis-integration-runtime"></a>创建 Azure-SSIS 集成运行时
如果还没有 Azure-SSIS 集成运行时，请按照[教程：部署 SSIS 包](tutorial-deploy-ssis-packages-azure.md)中的分步说明创建一个。

### <a name="azure-powershell"></a>Azure PowerShell
按[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明安装最新的 Azure PowerShell 模块。 

## <a name="create-a-data-factory"></a>创建数据工厂
可使用具有 Azure-SSIS IR 的相同数据工厂，也可以创建单独的数据工厂。 下列过程提供创建数据工厂的步骤。 可在数据工厂中使用存储过程活动创建管道。 存储过程活动在 SSISDB 数据库中执行存储过程，运行 SSIS 包。 

1. 为资源组名称定义一个变量，稍后会在 PowerShell 命令中使用该变量。 将以下命令文本复制到 PowerShell，在双引号中指定 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称，然后运行命令。 例如：`"adfrg"`。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    如果该资源组已存在，请勿覆盖它。 为 `$ResourceGroupName` 变量分配另一个值，然后再次运行命令
2. 若要创建 Azure 资源组，请运行以下命令： 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    如果该资源组已存在，请勿覆盖它。 为 `$ResourceGroupName` 变量分配另一个值，然后再次运行命令。 
3. 定义一个用于数据工厂名称的变量。 

    > [!IMPORTANT]
    >  更新数据工厂名称，使之全局唯一。 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. 若要创建数据工厂，请运行下面的 **Set-AzureRmDataFactoryV2** cmdlet，使用 $ResGrp 变量中的 Location 和 ResourceGroupName 属性： 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* 若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于**参与者**或**所有者**角色，或者是 Azure 订阅的**管理员**。
* 目前，数据工厂版本 2 仅允许在“美国东部”、“美国东部 2”和“西欧”区域创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

### <a name="create-an-azure-sql-database-linked-service"></a>创建 Azure SQL 数据库链接服务
创建一个链接服务，将托管 SSIS 目录的 Azure SQL 数据库链接到数据工厂。 数据工厂使用此链接服务中的信息连接到 SSISDB 数据库，并执行存储过程来运行 SSIS 包。 

1. 在 C:\ADF\RunSSISPackage 文件夹中创建一个名为 AzureSqlDatabaseLinkedService.json 的 JSON 文件，并在其中包含以下内容： 

    > [!IMPORTANT]
    > 保存文件之前，请将 &lt;servername&gt;、&lt;username&gt; 和 &lt;password&gt; 替换为 Azure SQL 数据库的值。

    ```json
    {
        "name": "AzureSqlDbLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. 在 Azure PowerShell 中，切换到 C:\ADF\RunSSISPackage 文件夹。

3. 运行 **Set-AzureRmDataFactoryV2LinkedService** cmdlet 创建链接服务：**AzureSqlDatabaseLinkedService**。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>使用存储过程活动创建管道 
在此步骤中，使用存储过程活动创建管道。 该活动调用 sp_executesql 存储过程来运行 SSIS 包。 

1. 在 C:\ADF\RunSSISPackage 文件夹中创建一个名为 RunSSISPackagePipeline.json 的 JSON 文件，并在其中包含以下内容：

    > [!IMPORTANT]
    > 保存文件之前，请将 &lt;FOLDER NAME&gt;、&lt;PROJECT NAME&gt; 和 &lt;PACKAGE NAME&gt; 替换为 SSIS 目录中文件夹、项目和包的名称。 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDbLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. 要创建管道 RunSSISPackagePipeline，请运行 Set-AzureRmDataFactoryV2Pipeline cmdlet。

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    下面是示例输出：

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>创建管道运行
使用 Invoke-AzureRmDataFactoryV2Pipeline cmdlet 运行管道。 此 cmdlet 返回管道运行 ID，用于将来的监视。

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

## <a name="monitor-the-pipeline-run"></a>监视管道运行

运行以下 PowerShell 脚本，持续检查管道运行状态，直到完成数据复制为止。 在 PowerShell 窗口中复制/粘贴以下脚本，然后按 ENTER。 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

## <a name="create-a-trigger"></a>创建触发器
在上一步中，可按需调用管道。 还可创建一个计划触发器，按计划（按小时、每天等）运行管道。

1. 在 C:\ADF\RunSSISPackage 文件夹中创建一个名为 MyTrigger.json 的 JSON 文件，并在其中包含以下内容： 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. 在 Azure PowerShell 中，切换到 C:\ADF\RunSSISPackage 文件夹。
3. 运行 Set-AzureRmDataFactoryV2Trigger cmdlet，创建触发器。 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. 默认情况下，触发器处于停止状态。 运行 Start-AzureRmDataFactoryV2Trigger cmdlet，启动触发器。 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. 运行 Get-AzureRmDataFactoryV2Trigger cmdlet，确认已启动触发器。 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. 在下一小时后运行以下命令。 例如，如果当前时间为下午 3:25 (UTC)，则在下午 4:00 (UTC) 运行该命令。 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    可在 Azure SQL 服务器中针对 SSISDB 数据库运行以下查询，验证是否执行了该包。 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>后续步骤
还可使用 Azure 门户监视管道。 有关分步说明，请参阅[监视管道](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)。
