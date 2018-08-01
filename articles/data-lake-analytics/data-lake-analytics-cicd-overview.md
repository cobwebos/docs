---
title: 如何为 Azure Data Lake Analytics 设置 CI/CD 管道 | Microsoft Docs
description: 了解如何为 Azure Data Lake Analytics 设置持续集成和持续部署。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145003"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>如何为 Azure Data Lake Analytics 设置 CI/CD 管道

本文档介绍如何为 U-SQL 作业和 U-SQL 数据库设置 CI/CD 管道。

## <a name="cicd-for-u-sql-job"></a>用于 U-SQL 作业的 CI/CD

针对 Visual Studio 的 Azure Data Lake 工具提供帮你组织 U-SQL 脚本的 U-SQL 项目类型。 使用 U-SQL 项目管理你的 U-SQL 代码便于创建进一步的 CI/CD 场景。

## <a name="build-u-sql-project"></a>生成 U-SQL 项目

通过传递相应的参数，可以使用 MSBuild 生成 U-SQL 项目。 按照以下步骤设置 U-SQL 项目的生成过程。

### <a name="project-migration"></a>项目迁移

在设置 U-SQL 项目的生成任务之前，请确保使用最新版本的 U-SQL 项目。 在编辑器中打开 U-SQL 项目文件并检查是否具有以下导入项目：

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

如果没有，可以使用下面的两个选项来迁移项目：

- 选项 1：将旧的导入项目更改为上面的一个。
- 选项 2：在版本 2.3.3000.0 之后的针对 Visual Studio 的 Azure Data Lake 工具中打开旧项目。 旧的项目模板将自动升级到最新版本。 版本 2.3.3000.0 之后新创建的项目直接使用新的模板。

### <a name="get-nuget-package"></a>获取 NuGet 包

MSBuild 不提供对 U-SQL 项目类型的内置支持。 若要添加此功能，需要将对解决方案的引用添加到添加所需语言服务的 [Microsoft.Azure.DataLake.USQL.SDK Nuget 包](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)。

若要添加 NuGet 包引用，可以在解决方案资源管理器中右键单击该解决方案，然后选择“管理 NuGet 包”。 也可以在解决方案文件夹中添加一个名为 `packages.config` 的文件并将以下内容添加到其中。

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>管理 U-SQL 数据库引用

如果 U-SQL 项目中的 U-SQL 脚本具有用于 U-SQL 数据库对象的查询语句（例如，查询 U-SQL 表或引用程序集），则需要在生成此 U-SQL 项目之前，引用包含这些对象的定义的相应 U-SQL 数据库项目。

