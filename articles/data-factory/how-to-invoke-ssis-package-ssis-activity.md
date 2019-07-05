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
ms.date: 07/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33259fff17633cc4864a342609f747ebb9902ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484835"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用“执行 SSIS 包”活动运行 SSIS 包
本文介绍如何通过使用执行 SSIS 包活动在 Azure 数据工厂 (ADF) 管道中运行 SQL Server Integration Services (SSIS) 包。 

## <a name="prerequisites"></a>必备组件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果还没有 Azure-SSIS Integration Runtime (IR)，请按照[教程：预配 Azure SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)。

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

4. 上**设置**执行 SSIS 包活动选项卡上，选择你想要运行包的 AZURE-SSIS IR。 如果您的包使用 Windows 身份验证来访问数据存储，例如将 SQL 服务器/文件共享在本地，Azure 文件，等等，检查**Windows 身份验证**复选框，输入你的包执行凭据值(**域**/**用户名**/**密码**)。 或者，可以使用作为其值存储在 Azure Key Vault (AKV) 中的机密。 为此，请单击**AZURE 密钥保管库**相关的凭据，旁边的复选框选择/编辑现有链接的 AKV 服务或创建一个新，然后选择你的凭据值机密名称/版本。  在您创建/编辑 AKV 链接服务，可以选择/编辑现有 AKV 或创建一个新，但请授予对你 AKV ADF 托管标识访问权限，如果你尚未这样做已经。 您还可以直接采用以下格式输入机密： `<AKV linked service name>/<secret name>/<secret version>`。 如果包需要 32 位运行时才能运行，请选中“32 位运行时”复选框  。 

   有关**包位置**，选择**SSISDB**，**文件系统 （包）** ，或者**文件系统 （项目）** 。 如果选择**SSISDB**作为你的包位置，如果，则会自动选择将 Azure SSIS IR 的预配时由 Azure SQL 数据库服务器/托管实例承载 SSIS 目录 (SSISDB) 使用，你需要指定您的包若要运行的已部署到 SSISDB。 如果 Azure SSIS IR 正在运行并且**手动输入**复选框处于未选中状态，则可以浏览并选择从 SSISDB 的现有文件夹/项目/包/环境。 单击“刷新”按钮可从 SSISDB 获取新添加的文件夹/项目/包/环境，以便可以进行浏览和选择  。 
   
   对于“日志记录级别”，请为包执行选择预定义的日志记录范围  。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框  。 

   ![在“设置”选项卡上设置属性 - 自动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   如果将 Azure SSIS IR 未运行或**手动条目**选中复选框，可以从 SSISDB 中输入包和环境路径直接采用以下格式：`<folder name>/<project name>/<package name>.dtsx`和`<folder name>/<environment name>`。

   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   如果选择**文件系统 （包）** 作为你的包位置，如果，则自动选择将 Azure SSIS IR SSISDB 的情况下设置，你需要指定要运行通过提供一个通用命名约定 （在包为包文件的 UNC) 路径 (`.dtsx`) 中**包路径**。 例如，如果您的包存储在 Azure 文件中，其包路径将为`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`。 
   
   如果在一个单独的文件中配置你的包，还需要提供你的配置文件的 UNC 路径 (`.dtsConfig`) 中**配置路径**。 例如，如果你的配置存储在 Azure 文件中，其配置路径将为`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`。

   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   如果选择**文件系统 （项目）** 作为你的包位置，你需要指定您的程序包以通过提供你的项目文件的 UNC 路径运行 (`.ispac`) 中**项目路径**和包文件 （`.dtsx`) 中的项目中从**包名称**。 例如，如果将项目存储在 Azure 文件时，其项目路径将为`\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`。

   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   接下来，需要指定用于访问你的项目/包/配置文件的凭据。 如果您以前为包执行凭据 （见上文） 输入值，则可以通过检查重用它们**与包执行凭据相同**复选框。 否则，你需要输入你的包访问凭据的值 (**域**/**用户名**/**密码**)。 例如，如果将您的项目/包/配置存储在 Azure 文件**域**是`Azure`;**用户名**是`<storage account name>`; 和**密码**是`<storage account key>`。 或者，可以使用机密存储在 AKV 中为它们的值 （见上文）。 这些凭据将用于访问你的包和在执行包任务中，从他们自己路径 / 同一个项目，以及配置，包括您的包中指定的所有子包。 
   
   如果您使用过**EncryptAllWithPassword**/**EncryptSensitiveWithPassword**保护级别创建 SQL Server Data Tools (SSDT) 通过您的包时，您需要输入的值在你的密码**加密密码**。 或者，可以使用存储在 AKV 中作为其值 （见上文） 密钥。 如果您使用过**EncryptSensitiveWithUserKey**保护级别，您需要重新输入你的敏感值，在配置文件中或在**SSIS 参数**/ **连接管理器**/**属性重写**选项卡 （见下文）。 如果您使用过**EncryptAllWithUserKey**保护级别，它是不受支持，因此需要重新配置您的程序包以使用其他保护级别通过 SSDT 或`dtutil`命令行实用程序。 
   
   对于“日志记录级别”，请为包执行选择预定义的日志记录范围  。 如果要改为输入自定义日志记录名称，请选中“自定义”复选框  。 如果你想要日志超出使用可以在包中指定的标准日志提供程序在包执行，则需要通过提供在中的 UNC 路径来指定日志文件夹**日志记录路径**。 例如，如果你将日志存储在 Azure 文件中，你的日志记录路径将是`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。 将在每个单个包运行此路径中创建子文件夹，并将其名为执行 SSIS 包活动运行的 ID，在其中生成日志文件将每隔五分钟后。 
   
   最后，您还需要指定用于访问你的日志文件夹的凭据。 如果您以前为包访问凭据 （见上文） 输入值，则可以通过检查重用它们**相同包访问凭据**复选框。 否则，你需要输入你的日志记录的访问凭据的值 (**域**/**用户名**/**密码**)。 例如，如果你将日志存储在 Azure 文件中**域**是`Azure`;**用户名**是`<storage account name>`; 并且**密码**是`<storage account key>`。 或者，可以使用机密存储在 AKV 中为它们的值 （见上文）。 这些凭据将用于存储日志。 
   
   对于上述所有的 UNC 路径，完全限定的文件名必须少于 260 个字符和目录名称必须少于 248 个字符。

5. 上**SSIS 参数**对于执行 SSIS 包活动，如果将 Azure SSIS IR 正在运行，选项卡**SSISDB**选为你的包位置，并**手动条目**上的复选框**设置**选项卡处于未选中状态，在所选项目/包从 SSISDB 中的现有 SSIS 参数将显示为您要为其赋值。 否则，可以逐个输入以便手动为它们赋值 - 为了使包成功执行，请确保它们存在并已正确输入。 
   
   如果您使用过**EncryptSensitiveWithUserKey**时创建通过 SSDT 在包的保护级别和**文件系统 （包）** /**文件系统 （项目）** 处于选中状态作为你的包位置，还需要重新输入您的敏感参数以在配置文件中或此选项卡上，为它们分配值。 
   
   如果将值分配到你的参数，您可以使用表达式、 函数、 ADF 系统变量和 ADF 管道参数/变量添加动态内容。 或者，可以使用机密存储在 AKV 中为它们的值 （见上文）。

   ![在“SSIS 参数”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. 上**连接管理器**对于执行 SSIS 包活动，如果将 Azure SSIS IR 正在运行，选项卡**SSISDB**选为你的包位置和**手动条目**上的复选框**设置**选项卡处于未选中状态，在所选项目/包从 SSISDB 中的现有连接管理器将显示为您要将值分配给它们的属性。 否则，你可以输入它们通过另一个用于将值手动分配给它们的属性-请确保它们存在并且正确输入为包执行成功。 
   
   如果您使用过**EncryptSensitiveWithUserKey**时创建通过 SSDT 在包的保护级别和**文件系统 （包）** /**文件系统 （项目）** 处于选中状态作为你的包位置，还需要重新输入您的敏感的连接管理器属性将值分配到这些配置文件中或此选项卡上。 
   
   将值分配给连接管理器属性，您可以使用表达式、 函数、 ADF 系统变量和 ADF 管道参数/变量添加动态内容。 或者，可以使用机密存储在 AKV 中为它们的值 （见上文）。

   ![在“连接管理器”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. 上**属性重写**选项卡执行 SSIS 包活动，可以在所选包逐个手动为它们分配值 – 请确保它们是否存在并且是否已正确输入现有属性的路径若要成功，例如在包执行的输入来覆盖用户变量的值，请输入其路径采用以下格式： `\Package.Variables[User::<variable name>].Value`。 
   
   如果您使用过**EncryptSensitiveWithUserKey**时创建通过 SSDT 在包的保护级别和**文件系统 （包）** /**文件系统 （项目）** 处于选中状态作为你的包位置，还需要重新输入您将值分配到这些配置文件中或此选项卡上的敏感属性。 
   
   将值分配给您的属性，您可以使用表达式、 函数、 ADF 系统变量和 ADF 管道参数/变量添加动态内容。

   ![在“属性替代”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   在配置文件和所赋的值*SSIS 参数*选项卡可以使用重写**连接管理器**/**属性重写**选项卡上，而那些上分配**连接管理器**选项卡还使用重写**属性重写**选项卡。

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

4. 您还可以获取 SSISDB 执行 ID 的输出中的管道活动运行，并使用 ID 来检查更全面的执行日志和错误消息中 SQL Server Management Studio (SSMS)。

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
