---
title: 使用“执行 SSIS 包”活动运行 SSIS 包 - Azure | Microsoft Docs
description: 本文介绍如何使用“执行 SSIS 包”活动在 Azure 数据工厂管道中运行 SQL Server Integration Services (SSIS) 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: db5941528eedd10cf252607dbe2160bd498a70de
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951961"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用“执行 SSIS 包”活动运行 SSIS 包
本文介绍如何使用“执行 SSIS 包”活动在 Azure 数据工厂管道中运行 SSIS 包。 

## <a name="prerequisites"></a>先决条件

### <a name="azure-sql-database"></a>Azure SQL 数据库 
本文中的演练使用托管 SSIS 目录的 Azure SQL 数据库。 还可使用 Azure SQL 托管实例（预览版）。

## <a name="create-an-azure-ssis-integration-runtime"></a>创建 Azure-SSIS 集成运行时
如果还没有 Azure-SSIS 集成运行时，请按照[教程：部署 SSIS 包](tutorial-create-azure-ssis-runtime-portal.md)中的分步说明创建一个。

## <a name="data-factory-ui-azure-portal"></a>数据工厂 UI（Azure 门户）
在本部分中，我们将使用数据工厂 UI 来创建一个数据工厂管道，其中包含可运行 SSIS 包的“执行 SSIS 包”活动。

