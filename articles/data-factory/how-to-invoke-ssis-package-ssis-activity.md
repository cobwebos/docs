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
ms.date: 03/19/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7287dc2fccf461cf23c45202336e3d92bc5a40aa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153072"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用“执行 SSIS 包”活动运行 SSIS 包
本文介绍如何使用“执行 SSIS 包”活动在 Azure 数据工厂 (ADF) 管道中运行 SSIS 包。 

## <a name="prerequisites"></a>必备组件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果还没有 Azure-SSIS Integration Runtime (IR)，请按照[教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md) 中的分步说明创建一个。

## <a name="run-a-package-in-the-azure-portal"></a>在 Azure 门户中运行包
在本部分中，将使用 ADF 用户界面 (UI)/应用创建一个 ADF 管道，管道中包含运行 SSIS 包的“执行 SSIS 包”活动。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用“执行 SSIS 包”活动创建管道
在此步骤中，将使用 ADF UI/应用创建管道。 将“执行 SSIS 包”活动添加到管道，并将该活动配置为运行 SSIS 包。 

1. 在 Azure 门户中的 ADF 概述/主页上，单击“创建者和监视器”磁贴，在单独的选项卡中启动 ADF UI/应用  。 

   ![数据工厂主页](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   在“开始使用”页中，单击“创建管道”   ： 

   ![“入门”页](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. 在“活动”工具箱中展开“常规”，然后将“执行 SSIS 包”活动拖放到管道设计面    。 

   ![将“执行 SSIS 包”活动拖到设计面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. 在“执行 SSIS 包”活动的“常规”选项卡上，提供活动的名称和说明  。 设置可选的超时和重试值。

   ![在“常规”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. 在“执行 SSIS 包”活动的“设置”选项卡上，选择与部署包的 SSISDB 数据库关联的 Azure-SSIS IR  。 如果您的包使用 Windows 身份验证来访问数据存储，例如将 SQL 服务器/文件共享在本地，Azure 文件，等等，检查**Windows 身份验证**复选框并输入域/用户名/密码对包执行。 如果包需要 32 位运行时才能运行，请选中“32 位运行时”复选框  。 对于“日志记录级别”，请为包执行选择预定义的日志记录范围  。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框  。 如果 Azure-SSIS IR 正在运行且未选中“手动输入内容”复选框，可以从 SSISDB 浏览并选择现有的文件夹/项目/包/环境  。 单击“刷新”按钮可从 SSISDB 获取新添加的文件夹/项目/包/环境，以便可以进行浏览和选择  。 

   ![在“设置”选项卡上设置属性 - 自动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   将 Azure SSIS IR 未运行时或**手动条目**选中复选框，可以从 SSISDB 中输入包和环境路径直接采用以下格式：`<folder name>/<project name>/<package name>.dtsx`和`<folder name>/<environment name>`。

   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. 在“执行 SSIS 包”活动的“SSIS 参数”选项卡上，如果 Azure-SSIS IR 正在运行且未选中“设置”选项卡上的“手动输入内容”复选框，则会显示 SSISDB 中选定项目/包中现有的 SSIS 参数，以便为它们赋值    。 否则，可以逐个输入以便手动为它们赋值 - 为了使包成功执行，请确保它们存在并已正确输入。 您可以向使用表达式、 函数、 ADF 系统变量和 ADF 管道参数/变量及其值添加动态内容。 或者，可以使用作为其值存储在 Azure Key Vault (AKV) 中的机密。 为此，请单击**AZURE 密钥保管库**相关参数旁边的复选框选择/编辑现有链接的 AKV 服务或创建一个新的活动，并选择机密名称/版本的参数值。  在您创建/编辑 AKV 链接服务，可以选择/编辑现有 AKV 或创建一个新，但请授予对你 AKV ADF 托管标识访问权限，如果你尚未这样做已经。 您还可以直接采用以下格式输入机密： `<AKV linked service name>/<secret name>/<secret version>`。

   ![在“SSIS 参数”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. 上**连接管理器**执行 SSIS 包活动中，如果 Azure SSIS IR 正在运行的选项卡和**手动条目**上的复选框**设置**选项卡处于未选中状态，在所选项目/包从 SSISDB 中的现有连接管理器将显示为您要将值分配给它们的属性。 否则，你可以输入它们通过另一个用于将值手动分配给它们的属性-请确保它们存在并且正确输入为包执行成功。 可以向使用表达式、 函数、 ADF 系统变量和 ADF 管道参数/变量及其属性值来添加动态内容。 或者，可以使用作为其属性值存储在 Azure Key Vault (AKV) 中的机密。 为此，请单击**AZURE 密钥保管库**相关属性旁边的复选框选择/编辑现有链接的 AKV 服务或创建一个新，然后选择机密属性值的名称/版本。  在您创建/编辑 AKV 链接服务，可以选择/编辑现有 AKV 或创建一个新，但请授予对你 AKV ADF 托管标识访问权限，如果你尚未这样做已经。 您还可以直接采用以下格式输入机密： `<AKV linked service name>/<secret name>/<secret version>`。

   ![在“连接管理器”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. 在“执行 SSIS 包”活动的“属性替代”选项卡上，可以逐个输入 SSISDB 中选定包的现有属性的路径，以便手动为其赋值 - 为了使包成功执行，请确保它们存在并已正确输入，例如，若要替代用户变量的值，请按以下格式输入其路径：`\Package.Variables[User::YourVariableName].Value`  。 还可以使用表达式、函数、ADF 系统变量和 ADF 管道参数/变量将动态内容添加到其值中。

   ![在“属性替代”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. 若要验证管道配置，请单击工具栏中的“验证”  。 若要关闭“管道验证报告”，  请单击 **>>** 。

9. 单击“全部发布”按钮，将管道发布到 ADF  。 

### <a name="run-the-pipeline"></a>运行管道
在此步骤中，将触发管道运行。 

1. 若要触发某个管道运行，请在工具栏中单击“触发器”  ，然后单击“立即触发”  。 

   ![立即触发](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在“管道运行”窗口中选择“完成”。   

### <a name="monitor-the-pipeline"></a>监视管道

1. 在左侧切换到“监视”选项卡。  随即显示管道运行及其状态以及其他信息（比如运行开始时间）。 若要刷新视图，请单击“刷新”  。

   ![管道运行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. 在“操作”  列中单击“查看活动运行”  链接。 此时只显示一个活动运行，因为管道只有一个活动（“执行 SSIS 包”活动）。

   ![活动运行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 可在 Azure SQL Server 中针对 SSISDB 数据库运行以下**查询**，验证是否执行了该包。 

   ```sql
   select * from catalog.executions
   ```

   ![验证包执行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 还可以从管道活动运行的输出中获取 SSISDB 执行 ID，并使用此 ID 在 SSMS 中查看更全面的执行日志和错误消息。

   ![获取执行 ID。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用触发器计划管道

也可以为管道创建一个计划触发器，以便按计划（每小时、每天等）运行管道。 有关示例，请参阅[创建数据工厂 - 数据工厂 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="run-a-package-with-powershell"></a>使用 PowerShell 运行包
在此部分中，将使用 Azure PowerShell 创建一个 ADF 管道，管道中包含运行 SSIS 包的“执行 SSIS 包”活动。 

按照[如何安装和配置 Azure PowerShell](/powershell/azure/install-az-ps) 中的分步说明安装最新的 Azure PowerShell 模块。

### <a name="create-an-adf-with-azure-ssis-ir"></a>创建包含 Azure-SSIS IR 的 ADF
可以使用已预配 Azure-SSIS IR 的现有 ADF，也可以按照[教程：通过 PowerShell 将 SSIS 包部署到 Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell) 中的分步说明，创建包含 Azure-SSIS IR 的新 ADF。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用“执行 SSIS 包”活动创建管道 
在此步骤中创建包含“执行 SSIS 包”活动的管道。 该活动运行 SSIS 包。 

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
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
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
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
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

2. 在 Azure PowerShell 中，切换到 `C:\ADF\RunSSISPackage` 文件夹。

3. 若要创建管道**RunSSISPackagePipeline**，请运行**集 AzDataFactoryV2Pipeline** cmdlet。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
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

### <a name="run-the-pipeline"></a>运行管道
使用**Invoke AzDataFactoryV2Pipeline** cmdlet 运行管道。 此 cmdlet 返回管道运行 ID，用于将来的监视。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>监视管道

运行以下 PowerShell 脚本，持续检查管道运行状态，直到完成数据复制为止。 在 PowerShell 窗口中复制/粘贴以下脚本，然后按 ENTER。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
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

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用触发器计划管道
在上一步骤中，已按需运行了管道。 还可创建一个计划触发器，按计划（按小时、每天等）运行管道。

1. 在 C:\ADF\RunSSISPackage 文件夹中创建一个名为 MyTrigger.json 的 JSON 文件，并在其中包含以下内容   ： 

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
           }]
       }
   }    
   ```
2. 在 Azure PowerShell 中，切换到 C:\ADF\RunSSISPackage 文件夹   。
3. 运行**集 AzDataFactoryV2Trigger** cmdlet，后者创建触发器。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. 默认情况下，触发器处于停止状态。 通过运行启动触发器**开始 AzDataFactoryV2Trigger** cmdlet。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. 确认已通过运行启动触发器**Get AzDataFactoryV2Trigger** cmdlet。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. 在下一小时后运行以下命令。 例如，如果当前时间为下午 3:25 (UTC)，则在下午 4:00 (UTC) 运行该命令。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
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
