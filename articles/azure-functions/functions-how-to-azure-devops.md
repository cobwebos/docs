---
title: 通过使用 Azure DevOps-Azure 函数持续交付函数代码更新
description: 了解如何设置 Azure DevOps 管道面向 Azure Functions。
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594460"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>通过使用 Azure DevOps 持续交付

您可以使用自动部署您的函数到 Azure Functions 应用[Azure 管道](/azure/devops/pipelines/)。

有两个选项用于定义你的管道：

- **YAML 文件**:YAML 文件描述管道。 该文件可能具有的生成步骤节和版本部分。 YAML 文件必须与应用相同的存储库中。
- **模板**:模板是现成的生成或部署您的应用程序的任务。

## <a name="yaml-based-pipeline"></a>基于 YAML 管道

若要创建基于 YAML 管道，首先构建您的应用程序，然后部署该应用。

### <a name="build-your-app"></a>生成应用

生成 Azure 管道中的应用的方式取决于应用程序的编程语言。 每种语言具有创建部署项目的特定生成步骤。 部署项目用于在 Azure 中的将函数应用部署。

#### <a name="net"></a>.NET

下面的示例可用于创建要构建一个.NET 应用的 YAML 文件：

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

下面的示例可用于创建要生成的 JavaScript 应用的 YAML 文件：

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

下面的示例可用于创建 YAML 文件用于生成 Python 应用。 仅 Linux Azure Functions 支持 Python。

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

下面的示例可用于创建一个 YAML 文件来打包 PowerShell 应用。 PowerShell 仅支持 Windows Azure Functions。

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

### <a name="deploy-your-app"></a>将应用部署

必须在 YAML 文件中，具体取决于宿主操作系统包括以下 YAML 示例之一。

#### <a name="windows-function-app"></a>Windows 函数应用

以下代码片段可用于部署 Windows 函数应用：

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

以下代码片段可用于部署 Linux 函数应用：

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

Azure DevOps 中的模板是预定义的生成或部署应用程序的任务。

### <a name="build-your-app"></a>生成应用

生成 Azure 管道中的应用的方式取决于应用程序的编程语言。 每种语言具有创建部署项目的特定生成步骤。 部署项目用于更新 Azure 中的函数应用。

若要使用的内置生成模板，创建新的生成管道时，选择**使用经典编辑器**使用设计器模板创建的管道。

![选择 Azure 管道经典编辑器](media/functions-how-to-azure-devops/classic-editor.png)

配置你的代码的源后，搜索 Azure Functions 生成模板。 选择与你的应用程序的语言相匹配的模板。

![选择 Azure Functions 生成模板](media/functions-how-to-azure-devops/build-templates.png)

在某些情况下，生成项目具有特定的文件夹结构。 可能需要选择**预置根文件夹名称，在存档路径**复选框。

![选择前面添加的根文件夹名称](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript 应用程序

如果 JavaScript 应用程序依赖于 Windows 的本机模块，则必须更新到的代理池版本**Hosted VS2017**。

![更新代理池版本](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>将应用部署

当创建新的发布管道时，搜索 Azure Functions 发布模板。

![搜索 Azure Functions 发布模板](media/functions-how-to-azure-devops/release-template.png)

发布模板中不支持部署到的部署槽。

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>使用 Azure CLI 创建一个生成管道

若要在 Azure 中创建一个生成管道，请使用`az functionapp devops-pipeline create`[命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)。 创建生成管道来生成和发布存储库中所做的任何代码更改。 该命令生成的新的 YAML 文件定义生成和发布管道，然后将其提交到存储库。 此命令的必备组件取决于你的代码的位置。

- 如果你的代码是在 GitHub 中：

    - 您必须具有**编写**为你的订阅的权限。

    - 必须是 Azure DevOps 中的项目管理员。

    - 必须有权创建具有足够的权限的 GitHub 个人访问令牌 (PAT)。 有关详细信息，请参阅[GitHub PAT 权限要求。](https://aka.ms/azure-devops-source-repos)

    - 必须有权使您可以提交自动生成的 YAML 文件提交到 GitHub 存储库中的主分支。

- 如果你的代码在 Azure 存储库：

    - 您必须具有**编写**为你的订阅的权限。

    - 必须是 Azure DevOps 中的项目管理员。

## <a name="next-steps"></a>后续步骤

- 审阅[Azure Functions 概述](functions-overview.md)。
- 审阅[Azure DevOps 概述](/azure/devops/pipelines/)。
