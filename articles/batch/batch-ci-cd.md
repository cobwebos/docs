---
title: 使用 Azure 管道以生成并部署 HPC 解决方案-Azure Batch |Microsoft Docs
description: 了解如何部署 Azure Batch 上运行的 HPC 应用程序的生成/发布管道。
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880763"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure 管道来生成和部署的 HPC 解决方案

Azure DevOps 服务提供一系列的开发团队构建自定义应用程序时使用的工具。 提供的 Azure DevOps 工具可顺利转换到自动生成和测试的高性能计算解决方案。 本文演示如何设置 Azure 管道，以了解高性能计算解决方案部署在 Azure Batch 上的持续集成 (CI) 和持续部署 (CD) 使用。

Azure 管道用于生成、 部署、 测试和监视软件提供了一系列新式 CI/CD 过程。 这些进程加速软件交付，从而可以专注于你的代码，而不是支持基础结构和操作。

## <a name="create-an-azure-pipeline"></a>创建 Azure 的管道

在此示例中，我们将创建生成和发布管道来部署 Azure Batch 基础结构并发布应用程序包。 假设本地开发代码，这是常规部署流：

![在我们的管道中显示的部署流关系图](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>设置

若要按照本文中的步骤，需要 Azure DevOps 组织和团队项目。

* [创建 Azure DevOps 组织](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Azure DevOps 中创建项目](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>你的环境的源代码管理

源代码管理可让团队能够跟踪对基本代码所做的更改，以查看以前版本的代码。

通常情况下，源代码管理是认为的手中现有软件代码。 如何在底层基础结构？ 这使我们将基础结构即代码，其中我们将使用 Azure 资源管理器模板或其他开放源代码的备选方法来以声明方式定义我们的底层基础结构。

此示例很大程度依赖的资源管理器模板 （JSON 文档） 和现有的二进制文件数。 可以将这些示例复制到你的存储库，并将它们推送到 Azure DevOps。

此示例中使用的基本代码结构类似于以下操作：

* **Arm 模板**包含多个 Azure 资源管理器模板的文件夹。 本文介绍了这些模板。
* 一个**客户端应用程序**文件夹中，这是副本的[Azure 批处理.NET 文件处理通过 ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)示例。 这不是需要这篇文章。
* **Hpc 应用程序**文件夹中，这是 Windows 64 位版本的[ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)。
* 一个**管道**文件夹。 这包含大纲显示我们生成过程的 YAML 文件。 本文对此进行了讨论。

本部分假设你熟悉版本控制和设计资源管理器模板。 如果您不熟悉这些概念，请参阅以下页面了解详细信息。

* [什么是源代码管理？](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

此示例利用多个资源管理器模板来部署我们的解决方案。 若要执行此操作，我们使用功能的模板数 （类似于单元或模块） 实现特定的一项功能。 我们还使用它负责将这些基础功能在一起的端到端解决方案模板。 有几个为这种方法带来的好处：

* 基础功能模板可以单独进行单元测试。
* 基础功能模板可以定义为在组织内的标准，并在多个解决方案中重用。

对于此示例中，没有用于部署三个模板的端到端解决方案模板 (deployment.json)。 基础模板是已功能的模板，负责部署解决方案的特定方面。

![使用 Azure 资源管理器模板的链接模板结构示例](media/batch-ci-cd/ARMTemplateHierarchy.png)

第一个模板，我们将介绍适用于 Azure 存储帐户。 我们的解决方案需要应用程序部署到我们的 Batch 帐户的存储帐户。 值得注意的是[Microsoft.Storage 资源类型的资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)构建资源管理器模板的存储帐户时。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

接下来，我们将介绍 Azure Batch 帐户模板。 Azure Batch 帐户充当一个平台，用于运行大量应用程序池 （的机分组）。 值得注意的是[Microsoft.Batch 资源类型的资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)生成资源管理器模板的 Batch 帐户时。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

下一个模板显示示例创建 Azure Batch 池 （处理我们的应用程序的后端计算机）。 值得注意的是[Microsoft.Batch 资源类型的资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)时为 Batch 帐户池构建资源管理器模板。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

最后，我们有作用类似于业务流程协调程序的模板。 此模板将负责部署功能模板。

此外可以有关查找更多信息[创建链接的 Azure 资源管理器模板](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md)中单独的文章。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>HPC 解决方案

可以定义为代码和共置在相同的存储库的基础结构和软件。

对于此解决方案，ffmpeg 用作应用程序包。 可以下载 ffmpeg 包[此处](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)。

![Git 存储库结构示例](media/batch-ci-cd/git-repository.jpg)

有四个主要部分到此存储库：

* **Arm 模板**将我们的基础结构存储为代码的文件夹
* **Hpc 应用程序**包含 ffmpeg 的二进制文件的文件夹
* **管道**包含为我们生成的管道定义的文件夹。
* 可选：**客户端应用程序**将存储为.NET 应用程序代码的文件夹。 我们不要使用这在示例中，但在您自己的项目，你可能想要执行的 HPC 批处理应用程序通过客户端应用程序的运行。

> [!NOTE]
> 这只是一个示例结构的代码库。 这种方法用于演示应用程序、 基础架构和管道代码存储在同一个存储库的目的。

现在，设置的源代码，我们可以开始第一次生成。

## <a name="continuous-integration"></a>持续集成

[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)内 Azure DevOps 服务，, 可帮助你实现用于你的应用程序的生成、 测试和部署管道。

在管道的此阶段，测试通常运行验证代码和生成软件的相应部分。 数量和类型的测试和运行的任何其他任务将取决于您更广的生成和发布策略。

## <a name="preparing-the-hpc-application"></a>准备好 HPC 应用程序

在此示例中，我们将重点**hpc 应用程序**文件夹。 **Hpc 应用程序**文件夹是将从运行在 Azure Batch 帐户的 ffmpeg 软件。

1. 导航到 Azure 管道的 Azure DevOps 组织中的生成部分。 创建**新的管道**。

    ![创建新的生成管道](media/batch-ci-cd/new-build-pipeline.jpg)

1. 必须创建一个生成管道的两个选项：

    a. [使用可视化设计器](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。 为此，单击"使用可视化设计器"**新的管道**页。

    b. [使用 YAML 生成](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 可以通过单击 Azure 存储库或新的管道页上的 GitHub 选项创建新的 YAML 管道。 或者，可以在您的源代码管理中存储下面的示例中，并通过可视化设计器中，单击，然后使用 YAML 模板引用现有的 YAML 文件。

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. 生成配置后根据需要选择**保存并排队**。 如果已启用的持续集成 (在**触发器**部分)，进行新的提交到存储库时，会自动触发生成，在生成中满足条件设置。

    ![现有的生成管道的示例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 通过导航到 Azure DevOps 中生成的进度查看实时更新**生成**Azure 管道的一部分。 从你的生成定义中选择适当的版本。

    ![从生成查看实时输出](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果使用客户端应用程序来执行 HPC Batch 应用程序，您需要创建该应用程序的单独的生成定义。 您可以找到操作方法指南中的大量[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)文档。

## <a name="continuous-deployment"></a>连续部署

Azure 管道还用于部署应用程序和底层基础结构。 [发布管道](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops)是启用持续部署和自动执行发布过程的组件。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>部署应用程序和基础结构

有多个步骤涉及在部署基础结构。 因为我们用[链接模版](../azure-resource-manager/resource-group-linked-templates.md)，这些模板将需要从公共终结点 （HTTP 或 HTTPS） 进行访问。 这可能是 GitHub 上的存储库或 Azure Blob 存储帐户或另一个存储位置。 已上传的模板项目可保持安全，因为它们可以保存在专用模式中，但访问使用某种形式的共享的访问签名 (SAS) 令牌。 下面的示例演示如何部署具有 Azure 存储 blob 中的模板的基础结构。

1. 创建**新发布定义**，并选择一个空定义。 然后，我们需要新创建的环境重命名为我们的管道与相关的内容。

    ![初始发布管道](media/batch-ci-cd/Release-0.jpg)

1. 若要获得我们的 HPC 应用程序输出的生成管道上创建依赖关系。

    > [!NOTE]
    > 再次重申，请注意**源别名**，因为这将需要在发布定义内创建任务时。

    ![在相应的生成管道中创建的项目链接到 HPCApplicationPackage](media/batch-ci-cd/Release-1.jpg)

1. 创建指向另一个项目的这一次，Azure 存储库。 这需要访问存储在存储库中的资源管理器模板。 因为资源管理器模板不需要编译，不需要将它们通过生成管道。

    > [!NOTE]
    > 再次重申，请注意**源别名**，因为这将需要在发布定义内创建任务时。

    ![创建指向 Azure 存储库的项目链接](media/batch-ci-cd/Release-2.jpg)

1. 导航到**变量**部分。 建议在管道中，创建多个变量，因此不输入到多个任务的相同信息。 这些是此示例中，以及它们如何影响部署中使用的变量。

    * **applicationStorageAccountName**:存储帐户来保存 HPC 应用程序二进制文件的名称
    * **batchAccountApplicationName**:在 Azure Batch 帐户中的应用程序名称
    * **batchAccountName**:Azure Batch 帐户的名称
    * **batchAccountPoolName**:执行处理的 Vm 的池名称
    * **batchApplicationId**:Azure Batch 应用程序的唯一 ID
    * **batchApplicationVersion**:语义版本的 batch 应用程序 （即，ffmpeg 二进制文件）
    * **location**：要部署 Azure 资源的位置
    * **resourceGroupName**:要创建的资源组的名称和在其中部署你的资源
    * **storageAccountName**：若要保存链接的资源管理器模板的存储帐户的名称

    ![对于 Azure 管道版本设置变量的示例](media/batch-ci-cd/Release-4.jpg)

1. 导航到开发环境的任务。 在以下快照，可以看到六个任务。 这些任务将： 下载的 ffmpeg zip 格式的文件，存储帐户来托管嵌套的资源管理器模板、 将这些资源管理器模板复制到存储帐户、 部署 batch 帐户和必需的依赖项、 创建中的应用程序部署Azure Batch 帐户和上传到 Azure Batch 帐户的应用程序包中。

    ![用于发布到 Azure Batch HPC 应用程序的任务的示例](media/batch-ci-cd/Release-3.jpg)

1. 添加**下载管道项目 （预览版）** 任务并设置以下属性：
    * **显示名称：** ApplicationPackage 下载到代理
    * **要下载的项目的名称：** hpc 应用程序
    * **若要下载到的路径**: $(System.DefaultWorkingDirectory)

1. 创建一个存储帐户来存储你的项目。 可以使用该解决方案中的现有存储帐户，但对于自包含的示例和内容的隔离，我们正在对我们的项目 （特别是资源管理器模板） 进行的专用的存储帐户。

    添加**Azure 资源组部署**任务并设置以下属性：
    * **显示名称：** 为资源管理器模板部署存储帐户
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **操作**：创建或更新资源组
    * **资源组**: $(resourceGroupName)
    * **位置**: $(location)
    * **模板**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **替代模板参数**:-accountName $(storageAccountName)

1. 将从源代码管理项目上传到存储帐户。 没有要执行此操作的 Azure 管道的任务。 作为此任务的一部分，存储帐户容器 URL 和 SAS 令牌可以输出到 Azure 管道中的变量。 这意味着整个此代理阶段可以重复使用。

    添加**Azure 文件复制**任务并设置以下属性：
    * **源：** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates /
    * **Azure 连接类型**:Azure 资源管理器
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **目标类型**:Azure Blob
    * **RM 存储帐户**: $(storageAccountName)
    * **容器名称**： 模板
    * **存储容器 URI**: templateContainerUri
    * **存储容器 SAS 令牌**: templateContainerSasToken

1. 部署业务流程协调程序模板。 回想一下前面的业务流程协调程序模板，你会注意到了存储帐户的容器 URL，除了 SAS 令牌的参数。 您应注意到 Resource Manager 模板中所需的变量可以保存在变量部分中的发布定义中，或从另一个 Azure 管道任务 （例如，Azure Blob 复制任务的一部分） 设置。

    添加**Azure 资源组部署**任务并设置以下属性：
    * **显示名称：** 部署 Azure Batch
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **操作**：创建或更新资源组
    * **资源组**: $(resourceGroupName)
    * **位置**: $(location)
    * **模板**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **替代模板参数**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

一种常见做法是使用 Azure 密钥保管库任务。 如果服务主体 （连接到 Azure 订阅） 的设置适当的访问策略，它可以从 Azure 密钥保管库下载机密，并使用为你的管道中的变量。 将与相关联的值设置的机密的名称。 例如，可以使用 $(sshPassword) 发布定义中引用 sshPassword 的机密。

1. 接下来的步骤调用 Azure CLI。 第一个用于在 Azure Batch 中创建应用程序。 并上传相关联的包。

    添加**Azure CLI**任务并设置以下属性：
    * **显示名称：** 在 Azure Batch 帐户中创建应用程序
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **脚本位置**:内联脚本
    * **内联脚本**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 第二个步骤用于将相关联的包上传到应用程序。 在本例中，将 ffmpeg 文件。

    添加**Azure CLI**任务并设置以下属性：
    * **显示名称：** 将包上载到 Azure Batch 帐户
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **脚本位置**:内联脚本
    * **内联脚本**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 应用程序包的版本编号设置为一个变量。 如果覆盖以前版本适用于你，以及如果你想要手动控制包推送到 Azure Batch 的版本号，这是包的非常方便。

1. 通过选择创建新的发布**发布 > 创建新的发布**。 触发后，选择指向新版本以查看状态的链接。

1. 可以通过选择查看代理的实时输出**日志**您的环境下的按钮。

    ![查看你的发布的状态](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>测试环境

一旦设置好环境，请确认以下测试可以成功完成。

连接到新的 Azure Batch 帐户，使用 Azure CLI 从 PowerShell 命令提示符。

* 登录到 Azure 帐户和`az login`并按照说明进行身份验证。
* 现在对 Batch 帐户进行身份验证： `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的应用程序

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>检查池有效

```azurecli
az batch pool list
```

记下的值`currentDedicatedNodes`此命令输出中。 在下一个测试中调整此值。

#### <a name="resize-the-pool"></a>将池大小调整

将池大小调整，使计算节点可用于作业和任务测试，请与池列表命令来查看当前状态，直到重设大小已完成并且没有可用的节点

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>后续步骤

除了本文中，有两个利用 ffmpeg，使用.NET 和 Python 的教程。 有关如何与 Batch 帐户，通过简单的应用程序进行交互，请参阅这些教程，详细信息。

* [通过使用 Python API 的 Azure Batch 运行并行工作负荷](tutorial-parallel-python.md)
* [通过使用.NET API 的 Azure Batch 运行并行工作负荷](tutorial-parallel-dotnet.md)
