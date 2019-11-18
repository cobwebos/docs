---
title: 使用 "执行 SSIS 包" 活动运行 SSIS 包-Azure
description: 本文介绍如何使用 "执行 SSIS 包" 活动在 Azure 数据工厂管道中运行 SQL Server Integration Services （SSIS）包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/14/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ddb7cd06934c85243717dd2a34dc99bae582b6fa
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122963"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用“执行 SSIS 包”活动运行 SSIS 包
本文介绍如何使用 "执行 SSIS 包" 活动在 Azure 数据工厂管道中运行 SQL Server Integration Services （SSIS）包。 

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果尚未按照本教程中的分步说明操作，请创建 Azure SSIS 集成运行时（IR） [：预配 Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)。

## <a name="run-a-package-in-the-azure-portal"></a>在 Azure 门户中运行包
在本部分中，将使用数据工厂用户界面（UI）或应用通过运行 SSIS 包的 "执行 SSIS 包" 活动来创建数据工厂管道。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用“执行 SSIS 包”活动创建管道
在此步骤中，将使用数据工厂 UI 或应用创建管道。 将“执行 SSIS 包”活动添加到管道，并将该活动配置为运行 SSIS 包。 

1. 在 Azure 门户中的数据工厂概述或主页上，选择 "**作者 & 监视器**" 磁贴，在单独的选项卡中启动数据工厂 UI 或应用。 

   ![数据工厂主页](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   在“开始使用”页中，选择“创建管道”。 

   ![“入门”页](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. 在 "**活动**" 工具箱中展开 "**常规**"。 然后将 "**执行 SSIS 包**" 活动拖到管道设计器图面。 

   ![将“执行 SSIS 包”活动拖到设计面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. 在 "执行 SSIS 包" 活动的 "**常规**" 选项卡上，为活动提供名称和说明。 设置可选的**超时**值和**重试**值。

   ![在“常规”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. 在 "执行 SSIS 包" 活动的 "**设置**" 选项卡上，选择要在其中运行包的 Azure-SSIS IR。 如果你的包使用 Windows 身份验证访问数据存储（例如，SQL server 或本地或 Azure 文件共享），请选中 " **windows 身份验证**" 复选框。 在 "**域**"、"**用户名**" 和 "**密码**" 框中输入包执行凭据的值。 

    或者，你可以使用存储在 Azure 密钥保管库中的机密作为其值。 为此，请选中相关凭据旁边的 " **AZURE 密钥保管库**" 复选框。 选择或编辑现有的 key vault 链接服务，或创建一个新的服务。 然后选择凭据值的机密名称或版本。

    创建或编辑 key vault 链接服务时，可以选择或编辑现有密钥保管库，也可以创建新的密钥保管库。 如果尚未这样做，请确保向数据工厂托管标识授予对密钥保管库的访问权限。 此外，还可以采用以下格式直接输入机密：`<Key vault linked service name>/<secret name>/<secret version>`。 如果你的包需要32位运行时才能运行，请选中 " **32 位运行时**" 复选框。

   对于**包位置**，请选择 " **SSISDB**"、"**文件系统（包）** "、"**文件系统（项目）** " 或 "**嵌入式包**"。 如果你选择**SSISDB**作为包位置，并且如果你的 Azure-SSIS IR 是使用 Azure SQL 数据库服务器或托管实例承载的 SSIS 目录（SSISDB）设置的，则会自动选择到 SSISDB 中。 

    如果你的 Azure-SSIS IR 正在运行，并且清除了 "**手动输入**" 复选框，请浏览并从 SSISDB 中选择现有的文件夹、项目、包或环境。 选择 "**刷新**" 可从 SSISDB 中获取新添加的文件夹、项目、包或环境，使其可供浏览和选择。 若要浏览或选择用于执行包的环境，必须事先配置项目以将这些环境添加为 SSISDB 下的相同文件夹中的引用。 有关详细信息，请参阅[创建和映射 SSIS 环境](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)。

   对于“日志记录级别”，请为包执行选择预定义的日志记录范围。 如果要改为输入自定义日志记录名称，请选中 "**自定义**" 复选框。 

   ![在“设置”选项卡上设置属性 - 自动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   如果 Azure-SSIS IR 未运行或已选中 "**手动输入**" 复选框，请按以下格式直接从 SSISDB 输入包和环境路径： `<folder name>/<project name>/<package name>.dtsx` 和 `<folder name>/<environment name>`。

   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   如果选择 "**文件系统（包）** " 作为包位置，并且如果你的 Azure-SSIS IR 是在没有 SSISDB 的情况下设置的，则会自动选择你的包，方法是提供包文件的通用命名约定（UNC）路径（`.dtsx`）。 例如，如果你将包存储在 Azure 文件中，则其包路径 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`。 
   
   如果将包配置为单独的文件，还需要在 "**配置路径**" 框中提供配置文件（`.dtsConfig`）的 UNC 路径。 例如，如果你将配置存储在 Azure 文件中，则其配置路径将 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`。

   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   如果选择 "**文件系统（项目）** " 作为包位置，请在 "**项目路径**" 框中提供项目文件的 UNC 路径（`.ispac`），并在**包名称**中提供项目的包文件（`.dtsx`），以指定要运行的包。文本框. 例如，如果你将项目存储在 Azure 文件中，则其项目路径将 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`。

   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   接下来，指定用于访问项目、包或配置文件的凭据。 如果你之前输入了包执行凭据的值（请参阅上一步），可以通过选择 "与**包执行凭据相同**" 复选框重复使用它们。 否则，请在 "**域**"、"**用户名**" 和 "**密码**" 框中输入包访问凭据的值。 例如，如果将项目、包或配置存储在 Azure 文件中，则会 `Azure`域，用户名 `<storage account name>`，并 `<storage account key>`密码。 

   或者，你可以使用存储在密钥保管库中的机密作为它们的值（请参阅上一节）。 这些凭据用于访问 "执行包" 任务中的包和子包，全部来自其自己的路径或相同的项目，以及配置（包括包中指定的包和配置）。 

   如果选择 "**嵌入的包**" 作为包位置，请将包拖放到所提供的框中，将其从文件文件夹运行或**上传**。 你的包将自动压缩并嵌入到活动有效负载中。 嵌入后，可以在以后**下载**包进行编辑。 还可以通过将嵌入的包分配给可在多个活动中使用的管道参数来对其进行**参数**化，从而优化管道有效负载的大小。 如果你的嵌入式包未全部加密并且我们检测到其中的 "执行包" 任务，则会自动选择 "**执行包任务**" 复选框，并将自动添加相关子包及其文件系统引用。 如果无法检测到 "执行包" 任务的使用，则必须手动选中 "**执行包任务**" 复选框，并添加相关子包及其文件系统，以便您还可以将其嵌入。 如果子包使用 SQL Server 引用，请确保 Azure-SSIS IR 可以访问 SQL Server。  当前不支持为子包使用项目引用。
   
   ![在“设置”选项卡上设置属性 - 手动](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   如果在通过 SQL Server Data Tools 创建包时使用了 " **EncryptAllWithPassword** " 或 " **EncryptSensitiveWithPassword** " 保护级别，请在 "**加密密码**" 框中输入密码的值。 或者，你可以使用存储在密钥保管库中的机密作为其值（请参阅上一节）。 如果使用了**EncryptSensitiveWithUserKey**保护级别，请在配置文件中或在**SSIS 参数**、**连接管理器**或**属性替代**选项卡上重新输入敏感值（请参阅下文）。 

   如果使用**EncryptAllWithUserKey**保护级别，则不受支持。 你需要通过 SQL Server Data Tools 或 `dtutil` 命令行实用程序将包重新配置为使用另一个保护级别。 
   
   对于“日志记录级别”，请为包执行选择预定义的日志记录范围。 如果要改为输入自定义日志记录名称，请选中 "**自定义**" 复选框。 如果要使用可以在包中指定的标准日志提供程序来记录包的执行，请通过在 "**日志记录路径**" 框中提供其 UNC 路径来指定日志文件夹。 例如，如果将日志存储在 Azure 文件中，则会 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`日志记录路径。 对于每个单独的包运行，将在此路径中创建一个子文件夹，每隔五分钟生成一次日志文件。 
   
   最后，指定访问日志文件夹所用的凭据。 如果你之前输入了包访问凭据的值（请参阅上一步），则可以通过选择 "与**包访问凭据相同**" 复选框来重复使用这些值。 否则，请在 "**域**"、"**用户名**" 和 "**密码**" 框中输入日志访问凭据的值。 例如，如果将日志存储在 Azure 文件中，则会 `Azure`域，用户名 `<storage account name>`，并 `<storage account key>`密码。 

    或者，你可以使用存储在密钥保管库中的机密作为它们的值（请参阅上一节）。 这些凭据用于存储日志。 
   
   对于前面提到的所有 UNC 路径，完全限定的文件名必须少于260个字符。 目录名称必须少于248个字符。

1. 在 "执行 SSIS 包" 活动的 " **SSIS 参数**" 选项卡上，如果 Azure-SSIS IR 正在运行，则选择 " **SSISDB** " 作为 "包位置"，并清除 "**设置**" 选项卡上的 "**手动条目**" 复选框，即现有 SSIS将显示你从 SSISDB 中选择的项目或包中的参数，以便为你分配值。 否则，你可以逐个输入这些值，以手动为其赋值。 请确保它们存在并正确输入，以便成功执行包。 
   
   如果在通过 SQL Server Data Tools 和**文件系统（包）** 或**文件系统（项目）** 创建包时使用了**EncryptSensitiveWithUserKey**保护级别作为包位置，则还需要重新输入要在配置文件或此选项卡中向其分配值的敏感参数。 
   
   为参数赋值时，可以通过使用表达式、函数、数据工厂系统变量和数据工厂管道参数或变量来添加动态内容。 或者，你可以使用存储在密钥保管库中的机密作为它们的值（请参阅上一节）。

   ![在“SSIS 参数”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. 在 "执行 SSIS 包" 活动的 "**连接管理器**" 选项卡上，如果 Azure-SSIS IR 正在运行，将选择 " **SSISDB** " 作为包位置，并清除 "**设置**" 选项卡上的 "**手动输入**" 复选框，即现有系统会显示所选项目或包中的连接管理器，以便为其属性分配值。 否则，你可以逐个输入这些值，以手动为其属性赋值。 请确保它们存在并正确输入，以便成功执行包。 
   
   如果在通过 SQL Server Data Tools 和**文件系统（包）** 或**文件系统（项目）** 创建包时使用了**EncryptSensitiveWithUserKey**保护级别作为包位置，则还需要重新输入要在配置文件或此选项卡上为这些属性分配值的敏感连接管理器属性。 
   
   将值分配给连接管理器属性时，可以通过使用表达式、函数、数据工厂系统变量和数据工厂管道参数或变量来添加动态内容。 或者，你可以使用存储在密钥保管库中的机密作为它们的值（请参阅上一节）。

   ![在“连接管理器”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. 在 "执行 SSIS 包" 活动的 "**属性替代**" 选项卡上，逐个输入所选包中现有属性的路径，以手动为其赋值。 请确保它们存在并正确输入，以便成功执行包。 例如，若要覆盖用户变量的值，请按以下格式输入其路径： `\Package.Variables[User::<variable name>].Value`。 
   
   如果在通过 SQL Server Data Tools 和**文件系统（包）** 或**文件系统（项目）** 创建包时使用了**EncryptSensitiveWithUserKey**保护级别作为包位置，则还需要重新输入要在配置文件或此选项卡中向其分配值的敏感属性。 
   
   为属性赋值时，可以通过使用表达式、函数、数据工厂系统变量和数据工厂管道参数或变量来添加动态内容。

   ![在“属性替代”选项卡上设置属性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   使用 "**连接管理器**" 或 "**属性替代**" 选项卡，可以重写在 "配置文件" 和 " **SSIS 参数**" 选项卡中分配的值。 还可以通过使用 "**属性替代**" 选项卡来替代在 "**连接管理器**" 选项卡上分配的值。

1. 若要验证管道配置，请在工具栏上选择 "**验证**"。 若要关闭**管道验证报告**，请选择 " **>>** "。

1. 若要将管道发布到数据工厂，请选择 "**全部发布**"。 

### <a name="run-the-pipeline"></a>运行管道
在此步骤中，将触发管道运行。 

1. 若要触发管道运行，请在工具栏上选择 "**触发器**"，然后选择 "**立即触发**"。 

   ![立即触发](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在“管道运行”窗口中选择“完成”。 

### <a name="monitor-the-pipeline"></a>监视管道

1. 在左侧切换到“监视”选项卡。 你会看到管道运行及其状态以及其他信息，如**运行开始**时间。 若要刷新视图，请选择“刷新”。

   ![管道运行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. 在“操作”列中选择“查看活动运行”链接。 只能看到一个活动运行，因为管道只有一个活动。 这是 "执行 SSIS 包" 活动。

   ![活动运行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 在 SQL server 中针对 SSISDB 数据库运行以下查询，验证包是否已执行。 

   ```sql
   select * from catalog.executions
   ```

   ![验证包执行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 还可以从管道活动运行的输出中获取 SSISDB 执行 ID，并使用 ID 检查 SQL Server Management Studio 中更全面的执行日志和错误消息。

   ![获取执行 ID。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用触发器计划管道

你还可以为管道创建计划的触发器，以便按计划（例如每小时或每天）运行管道。 有关示例，请参阅[创建数据工厂 - 数据工厂 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="run-a-package-with-powershell"></a>使用 PowerShell 运行包
在本部分中，将使用 Azure PowerShell 通过运行 SSIS 包的 "执行 SSIS 包" 活动来创建数据工厂管道。 

按照[如何安装和配置 Azure PowerShell](/powershell/azure/install-az-ps) 中的分步说明安装最新的 Azure PowerShell 模块。

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>使用 Azure-SSIS IR 创建数据工厂
可以使用已预配 Azure-SSIS IR 的现有数据工厂，也可以使用 Azure-SSIS IR 创建新的数据工厂。 按照本教程中的分步说明[操作：通过 PowerShell 将 SSIS 包部署到 Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用“执行 SSIS 包”活动创建管道 
在此步骤中创建包含“执行 SSIS 包”活动的管道。 该活动运行 SSIS 包。 

1. 在*C:\ADF\RunSSISPackage*文件夹中创建一个名为*RunSSISPackagePipeline*的 json 文件，其中包含类似于以下示例的内容。

   > [!IMPORTANT]
   > 保存文件之前，请替换对象名称、说明和路径、属性或参数值、密码和其他变量值。 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
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
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
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

   若要执行存储在文件系统、文件共享或 Azure 文件中的包，请输入 "包" 和 "日志位置" 属性的值，如下所示：

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   若要执行存储在文件系统、文件共享或 Azure 文件中的项目中的包，请输入包位置属性的值，如下所示：

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   若要执行嵌入的包，请输入包位置属性的值，如下所示：

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. 在 Azure PowerShell 中，切换到*C:\ADF\RunSSISPackage*文件夹。

3. 若要创建管道 **RunSSISPackagePipeline**，请运行 **Set-AzDataFactoryV2Pipeline** cmdlet。

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
使用 **Invoke-AzDataFactoryV2Pipeline** cmdlet 运行该管道。 此 cmdlet 返回管道运行 ID，用于将来的监视。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>监视管道

运行以下 PowerShell 脚本，持续检查管道运行状态，直到完成数据复制为止。 在 PowerShell 窗口中复制或粘贴下面的脚本，然后选择 Enter。 

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

还可以使用 Azure 门户来监视管道。 有关分步说明，请参阅[监视管道](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)。

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用触发器计划管道
在上一步中，你按需运行了管道。 你还可以创建一个计划触发器，以便按计划（例如每小时或每天）运行管道。

1. 在*C:\ADF\RunSSISPackage*文件夹中，创建包含以下内容的名为*mytrigger.json*的 json 文件： 
        
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
    
1. 在 Azure PowerShell 中，切换到*C:\ADF\RunSSISPackage*文件夹。
1. 运行 **Set-AzDataFactoryV2Trigger** cmdlet，以创建触发器。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 默认情况下，触发器处于停止状态。 运行 **Start-AzDataFactoryV2Trigger** cmdlet 以启动该触发器。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. 通过运行 **Get-AzDataFactoryV2Trigger** cmdlet 确认该触发器已启动。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. 在下一小时后运行以下命令。 例如，如果当前时间为下午 3:25 (UTC)，则在下午 4:00 (UTC) 运行该命令。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   在 SQL server 中针对 SSISDB 数据库运行以下查询，验证包是否已执行。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>后续步骤
请参阅以下博客文章：
- [通过 Azure 数据工厂管道中的 SSIS 活动实现对 ETL/ELT 工作流的现代化和扩展](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
