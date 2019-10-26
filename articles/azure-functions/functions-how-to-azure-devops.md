---
title: 使用 Azure DevOps 持续交付函数代码更新-Azure Functions
description: 了解如何设置面向 Azure Functions 的 Azure DevOps 管道。
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 4fb01eac53151799a0def00d13f18619faf437f6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72927533"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>使用 Azure DevOps 进行持续交付

可以通过使用[Azure Pipelines](/azure/devops/pipelines/)自动将函数部署到 Azure Functions 应用。

可以使用两个选项来定义管道：

- **YAML 文件**：一个 YAML 文件，用于描述管道。 此文件可能有一个 "生成步骤" 部分和一个 "发布" 部分。 YAML 文件必须在与应用相同的存储库中。
- **模板**：模板是生成或部署应用的现成任务。

## <a name="yaml-based-pipeline"></a>基于 YAML 的管道

若要创建基于 YAML 的管道，请首先构建你的应用程序，然后部署该应用。

### <a name="build-your-app"></a>构建你的应用程序

在 Azure Pipelines 中构建应用程序的方式取决于应用程序的编程语言。 每种语言都有创建部署项目的特定生成步骤。 部署项目用于在 Azure 中部署函数应用。

#### <a name="net"></a>.NET

可以使用以下示例创建 YAML 文件以生成 .NET 应用：

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

你可以使用以下示例创建 YAML 文件来构建 JavaScript 应用：

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

可以使用以下示例创建 YAML 文件来构建 Python 应用。 仅 Linux Azure Functions 支持 Python。 可以通过在此 YAML 中将3.6 的所有实例替换为3.7 来构建 Python 3.7 的 YAML。

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

可以使用以下示例创建 YAML 文件以打包 PowerShell 应用。 只有 Windows Azure Functions 支持 PowerShell。

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>部署应用

你必须在 YAML 文件中包含以下 YAML 示例之一，具体取决于托管操作系统。

#### <a name="windows-function-app"></a>Windows function 应用

你可以使用以下代码片段部署 Windows function 应用：

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux 函数应用

你可以使用以下代码片段来部署 Linux 函数应用：

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>基于模板的管道

Azure DevOps 中的模板是生成或部署应用的预定义任务组。

### <a name="build-your-app"></a>构建你的应用程序

在 Azure Pipelines 中构建应用程序的方式取决于应用程序的编程语言。 每种语言都有创建部署项目的特定生成步骤。 部署项目用于更新 Azure 中的函数应用。

若要使用内置生成模板，请在创建新的生成管道时，选择 **"使用经典编辑器**通过设计器模板创建管道"。

![选择 Azure Pipelines 经典编辑器](media/functions-how-to-azure-devops/classic-editor.png)

配置代码源后，搜索 "Azure Functions 生成模板"。 选择与应用语言匹配的模板。

![选择 Azure Functions 生成模板](media/functions-how-to-azure-devops/build-templates.png)

在某些情况下，生成项目具有特定文件夹结构。 可能需要选中 "**将根文件夹名称预置到存档路径**" 复选框。

![用于预置根文件夹名称的选项](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 应用

如果 JavaScript 应用依赖于 Windows 本机模块，则必须将代理池版本更新为**托管 VS2017**。

![更新代理池版本](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>部署应用

创建新的发布管道时，请搜索 Azure Functions 发布模板。

![搜索 Azure Functions 版本模板](media/functions-how-to-azure-devops/release-template.png)

发布模板中不支持部署到部署槽。

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>使用 Azure CLI 创建生成管道

若要在 Azure 中创建生成管道，请使用 `az functionapp devops-pipeline create`[命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)。 创建生成管道以生成和释放在存储库中所做的任何代码更改。 此命令生成新的 YAML 文件，该文件定义生成和发布管道，然后将其提交到存储库。 此命令的先决条件取决于代码的位置。

- 如果你的代码位于 GitHub 中：

    - 您必须对订阅具有**写入**权限。

    - 你必须是 Azure DevOps 中的项目管理员。

    - 你必须具有创建具有足够权限的 GitHub 个人访问令牌（PAT）的权限。 有关详细信息，请参阅[GITHUB PAT 权限要求。](https://aka.ms/azure-devops-source-repos)

    - 您必须有权提交到 GitHub 存储库中的主分支，才能提交自动生成的 YAML 文件。

- 如果你的代码在 Azure Repos：

    - 您必须对订阅具有**写入**权限。

    - 你必须是 Azure DevOps 中的项目管理员。

## <a name="next-steps"></a>后续步骤

- 查看[Azure Functions 概述](functions-overview.md)。
- 查看[Azure DevOps 概述](/azure/devops/pipelines/)。