### <a name="create-a-data-factory"></a>创建数据工厂
第一步是使用 Azure 门户创建数据工厂。 

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 导航到 [Azure 门户](https://portal.azure.com)。 
3. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果看到名称字段的以下错误，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
     ![名称不可用 - 错误](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
    若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
4. 选择“V2”作为“版本”。
5. 选择数据工厂的**位置**。 下拉列表中仅显示数据工厂支持的位置。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库等）和计算资源（HDInsight 等）可以位于其他位置。
6. 选择“固定到仪表板”。     
7. 单击“创建”。
8. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. 创建完成后，可以看到图中所示的“数据工厂”页。
   
    ![数据工厂主页](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂用户界面 (UI) 应用程序。 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用“执行 SSIS 包”活动创建管道
在此步骤中，将使用数据工厂 UI 创建管道。 将“执行 SSIS 包”活动添加到管道，并将该活动配置为运行 SSIS 包。 

1. 在“入门”页中，单击“创建管道”： 

    ![“入门”页](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. 在“活动”工具箱中展开“常规”，将“执行 SSIS 包”活动拖放到管道设计器图面。 

   ![将 SSIS 活动拖到设计器图面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. 在“执行 SSIS 包”活动属性的“常规”选项卡上，提供活动的名称和说明。 设置可选的超时和重试值。

    ![在“常规”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. 在“执行 SSIS 包”活动属性的“设置”选项卡上，选择与包部署到的 `SSISDB` 数据库关联的 Azure-SSIS 集成运行时。 以 `<folder name>/<project name>/<package name>.dtsx` 格式提供包在 `SSISDB` 数据库中的路径。 （可选）指定 32 位执行和预定义或自定义的日志记录级别，并以 `<folder name>/<environment name>` 格式提供环境路径。

    ![在“设置”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. 若要验证管道配置，请单击工具栏中的“验证”。 若要关闭“管道验证报告”，请单击 **>>**。

6. 单击“全部发布”按钮将管道发布到数据工厂。 

### <a name="optionally-parameterize-the-activity"></a>（可选）将活动参数化

（可选）使用“执行 SSIS 包”活动框底部的“查看源代码”按钮，或管道区域右上角的“代码”按钮，将可以引用数据工厂系统变量的值、表达式或函数分配给 JSON 格式的项目或包参数。 例如，可以将数据工厂管道参数分配给 SSIS 项目或包参数，如以下屏幕截图所示：

![编辑“执行 SSIS 包”活动的 JSON 脚本](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

![将参数添加到“执行 SSIS 包”活动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

### <a name="run-and-monitor-the-pipeline"></a>运行和监视管道
在此部分中，将触发管道运行，然后对其进行监视。 

1. 若要触发某个管道运行，请在工具栏中单击“触发器”，然后单击“立即触发”。 

    ![立即触发](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在“管道运行”窗口中选择“完成”。 

3. 在左侧切换到“监视”选项卡。 随即显示管道运行及其状态以及其他信息（比如运行开始时间）。 若要刷新视图，请单击“刷新”。

    ![管道运行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. 在“操作”列中单击“查看活动运行”链接。 此时只显示一个活动运行，因为管道只有一个活动（“执行 SSIS 包”活动）。

    ![活动运行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. 可在 Azure SQL Server 中针对 SSISDB 数据库运行以下**查询**，验证是否执行了该包。 

    ```sql
    select * from catalog.executions
    ```

    ![验证包执行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. 还可以从管道活动运行的输出中获取 SSISDB 执行 ID，并使用此 ID 在 SSMS 中查看更全面的执行日志和错误消息。

    ![获取执行 ID。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> 也可以为管道创建一个计划触发器，以便按计划（每小时、每天等）运行管道。 有关示例，请参阅[创建数据工厂 - 数据工厂 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="azure-powershell"></a>Azure PowerShell
在本部分，我们将使用 Azure PowerShell 来创建一个数据工厂管道，其中包含可运行 SSIS 包的 SSIS 活动。 

按[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明安装最新的 Azure PowerShell 模块。 

### <a name="create-a-data-factory"></a>创建数据工厂
可使用具有 Azure-SSIS IR 的相同数据工厂，也可以创建单独的数据工厂。 下列过程提供创建数据工厂的步骤。 在此数据工厂中创建包含 SSIS 活动的管道。 该 SSIS 活动运行 SSIS 包。 

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
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

请注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* 若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于**参与者**或**所有者**角色，或者是 Azure 订阅的**管理员**。
* 要查看目前提供数据工厂的 Azure 区域的列表，请在以下页面上选择感兴趣的区域，然后展开“分析”以找到“数据工厂”：[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。

### <a name="create-a-pipeline-with-an-ssis-activity"></a>创建包含 SSIS 活动的管道。 
本步骤创建包含 SSIS 活动的管道。 该活动运行 SSIS 包。 

1. 在 **C:\ADF\RunSSISPackage** 文件夹中创建名为 **RunSSISPackagePipeline.json** 的 JSON 文件，并在其中包含类似于以下示例的内容：

    > [!IMPORTANT]
    > 在保存该文件之前，请替换对象名称、说明、路径、属性和参数值、密码及其他变量值。 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  在 Azure PowerShell 中，切换到 `C:\ADF\RunSSISPackage` 文件夹。

3. 若要创建管道 **RunSSISPackagePipeline**，请运行 **Set-AzureRmDataFactoryV2Pipeline** cmdlet。

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    下面是示例输出：

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>创建管道运行
使用 Invoke-AzureRmDataFactoryV2Pipeline cmdlet 运行管道。 此 cmdlet 返回管道运行 ID，用于将来的监视。

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>监视管道运行

运行以下 PowerShell 脚本，持续检查管道运行状态，直到完成数据复制为止。 在 PowerShell 窗口中复制/粘贴以下脚本，然后按 ENTER。 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

还可使用 Azure 门户监视管道。 有关分步说明，请参阅[监视管道](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)。

### <a name="create-a-trigger"></a>创建触发器
在上一步骤中，已按需运行了管道。 还可创建一个计划触发器，按计划（按小时、每天等）运行管道。

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
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. 默认情况下，触发器处于停止状态。 运行 Start-AzureRmDataFactoryV2Trigger cmdlet，启动触发器。 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. 运行 Get-AzureRmDataFactoryV2Trigger cmdlet，确认已启动触发器。 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. 在下一小时后运行以下命令。 例如，如果当前时间为下午 3:25 (UTC)，则在下午 4:00 (UTC) 运行该命令。 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    可在 Azure SQL 服务器中针对 SSISDB 数据库运行以下查询，验证是否执行了该包。 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>后续步骤
请参阅以下博客文章：
-   [使用 ADF 管道中的 SSIS 活动来实现 ETL/ELT 工作流的现代化并对其进行扩展](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