[了解有关 U-SQL 数据库项目的详细信息](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>U-SQL 数据库项目目前处于公开预览状态。 如果该项目中有 DROP 语句，生成将失败。 DROP 语句不久将会被允许。
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>使用 MSBuild 命令行生成 U-SQL 项目

在迁移项目并获取 NuGet 包之后，可以使用下面的其他参数调用标准的 MSBuild 命令行以生成 U-SQL 项目：

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

参数的定义和值为：

* USQLSDKPath=<U-SQL Nuget package>\build\runtime：此参数指 U-SQL 语言服务的 NuGet 包的安装路径。
* USQLTargetType=Merge 或 SyntaxCheck：
    * Merge：Merge 模式编译代码隐藏文件（如 .cs、.py 和 .r 文件）并将生成的用户定义的代码库（如 dll 库、Python 或 R 代码）内联到 U-SQL 脚本。
    * SyntaxCheck：SyntaxCheck 模式首先将代码隐藏文件合并到 U-SQL 脚本中，然后再编译 U-SQL 脚本以验证你的代码。
* DataRoot=<DataRoot path>：仅 SyntaxCheck 模式需要 DataRoot。 当使用 SyntaxCheck 模式生成脚本时，MSBuild 会检查脚本中对数据库对象的引用。 确保在生成之前设置匹配的本地环境，其中包含生成计算机的 DataRoot 文件夹中 U-SQL 数据库中的引用对象。 你还可以通过[引用 U-SQL 数据库项目](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)管理这些数据库依赖项。 请注意，MSBuild 只会检查数据库对象引用，而不检查文件。
* EnableDeployment=true 或 false：EnableDeployment 指示是否允许在生成过程中部署引用的 U-SQL 数据库。 如果引用了 U-SQL 数据库项目，并在 U-SQL 脚本中使用了数据库对象，请将此参数设置为 true。

### <a name="continuous-integration-with-visual-studio-team-service"></a>与 Visual Studio Team Service 的持续集成

除了命令行以外，客户还可以使用 Visual Studio 生成或 MSBuild 任务在 Visual Studio Team Service 中生成 U-SQL 项目。 若要设置生成任务，请确保：

1.  添加 NuGet 还原任务以获取解决方案引用的包括 `Azure.DataLake.USQL.SDK` 的 NuGet 包，以便 MSBuild 可以找到 U-SQL 语言目标。 如果要在步骤 2 中直接使用 MSBuild 参数示例，请将“高级”>“目标目录”设置为 `$(Build.SourcesDirectory)/packages`。

    ![Data Lake 为 U-SQL 项目设置了 CI CD MSBuild 任务](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake 为 U-SQL 项目设置了 CI CD Nuget 任务](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  设置 MSBuild 参数，并且可以在如下 Visual Studio 生成或 MSBuild 任务中设置参数，还可以在 VSTS 生成定义中为这些参数定义变量。

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Data Lake 为 U-SQL 项目设置了 CI CD MSBuild 变量](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>U-SQL 项目生成输出

运行生成后，生成 U-SQL 项目中的所有脚本并输出到名为 `USQLProjectName.usqlpack` 的 zip 文件。 项目中的文件夹结构将保留在压缩的生成输出中。

>[!NOTE]
>
>每个 U-SQL 脚本的代码隐藏文件将作为内联语句合并到脚本生成输出。
>

## <a name="test-u-sql-script"></a>测试 U-SQL 脚本

Azure Data Lake 为 U-SQL 脚本和 C# UDO/UDAG/UDF 提供测试项目：
* [了解如何为 U-SQL 脚本和扩展的 C# 代码添加测试用例](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [了解如何在 Visual Studio Team Service 中运行这些测试用例](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>U-SQL 作业部署

通过生成和测试过程验证代码后，可以通过“Azure PowerShell 任务”直接从 Visual Studio Team Service 提交 U-SQL 作业。 还可以将脚本部署到 Azure Data Lake Store/Azure Blob 存储并[通过 Azure 数据工厂运行计划的作业](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)。

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>通过 Visual Studio Team Service 提交 U-SQL 作业

U-SQL 项目的生成输出是一个名为“USQLProjectName.usqlpack”的 zip 文件，其中包括项目中的所有 U-SQL 脚本。 你可以将 [Visual Studio Team Service 中的 Azure PowserShell 任务](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts)与下面的示例 PowserShell 脚本配合使用，直接从 Visual Studio Team Service 生成或发布管道提交 U-SQL 作业。

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>通过 Azure 数据工厂部署 U-SQL 作业

除了直接从 Visual Studio Team Service 提交 U-SQL 作业，你还可以将生成的脚本上传到 Azure Data Lake Store/Azure Blob 存储并[通过 Azure 数据工厂运行计划的作业](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)。

将 [Visual Studio Team Service 中的 Azure PowerShell 任务](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts)与下面的示例 PowerShell 脚本配合使用，将 U-SQL 脚本上传到 Azure Data Lake Store 帐户。

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>用于 U-SQL 数据库的 CI/CD

针对 Visual Studio 的 Azure Data Lake 工具提供 U-SQL 数据库项目模板，这些模板可以帮助开发人员快速轻松地开发、管理和部署 U-SQL 数据库。 [了解有关 U-SQL 数据库项目的详细信息](data-lake-analytics-data-lake-tools-develop-usql-database.md)。

## <a name="build-u-sql-database-project"></a>生成 U-SQL 数据库项目

### <a name="get-nuget-package"></a>获取 NuGet 包

MSBuild 不提供对 U-SQL 数据库项目类型的内置支持。 若要添加此功能，需要将对解决方案的引用添加到添加所需语言服务的 [Microsoft.Azure.DataLake.USQL.SDK Nuget 包](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)。

若要添加 NuGet 包引用，可以在解决方案资源管理器中右键单击该解决方案，然后为该解决方案选择“管理 NuGet 包”，最后搜索并安装 NuGet 包。 也可以在解决方案文件夹中添加一个名为“packages.config”的文件并将以下内容添加到其中。

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>使用 MSBuild 命令行生成 U-SQL 数据库项目

可以调用标准的 MSBuild 命令行并将 U-SQL SDK NuGet 包引用作为附加参数传递（如下所示）以生成 U-SQL 数据库项目：

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

参数 `USQLSDKPath=<U-SQL Nuget package>\build\runtime` 指 U-SQL 语言服务的 NuGet 包的安装路径。

### <a name="continuous-integration-with-visual-studio-team-service"></a>与 Visual Studio Team Service 的持续集成

除了命令行以外，客户还可以使用“Visual Studio 生成”或“MSBuild 任务”在 Visual Studio Team Service 中生成 U-SQL 数据库项目。 若要设置生成任务，请确保：

1.  添加 NuGet 还原任务以获取解决方案引用的包括 `Azure.DataLake.USQL.SDK` 的 NuGet 包，以便 MSBuild 可以找到 U-SQL 语言目标。 如果要在步骤 2 中直接使用 MSBuild 参数示例，请将“高级”>“目标目录”设置为 `$(Build.SourcesDirectory)/packages`。

    ![Data Lake 为 U-SQL 项目设置了 CI CD MSBuild 任务](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake 为 U-SQL 项目设置了 CI CD Nuget 任务](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  设置 MSBuild 参数，并且可以在如下 Visual Studio 生成或 MSBuild 任务中设置参数，还可以在 VSTS 生成定义中为这些参数定义变量。

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![Data Lake 为 U-SQL 数据库项目设置了 CI CD MSBuild 变量](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>U-SQL 数据库项目生成输出

U-SQL 数据库项目的生成输出是一个 U-SQL 数据库部署包，以前缀 `.usqldbpack` 命名。 `.usqldbpack` 包是一个 zip 文件，其中包含“DDL”文件夹中单个 U-SQL 脚本中的所有 DDL 语句，以及“Temp”文件夹中程序集的所有 .dll 和其他文件。

## <a name="test-table-valued-function-and-stored-procedure"></a>测试表值函数和存储过程

目前，不支持为表值函数和存储过程直接添加测试用例。 若要解决此问题，可以创建具有可调用这些函数的 U-SQL 脚本的 U-SQL 项目，并为它们编写测试用例。 按照以下步骤为在 U-SQL 数据库项目中定义的表值函数和存储过程设置测试用例：

1.  创建用于测试目的的 U-SQL 项目，并编写调用表值函数和存储过程的 U-SQL 脚本。
2.  添加对此 U-SQL 项目的数据库引用。 为了获取表值函数和存储过程定义，需要引用包含 DDL 语句的数据库项目。 [了解有关数据库引用的详细信息](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)。
3.  为调用表值函数和存储过程的 U-SQL 脚本添加测试用例。 [了解如何为 U-SQL 脚本添加测试用例](data-lake-analytics-cicd-test.md#test-u-sql-scripts)。

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>通过 Visual Studio Team Service 部署 U-SQL 数据库

`PackageDeploymentTool.exe` 提供帮助部署 U-SQL 数据库部署包 (.usqldbpack) 的编程和命令行接口。 SDK 包含在 [U-SQL SDK NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)中，位于 build/runtime/PackageDeploymentTool.exe。 通过使用 `PackageDeploymentTool.exe`，可以将 U-SQL 数据库部署到 Azure Data Lake Analytics 和本地帐户。

>[!NOTE]
>
>针对 U-SQL 数据库部署的 PowerShell 命令行支持和 Visual Studio Team Service 发布任务支持正在开发中。
>

按照以下步骤在 Visual Studio Team Service 中设置数据库部署任务：

1. 在生成或发布管道中添加 PowerShell 脚本任务，并执行下面的 PowerShell 脚本。 此任务可帮助获取 `PackageDeploymentTool.exe` 和 `PackageDeploymentTool.exe` 的 Azure SDK 依赖项。 可以设置 -AzureSDK 和 -DBDeploymentTool 参数以将依赖项和部署工具加载到某些特定文件夹。 在步骤 2 中将 -AzureSDK 路径作为 -AzureSDKPath 参数传递给 `PackageDeploymentTool.exe`。 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. 在生成或发布管道中添加“命令行任务”并填写调用 `PackageDeploymentTool.exe` 的脚本。 `PackageDeploymentTool.exe` 位于定义的 $DBDeploymentTool 文件夹下。 示例脚本如下所示： 

    * 在本地部署 U-SQL 数据库

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * 使用交互式身份验证模式将 U-SQL 数据库部署到 Azure Data Lake Analytics 帐户：

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * 使用机密身份验证将 U-SQL 数据库部署到 Azure Data Lake Analytics 帐户：

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * 使用 certFile 身份验证将 U-SQL 数据库部署到 Azure Data Lake Analytics 帐户：

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**PackageDeploymentTool.exe 参数说明：**

**常见参数：**

|参数|Description|默认值|必选|
|---------|-----------|-------------|--------|
|程序包|要部署的 U-SQL 数据库部署包的路径|Null|是|
|数据库|要部署到/创建的数据库名称|master|false|
|LogFile|用于记录的文件路径，默认为标准输出（控制台）|Null|false|
|LogLevel|日志级别：详细、正常、警告、错误|LogLevel.Normal|false|

**用于本地部署的参数：**

|参数|Description|默认值|必选|
|---------|-----------|-------------|--------|
|DataRoot|本地数据根文件夹的路径|Null|是|

**用于 Azure Data Lake Analytics 部署的参数：**

|参数|Description|默认值|必选|
|---------|-----------|-------------|--------|
|帐户|按帐户名称指定部署到哪个 Azure Data Lake Analytics 帐户|Null|是|
|resourceGroup|Azure Data Lake Analytics 帐户的 Azure 资源组名称|Null|是|
|SubscriptionId|Azure Data Lake Analytics 帐户的 Azure 订阅 ID|Null|是|
|租户|租户名称（AAD Directory 域名，可在 Azure 门户的订阅管理页面中找到）|Null|是|
|AzureSDKPath|要在 Azure SDK 中搜索依赖程序集的路径|Null|是|
|交互|是否使用交互模式进行身份验证|false|false|
|ClientId|用于非交互式身份验证的 AAD 应用程序 ID，非交互式身份验证要求|Null|非交互式身份验证要求|
|机密|用于非交互式身份验证的机密/密码，它仅应在受信任/安全的环境中使用|Null|非交互式身份验证要求，或使用 SecreteFile|
|SecreteFile|文件保存非交互式身份验证的机密/密码，确保其仅可由当前用户读取|Null|非交互式身份验证要求，或使用机密|
|CertFile|文件保存非交互式身份验证的 X.509 证书，默认情况下使用客户端机密身份验证|Null|false|
|JobPrefix|数据库部署 U-SQL DDL 作业的前缀|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>后续步骤

- [如何测试 Azure Data Lake Analytics 代码](data-lake-analytics-cicd-test.md)
- [在本地计算机上运行 U-SQL 脚本](data-lake-analytics-data-lake-tools-local-run.md)
- [使用 U-SQL 数据库项目开发 U-SQL 数据库](data-lake-analytics-data-lake-tools-develop-usql-database.md)
