---
title: 具有 Azure Pipelines 和模板的 CI/CD
description: 介绍如何在 Visual Studio 中使用 Azure 资源组部署项目在 Azure Pipelines 中设置持续集成，以部署资源管理器模板。
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 51122e314ebd0a97647fc4026b1f49619950c351
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143750"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>将资源管理器模板与 Azure Pipelines 集成

Visual Studio 提供了 Azure 资源组项目用于创建模板并将其部署到 Azure 订阅。 可将此项目与 Azure Pipelines 集成，以实现持续集成和持续部署 (CI/CD)。

可通过两种方式在 Azure Pipelines 中部署模板：

* **添加用于运行 Azure PowerShell 脚本的任务**。 此选项的优势是可在整个开发生命周期中提供一致性，因为使用的脚本与 Visual Studio 项目中包含的脚本相同 (Deploy-AzureResourceGroup.ps1)。 该脚本会将项目中的生成工件暂存到资源管理器可以访问的存储帐户。 生成工件是项目中的一些项，例如链接的模板、脚本和应用程序二进制文件。 然后，该脚本将部署模板。

* **添加用于复制和部署任务的任务**。 此选项可以方便地取代项目脚本。 在管道中配置两个任务。 一个任务暂存生成工件，另一个任务部署模板。

本文介绍了这两种方法。

## <a name="prepare-your-project"></a>准备项目

本文假设 Visual Studio 项目和 Azure DevOps 组织已准备好创建管道。 以下步骤说明如何确保准备就绪：

* 你已有一个 Azure DevOps 组织。 如果没有，请[免费创建一个](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)。 如果你的团队已创建了一个 Azure DevOps 组织，请确保你是要使用的 Azure DevOps 项目的管理员。

* 已配置与 Azure 订阅之间的[服务连接](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)。 管道中的任务将以服务主体的身份执行。 有关创建连接的步骤，请参阅[创建 DevOps 项目](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project)。

* 已从 **Azure 资源组**初学者模板创建了一个 Visual Studio 项目。 有关创建此类项目的信息，请参阅[通过 Visual Studio 创建和部署 Azure 资源组](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

* Visual Studio 项目[已连接到 Azure DevOps 项目](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)。

## <a name="create-pipeline"></a>创建管道

1. 如果事先尚未添加管道，需要创建一个新管道。 在 Azure DevOps 组织中，选择“管道”和“新建管道”。

   ![添加新管道](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. 指定代码的存储位置。 在下图中，选择的是“Azure Repos Git”。

   ![选择代码源](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. 从该源中，选择包含项目代码的存储库。

   ![选择存储库](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. 选择要创建的管道类型。 可以选择“初学者管道”。

   ![选择管道](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

现已准备好添加 Azure PowerShell 任务，或者复制文件并部署任务。

## <a name="azure-powershell-task"></a>Azure PowerShell 任务

本部分介绍如何使用一个在项目中运行 PowerShell 脚本的任务来配置持续部署。 以下 YAML 文件将创建 [Azure PowerShell 任务](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)：

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

将该任务设置为 `AzurePowerShell@3` 时，管道将使用 AzureRM 模块中的命令对连接进行身份验证。 默认情况下，Visual Studio 项目中的 PowerShell 脚本使用 AzureRM 模块。 如果已将脚本更新为使用 [Az 模块](/powershell/azure/new-azureps-module-az)，请将任务设置为 `AzurePowerShell@4`。

```yaml
steps:
- task: AzurePowerShell@4
```

对于 `azureSubscription`，请提供创建的服务连接的名称。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

对于 `scriptPath`，请提供管道文件中脚本的相对路径。 可以在存储库中查看该路径。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

如果不需要暂存生成工件，则只需传递用于部署的资源组的名称和位置。 如果该资源组尚不存在，Visual Studio 脚本将创建该资源组。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

如果需要将生成工件暂存到现有存储帐户，请使用：

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

了解如何创建任务后，接下来让我们完成编辑管道的步骤。

1. 打开管道，并将内容替换为 YAML：

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

1. 提供要提交的消息，并直接提交到 **master**。

1. 选择“保存”时，生成管道将自动运行。 返回生成管道的摘要并观察状态。

   ![查看结果](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

可以选择当前正在运行的管道来查看有关任务的详细信息。 管道运行完成后，你将看到每个步骤的结果。

## <a name="copy-and-deploy-tasks"></a>复制并部署任务

本部分介绍如何配置持续部署：使用两个任务来暂存生成工件并部署模板。

以下 YAML 显示了 [Azure 文件复制任务](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)：

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

需要根据环境修改此任务的多个部分。 `SourcePath` 指示生成工件相对于管道文件的位置。 在此示例中，文件位于名为 `AzureResourceGroup1`（也是项目名称）的文件夹中。

```yaml
SourcePath: '<path-to-artifacts>'
```

对于 `azureSubscription`，请提供创建的服务连接的名称。

```yaml
azureSubscription: '<your-connection-name>'
```

对于存储和容器名称，请提供用于存储生成工件的存储帐户和容器的名称。 该存储帐户必须存在。

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

以下 YAML 显示[Azure 资源管理器模板部署任务](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)：

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

需要根据环境修改此任务的多个部分。

- `deploymentScope`：从选项中选择部署范围： `Management Group`、`Subscription` 和 `Resource Group`。 在此演练中使用**资源组**。 若要详细了解范围，请参阅[部署范围](./resource-group-template-deploy-rest.md#deployment-scope)。

- `ConnectedServiceName`：提供所创建的服务连接的名称。

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`：提供目标订阅 ID。 此属性仅适用于资源组部署范围和订阅部署优惠。

- `resourceGroupName` 和 `location`：提供要部署到的资源组的名称和位置。 如果该资源组不存在，任务将创建该资源组。

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

部署任务链接到名为 `WebSite.json` 的模板以及名为 WebSite.parameters.json 的参数文件。 请使用模板和参数文件的名称。

了解如何创建任务后，接下来让我们完成编辑管道的步骤。

1. 打开管道，并将内容替换为 YAML：

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
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. 选择“保存”。

1. 提供要提交的消息，并直接提交到 **master**。

1. 选择“保存”时，生成管道将自动运行。 返回生成管道的摘要并观察状态。

   ![查看结果](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

可以选择当前正在运行的管道来查看有关任务的详细信息。 管道运行完成后，你将看到每个步骤的结果。

## <a name="next-steps"></a>后续步骤

有关将 Azure Pipelines 与资源管理器模板结合使用的分步过程，请参阅[教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板](resource-manager-tutorial-use-azure-pipelines.md)。
