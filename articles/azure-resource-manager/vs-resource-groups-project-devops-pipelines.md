---
title: 使用 Azure 管道和资源管理器模板的 CI/CD
description: 介绍如何通过使用 Visual Studio 中的 Azure 资源组部署项目，将资源管理器模板部署设置 Azure 管道中的持续集成。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191455"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>将资源管理器模板与 Azure 管道集成

Visual Studio 提供了创建模板并将其部署到 Azure 订阅的 Azure 资源组项目。 可以将此项目与 Azure 管道集成的持续集成和持续部署 (CI/CD) 中。

有两种方法来使用 Azure 管道部署模板：

* **添加运行 Azure PowerShell 脚本的任务，** 。 此选项没有提供在整个开发生命周期的一致性，因为你使用 Visual Studio 项目 (Deploy-AzureResourceGroup.ps1) 中包含的脚本相同的优点。 脚本阶段中的项目在项目资源管理器可以访问的存储帐户。 项目是如链接的模板、 脚本和应用程序二进制文件在项目中的项。 然后，该脚本部署模板。

* **添加任务，以复制并将其部署任务**。 此选项提供了项目脚本到一个便捷替代方式。 在管道中配置两个任务。 一个任务暂存项目，并在另一任务部署模板。

本文介绍了这两种方法。

## <a name="prepare-your-project"></a>准备项目

本文假定你的 Visual Studio 项目和 Azure DevOps 的组织已经可供创建管道。 以下步骤演示如何确保准备就绪：

* 具有 Azure DevOps 组织。 如果你没有帐户，[免费创建一个](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)。 如果你的团队已有 Azure DevOps 组织，请确保你是你想要使用的 Azure DevOps 项目的管理员。

* 已配置[服务连接](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)到 Azure 订阅。 服务主体的标识下执行管道中的任务。 若要创建连接的步骤，请参阅[创建 DevOps 项目](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project)。

* 你有 Visual Studio 项目中创建**Azure 资源组**初学者模板。 有关创建该类型的项目的信息，请参阅[创建和部署通过 Visual Studio 的 Azure 资源组](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

* Visual Studio 项目[连接到 Azure DevOps 项目](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)。

## <a name="create-pipeline"></a>创建管道

1. 如果以前尚未添加管道，您需要创建新的管道。 从你的 Azure DevOps 组织，选择**管道**并**新管道**。

   ![添加新的管道](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. 指定你的代码的存储位置。 下图显示了选择**Azure 存储库 Git**。

   ![选择代码源](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. 从源中选择已为你的项目代码的存储库。

   ![选择存储库](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. 选择要创建管道的类型。 可以选择**初学者管道**。

   ![选择管道](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

现在即可将 Azure PowerShell 任务或复制文件添加和部署任务。

## <a name="azure-powershell-task"></a>Azure PowerShell 任务

本部分演示如何使用的单个任务，在项目中运行 PowerShell 脚本配置连续部署。 以下 YAML 文件将创建[Azure PowerShell 任务](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

将任务设置为`AzurePowerShell@3`，该管道使用 AzureRM 模块的命令进行身份验证连接。 默认情况下，Visual Studio 项目中的 PowerShell 脚本使用 AzureRM 模块。 如果你已更新您的脚本以使用[Az 模块](/powershell/azure/new-azureps-module-az)，将该任务设置为`AzurePowerShell@4`。

```yaml
steps:
- task: AzurePowerShell@4
```

有关`azureSubscription`，提供你创建的服务连接的名称。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

有关`scriptPath`，提供对你的脚本从管道文件的相对路径。 你可以查看你的存储库以查看的路径。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

如果您不需要为阶段的项目，只需传递的名称和要用于部署的资源组的位置。 Visual Studio 脚本会创建资源组，如果它尚不存在。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

如果你需要将项目阶段到现有的存储帐户，请使用：

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

现在，了解如何创建任务，让我们看一下用于编辑管道的步骤。

1. 打开你的管道，并替换在 YAML 内容：

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. 选择“保存”。 

   ![保存管道](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. 用于提交，提供一条消息，并直接提交到**主**。

1. 当选择**保存**，自动运行生成管道。 返回到摘要的生成管道，并观看状态。

   ![查看结果](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

您可以选择当前正在运行的管道，以查看任务的详细信息。 完成后，您将看到每个步骤的结果。

## <a name="copy-and-deploy-tasks"></a>复制并将其部署任务

本部分演示如何使用两个任务以暂存项目并将其部署该模板配置连续部署。 

下面的 YAML 演示[Azure 文件复制任务](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

有此任务可修改为您的环境的多个部分。 `SourcePath`指示相对于管道文件的项目的位置。 在此示例中，则文件存在于名为的文件夹`AzureResourceGroup1`是项目的名称。

```yaml
SourcePath: '<path-to-artifacts>'
```

有关`azureSubscription`，提供你创建的服务连接的名称。

```yaml
azureSubscription: '<your-connection-name>'
```

对于存储和容器名称，提供存储帐户和你想要用于存储项目的容器的名称。 存储帐户必须存在。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

下面的 YAML 演示[Azure 资源组部署任务](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

有此任务可修改为您的环境的多个部分。 有关`azureSubscription`，提供你创建的服务连接的名称。

```yaml
azureSubscription: '<your-connection-name>'
```

有关`resourceGroupName`和`location`，提供名称和你想要将部署到资源组的位置。 如果不存在，则任务会创建资源组。

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

部署任务都链接到一个名为模板`WebSite.json`和参数文件命名 WebSite.parameters.json。 使用模板和参数文件的名称。

现在，了解如何创建任务，让我们看一下用于编辑管道的步骤。

1. 打开你的管道，并替换在 YAML 内容：

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. 选择“保存”。 

1. 用于提交，提供一条消息，并直接提交到**主**。

1. 当选择**保存**，自动运行生成管道。 返回到摘要的生成管道，并观看状态。

   ![查看结果](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

您可以选择当前正在运行的管道，以查看任务的详细信息。 完成后，您将看到每个步骤的结果。

## <a name="next-steps"></a>后续步骤

有关与资源管理器模板配合使用 Azure 管道的分步过程，请参阅[教程：持续集成的 Azure 资源管理器模板与 Azure 管道](resource-manager-tutorial-use-azure-pipelines.md)。
