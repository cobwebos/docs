---
title: 具有 Azure Pipelines 和模板的 CI/CD
description: 介绍如何使用 Visual Studio 中的 Azure 资源组部署项目在 Azure Pipelines 中设置持续集成，以部署资源管理器模板。
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6f5d4846d32b4880ccd3fbd82f062f57948ac15a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478261"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>将资源管理器模板与 Azure Pipelines 集成

Visual Studio 提供用于创建模板并将其部署到 Azure 订阅的 Azure 资源组项目。 可以将此项目与 Azure Pipelines 集成，以实现持续集成和持续部署（CI/CD）。

可以通过两种方法部署具有 Azure Pipelines 的模板：

* **添加运行 Azure PowerShell 脚本的任务**。 此选项的优势在于在整个开发生命周期中提供一致性，因为你使用的脚本与 Visual Studio 项目中包含的脚本（Deploy-azureresourcegroup.ps1）相同。 脚本阶段会将项目中的项目投影到资源管理器可以访问的存储帐户。 项目是项目中的项，如链接的模板、脚本和应用程序二进制文件。 然后，该脚本将部署模板。

* **添加任务以复制和部署任务**。 使用此选项可以方便地替代项目脚本。 在管道中配置两个任务。 一个任务阶段，项目和其他任务部署模板。

本文介绍了这两种方法。

## <a name="prepare-your-project"></a>准备项目

本文假设你的 Visual Studio 项目和 Azure DevOps 组织已准备好创建管道。 以下步骤说明如何确保已准备就绪：

* 你有一个 Azure DevOps 组织。 如果没有，请[免费创建一个](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)。 如果你的团队已有一个 Azure DevOps 组织，请确保你是想要使用的 Azure DevOps 项目的管理员。

* 你已配置了与你的 Azure 订阅的[服务连接](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)。 管道中的任务在服务主体的标识下执行。 有关创建连接的步骤，请参阅[创建 DevOps 项目](template-tutorial-use-azure-pipelines.md#create-a-devops-project)。

* 你有一个从**Azure 资源组**初学者模板创建的 Visual Studio 项目。 有关创建该类型的项目的信息，请参阅[通过 Visual Studio 创建和部署 Azure 资源组](create-visual-studio-deployment-project.md)。

* 你的 Visual Studio 项目已[连接到 Azure DevOps 项目](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)。

## <a name="create-pipeline"></a>创建管道

1. 如果之前尚未添加管道，则需要创建新的管道。 在 Azure DevOps 组织中，选择 "**管道**" 和 "**新建管道**"。

   ![添加新管道](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 指定存储代码的位置。 下图显示了选择**Azure Repos Git**。

   ![选择代码源](./media/add-template-to-azure-pipelines/select-source.png)

1. 从该源中，选择包含项目代码的存储库。

   ![选择存储库](./media/add-template-to-azure-pipelines/select-repo.png)

1. 选择要创建的管道的类型。 你可以选择 " **Starter 管道**"。

   ![选择管道](./media/add-template-to-azure-pipelines/select-pipeline.png)

你已准备好添加 Azure PowerShell 任务或 "复制文件" 和 "部署任务"。

## <a name="azure-powershell-task"></a>Azure PowerShell 任务

本部分演示如何使用在项目中运行 PowerShell 脚本的单个任务配置连续部署。 下面的 YAML 文件创建[Azure PowerShell 任务](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)：

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

将任务设置为 "`AzurePowerShell@3`" 时，管道使用 AzureRM 模块中的命令对连接进行身份验证。 默认情况下，Visual Studio 项目中的 PowerShell 脚本使用 AzureRM 模块。 如果已将脚本更新为使用[Az 模块](/powershell/azure/new-azureps-module-az)，请将任务设置为 `AzurePowerShell@4`。

```yaml
steps:
- task: AzurePowerShell@4
```

对于 "`azureSubscription`"，请提供创建的服务连接的名称。

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

对于 `scriptPath`，请提供从管道文件到脚本的相对路径。 可以在存储库中查看路径。

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

如果不需要暂存项目，只需传递要用于部署的资源组的名称和位置。 如果资源组尚不存在，Visual Studio 脚本会创建该资源组。

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

如果需要将项目暂存到现有存储帐户，请使用：

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

现在，您已了解如何创建任务，接下来让我们完成编辑管道的步骤。

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

   ![保存管道](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. 提供提交消息，并直接提交给**master**。

1. 选择 "**保存**" 时，生成管道将自动运行。 返回生成管道的摘要，并观察状态。

   ![查看结果](./media/add-template-to-azure-pipelines/view-results.png)

您可以选择当前正在运行的管道，以查看有关任务的详细信息。 完成后，会看到每个步骤的结果。

## <a name="copy-and-deploy-tasks"></a>复制和部署任务

本部分演示如何通过使用两个任务暂存项目并部署模板来配置连续部署。

以下 YAML 显示[Azure 文件复制任务](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)：

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

要针对你的环境修改此任务的几个部分。 `SourcePath` 指示相对于管道文件的项目的位置。 在此示例中，文件位于名为 `AzureResourceGroup1` 的文件夹中，该文件夹是项目的名称。

```yaml
SourcePath: '<path-to-artifacts>'
```

对于 "`azureSubscription`"，请提供创建的服务连接的名称。

```yaml
azureSubscription: '<your-connection-name>'
```

对于 "存储" 和 "容器名称"，提供要用于存储项目的存储帐户和容器的名称。 存储帐户必须存在。

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

要针对你的环境修改此任务的几个部分。

- `deploymentScope`：从选项中选择部署范围： `Management Group`、`Subscription` 和 `Resource Group`。 在此演练中使用**资源组**。 若要详细了解范围，请参阅[部署范围](deploy-rest.md#deployment-scope)。

- `ConnectedServiceName`：提供所创建的服务连接的名称。

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`：提供目标订阅 ID。 此属性仅适用于资源组部署范围和订阅部署范围。

- `resourceGroupName` 和 `location`：提供要部署到的资源组的名称和位置。 如果资源组不存在，任务会创建该资源组。

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

部署任务链接到名为 `WebSite.json` 的模板和名为 "网站" 的参数文件。 使用模板和参数文件的名称。

现在，您已了解如何创建任务，接下来让我们完成编辑管道的步骤。

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

1. 提供提交消息，并直接提交给**master**。

1. 选择 "**保存**" 时，生成管道将自动运行。 返回生成管道的摘要，并观察状态。

   ![查看结果](./media/add-template-to-azure-pipelines/view-results.png)

您可以选择当前正在运行的管道，以查看有关任务的详细信息。 完成后，会看到每个步骤的结果。

## <a name="next-steps"></a>后续步骤

有关将 Azure Pipelines 与资源管理器模板结合使用的分步过程，请参阅[教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板](template-tutorial-use-azure-pipelines.md)。
