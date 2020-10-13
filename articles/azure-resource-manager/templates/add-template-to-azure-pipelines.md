---
title: 使用 Azure Pipelines 和模板实现 CI/CD
description: 介绍如何使用 Azure 资源管理器模板在 Azure Pipelines 中配置持续集成。 它演示了如何使用 PowerShell 脚本，或将文件复制到暂存位置并从该位置进行部署。
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 6784df30340e4c54b8b1d6e82b45046666824315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653394"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>将 ARM 模板与 Azure Pipelines 集成

可以将 Azure 资源管理器模板 (ARM 模板) 与 Azure Pipelines 进行持续集成和持续部署 (CI/CD) 集成。 教程 [与 Azure Pipelines 的 arm 模板持续集成](deployment-tutorial-pipeline.md) 演示了如何使用 [ARM 模板部署任务](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) 从 GitHub 存储库部署模板。 如果要直接从存储库部署模板，则可以使用此方法。

本文介绍了两种用 Azure Pipelines 部署模板的方法。 本文介绍以下操作：

* **添加用于运行 Azure PowerShell 脚本的任务**。 此选项的优势在于在整个开发生命周期中提供一致性，因为你可以使用运行本地测试时所用的同一脚本。 脚本将部署模板，但也可以执行其他操作，例如获取要用作参数的值。

   Visual Studio 提供了包含 PowerShell 脚本的 [Azure 资源组项目](create-visual-studio-deployment-project.md) 。 该脚本会将项目中的生成工件暂存到资源管理器可以访问的存储帐户。 生成工件是项目中的一些项，例如链接的模板、脚本和应用程序二进制文件。 如果要继续使用项目中的脚本，请使用本文中所述的 PowerShell 脚本任务。

* **添加用于复制和部署任务的任务**。 此选项可以方便地取代项目脚本。 在管道中配置两个任务。 一个任务将项目暂存到可访问的位置。 其他任务从该位置部署模板。

## <a name="prepare-your-project"></a>准备项目

本文假设你的 ARM 模板和 Azure DevOps 组织已准备好创建管道。 以下步骤说明如何确保准备就绪：

* 你已有一个 Azure DevOps 组织。 如果没有，请[免费创建一个](/azure/devops/pipelines/get-started/pipelines-sign-up)。 如果你的团队已创建了一个 Azure DevOps 组织，请确保你是要使用的 Azure DevOps 项目的管理员。

* 已配置与 Azure 订阅之间的[服务连接](/azure/devops/pipelines/library/connect-to-azure)。 管道中的任务将以服务主体的身份执行。 有关创建连接的步骤，请参阅[创建 DevOps 项目](deployment-tutorial-pipeline.md#create-a-devops-project)。

* 你有一个用于定义你的项目的基础结构的 [ARM 模板](quickstart-create-templates-use-visual-studio-code.md) 。

## <a name="create-pipeline"></a>创建管道

1. 如果事先尚未添加管道，需要创建一个新管道。 在 Azure DevOps 组织中，选择“管道”和“新建管道”。**** ****

   ![添加新管道](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. 指定代码的存储位置。 在下图中，选择的是“Azure Repos Git”。****

   ![选择代码源](./media/add-template-to-azure-pipelines/select-source.png)

1. 从该源中，选择包含项目代码的存储库。

   ![选择存储库](./media/add-template-to-azure-pipelines/select-repo.png)

1. 选择要创建的管道类型。 可以选择“初学者管道”。****

   ![选择管道](./media/add-template-to-azure-pipelines/select-pipeline.png)

现已准备好添加 Azure PowerShell 任务，或者复制文件并部署任务。

## <a name="azure-powershell-task"></a>Azure PowerShell 任务

本部分介绍如何使用一个在项目中运行 PowerShell 脚本的任务来配置持续部署。 如果需要部署模板的 PowerShell 脚本，请参阅 [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) 或 [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1)。

以下 YAML 文件将创建 [Azure PowerShell 任务](/azure/devops/pipelines/tasks/deploy/azure-powershell)：

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-Template.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

当您将任务设置为时 `AzurePowerShell@5` ，管道将使用 [Az 模块](/powershell/azure/new-azureps-module-az)。 如果要在脚本中使用 AzureRM 模块，请将任务设置为 `AzurePowerShell@3` 。

```yaml
steps:
- task: AzurePowerShell@3
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

在中 `ScriptArguments` ，提供脚本所需的任何参数。 下面的示例演示脚本的一些参数，但需要为脚本自定义参数。

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

选择“保存”时，生成管道将自动运行。**** 返回生成管道的摘要并观察状态。

![查看结果](./media/add-template-to-azure-pipelines/view-results.png)

可以选择当前正在运行的管道来查看有关任务的详细信息。 管道运行完成后，你将看到每个步骤的结果。

## <a name="copy-and-deploy-tasks"></a>复制并部署任务

本部分演示如何使用两个任务配置连续部署。 第一个任务将项目暂存到存储帐户，第二个任务部署模板。

若要将文件复制到存储帐户，必须为服务连接的服务主体分配存储 Blob 数据参与者或存储 Blob 数据所有者角色。 有关详细信息，请参阅 [AzCopy 入门](../../storage/common/storage-use-azcopy-v10.md)。

以下 YAML 显示 [Azure 文件复制任务](/azure/devops/pipelines/tasks/deploy/azure-file-copy)。

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

需要根据环境修改此任务的多个部分。 `SourcePath` 指示生成工件相对于管道文件的位置。

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

创建复制文件任务后，可以添加任务来部署暂存模板。

以下 YAML 显示了 [Azure 资源管理器模板部署任务](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)：

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

要更详细地查看此任务的几个部分。

- `deploymentScope`：从选项中选择部署范围： `Management Group` 、 `Subscription` 和 `Resource Group` 。 若要详细了解范围，请参阅[部署范围](deploy-rest.md#deployment-scope)。

- `azureResourceManagerConnection`：提供创建的服务连接的名称。

- `subscriptionId`：提供目标订阅 ID。 此属性仅适用于资源组部署范围和订阅部署范围。

- `resourceGroupName` 和 `location`：提供要部署到的资源组的名称和位置。 如果该资源组不存在，任务将创建该资源组。

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`：提供过渡模板的链接。 设置值时，请使用从文件复制任务返回的变量。 下面的示例链接到中名为 mainTemplate.js的模板。 包含名为 **templates** 的文件夹，这是因为文件复制任务将文件复制到的位置。 在管道中，提供模板的路径和模板的名称。

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

管道的外观如下所示：

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

选择“保存”时，生成管道将自动运行。**** 返回生成管道的摘要并观察状态。

## <a name="next-steps"></a>后续步骤

若要了解如何将 ARM 模板用于 GitHub 操作，请参阅 [使用 Github 操作部署 Azure 资源管理器模板](deploy-github-actions.md)。
