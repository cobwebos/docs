---
title: 持续交付函数代码更新使用 Azure DevOps
description: 了解如何设置面向 Azure Functions 的 Azure DevOps 管道。
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 86f1c36bd5f972a6ebd573019a22b0c0d07dc480
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928087"
---
# <a name="continuous-delivery-using-azure-devops"></a>使用 Azure DevOps 实现持续交付

你可以自动部署到 Azure 函数应用使用的函数[Azure 管道](/azure/devops/pipelines/)。
若要定义你的管道，可以使用：

- YAML 文件：此文件描述了管道，它可能具有生成步骤一节，并发布部分。 YAML 文件应与应用相同的存储库中。

- 模板：模板已准备进行生成或部署您的应用程序的任务。

## <a name="yaml-based-pipeline"></a>基于 YAML 管道

### <a name="build-your-app"></a>构建你的应用程序

生成 Azure 管道中的应用取决于您的应用程序的编程语言。
每种语言有特定的生成步骤，以创建用于部署项目，可用于部署 function app 在 Azure 中。

#### <a name="net"></a>.NET

下面的示例可用于创建你的 YAML 文件来构建.NET 应用程序。

```yaml
jobs:
  - job: Build
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

下面的示例可用于创建 YAML 文件以生成 JavaScript 应用程序：

```yaml
jobs:
  - job: Build
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

您可以使用下面的示例创建 YAML 文件构建 Python 应用，Linux Azure Functions 仅支持 Python:

```yaml
jobs:
  - job: Build
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>将应用部署

具体取决于宿主的 OS 中，您需要在 YAML 文件中包含以下 YAML 示例。

#### <a name="windows-function-app"></a>Windows 函数应用

可以使用以下代码片段将部署到 Windows 函数应用

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Linux 函数应用

可以使用以下代码片段将部署到 Linux 函数应用

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>基于模板的管道

Azure DevOps 中的模板是预定义的生成或部署应用程序的任务组。

### <a name="build-your-app"></a>构建你的应用程序

生成 Azure 管道中的应用取决于您的应用程序的编程语言。 每种语言有特定的生成步骤，以创建用于部署项目，可用于更新 Azure 中的函数应用。
若要使用的内置生成模板，创建新的生成管道时，选择**使用经典编辑器**以使用设计器模板创建一个管道

![Azure 管道经典编辑器](media/functions-how-to-azure-devops/classic-editor.png)

配置你的代码的源之后, 搜索 Azure Functions 生成模板，并选择与你的应用程序的语言相匹配的模板。

![Azure Functions 生成模板](media/functions-how-to-azure-devops/build-templates.png)

#### <a name="javascript-apps"></a>JavaScript 应用程序

如果 JavaScript 应用程序具有 Windows 本机模块依赖关系，您需要更新：

- 代理池版本到**托管 VS2017**

  ![更改生成代理 OS](media/functions-how-to-azure-devops/change-agent.png)

- 中的脚本**构建扩展**到模板中的步骤 `IF EXIST *.csproj dotnet build extensions.csproj --output ./bin`

  ![更改脚本](media/functions-how-to-azure-devops/change-script.png)

### <a name="deploy-your-app"></a>将应用部署

在创建新的发布管道，搜索 Azure Functions 发布模板。

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>创建使用 Azure CLI Azure 管道

使用`az functionapp devops-pipeline create`[命令](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)，会创建一个 Azure 管道以生成和发布你的存储库中的任何代码更改。 该命令将生成新的 YAML 文件，用于定义生成和发布管道，并将其提交到存储库。
此命令的必备组件取决于你的代码的位置：

- 如果你的代码是在 GitHub 中：

    - 需要能够**编写**到你的订阅的权限。

    - 你是在 Azure DevOps 项目管理员。

    - 您有权创建 GitHub 个人访问令牌具有足够的权限。 [GitHub PAT 权限要求。](https://aka.ms/azure-devops-source-repos)

    - 您有权提交到 GitHub 存储库提交自动生成的 YAML 文件中的主分支。

- 如果你的代码在 Azure 存储库：

    - 需要能够**编写**到你的订阅的权限。

    - 你是在 Azure DevOps 项目管理员。

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 概述](functions-overview.md)
+ [Azure DevOps 概述](/azure/devops/pipelines/)
