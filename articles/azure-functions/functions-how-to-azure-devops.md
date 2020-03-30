---
title: 使用 Azure DevOps 不断更新函数应用代码
description: 了解如何设置以 Azure 函数为目标的 Azure DevOps 管道。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255757"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>使用 Azure DevOps 连续传递

可以使用[Azure 管道](/azure/devops/pipelines/)自动将函数部署到 Azure 函数应用。

您有两个选项用于定义管道：

- **YAML 文件**：YAML 文件描述管道。 该文件可能具有生成步骤部分和发布部分。 YAML 文件必须与应用处于同一回购中。
- **模板**：模板是生成或部署应用的现成任务。

## <a name="yaml-based-pipeline"></a>基于 YAML 的管道

要创建基于 YAML 的管道，请先生成应用，然后部署应用。

### <a name="build-your-app"></a>生成应用

如何在 Azure 管道中构建应用取决于应用的编程语言。 每种语言都有创建部署项目的特定生成步骤。 部署项目用于在 Azure 中部署函数应用。

# <a name="c"></a>[C\#](#tab/csharp)

可以使用以下示例创建 YAML 文件来生成 .NET 应用：

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
    modifyOutputPath: false
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
    artifactName: 'drop'
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

可以使用以下示例创建 YAML 文件来构建 JavaScript 应用：

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
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

可以使用以下示例之一创建 YAML 文件，为特定的 Python 版本构建应用。 Python 仅支持在 Linux 上运行的函数应用程序。

**版本 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**版本 3.6**

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
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[电源外壳](#tab/powershell)

您可以使用以下示例创建 YAML 文件来打包 PowerShell 应用。 仅支持 Windows Azure 功能的 PowerShell。

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
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>部署应用

您必须在 YAML 文件中包括以下 YAML 示例之一，具体取决于托管操作系统。

#### <a name="windows-function-app"></a>窗口功能应用

您可以使用以下代码段部署 Windows 函数应用：

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

#### <a name="linux-function-app"></a>Linux函数应用程序

您可以使用以下代码段部署 Linux 函数应用：

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

Azure DevOps 中的模板是生成或部署应用的任务的预定义组。

### <a name="build-your-app"></a>生成应用

如何在 Azure 管道中构建应用取决于应用的编程语言。 每种语言都有创建部署项目的特定生成步骤。 部署项目用于在 Azure 中更新函数应用。

要使用内置生成模板，请在创建新生成管道时选择 **"使用经典编辑器**使用设计器模板创建管道"。

![选择 Azure 管道经典编辑器](media/functions-how-to-azure-devops/classic-editor.png)

配置代码源后，搜索 Azure 函数生成模板。 选择与您的应用语言匹配的模板。

![选择 Azure 函数生成模板](media/functions-how-to-azure-devops/build-templates.png)

在某些情况下，生成工件具有特定的文件夹结构。 您可能需要选择 **"准备结束根文件夹名称以存档路径**"复选框。

![准备根文件夹名称的选项](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 应用

如果您的 JavaScript 应用依赖于 Windows 本机模块，则必须将代理池版本更新为**托管 VS2017**。

![更新代理池版本](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>部署应用

创建新发布管道时，搜索 Azure 函数发布模板。

![搜索 Azure 函数发布模板](media/functions-how-to-azure-devops/release-template.png)

发布模板不支持部署到部署槽。

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>使用 Azure CLI 创建生成管道

要在 Azure 中创建生成管道，`az functionapp devops-pipeline create`请使用[命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)。 创建生成管道是为了生成和释放在回购中所做的任何代码更改。 该命令生成一个新的 YAML 文件，该文件定义生成和发布管道，然后将其提交到您的回购中。 此命令的先决条件取决于代码的位置。

- 如果代码在 GitHub 中：

    - 您必须具有订阅的**写入**权限。

    - 您必须是 Azure DevOps 中的项目管理员。

    - 您必须具有创建具有足够权限的 GitHub 个人访问令牌 （PAT） 的权限。 有关详细信息，请参阅[GitHub PAT 权限要求。](https://aka.ms/azure-devops-source-repos)

    - 您必须具有向 GitHub 存储库中的主分支提交的权限，以便提交自动生成的 YAML 文件。

- 如果代码位于 Azure 存储库中：

    - 您必须具有订阅的**写入**权限。

    - 您必须是 Azure DevOps 中的项目管理员。

## <a name="next-steps"></a>后续步骤

- 查看[Azure 函数概述](functions-overview.md)。
- 查看[Azure DevOps 概述](/azure/devops/pipelines/)。
