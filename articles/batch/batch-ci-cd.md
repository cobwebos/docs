---
title: 使用 Azure 管道生成&部署 HPC 解决方案 - Azure 批处理
description: 了解如何为在 Azure Batch 上运行的 HPC 应用程序部署生成/发布管道。
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533124"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure 管道构建和部署 HPC 解决方案

Azure DevOps 服务提供开发团队在构建自定义应用程序时使用的一系列工具。 Azure DevOps 提供的工具可以转换为高性能计算解决方案的自动构建和测试。 本文演示如何使用 Azure 管道为 Azure Batch 上部署的高性能计算解决方案设置连续集成 （CI） 和连续部署 （CD）。

Azure 管道提供了一系列用于构建、部署、测试和监视软件的现代 CI/CD 流程。 这些过程可加快软件交付速度，使您能够专注于代码，而不是支持基础结构和操作。

## <a name="create-an-azure-pipeline"></a>创建 Azure 管道

在此示例中，我们将创建一个生成和发布管道来部署 Azure Batch 基础结构并发布应用程序包。 假设代码是在本地开发的，这是常规部署流：

![显示管道中部署流的图表](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>设置

要按照本文中的步骤操作，您需要 Azure DevOps 组织和团队项目。

* [创建 Azure 开发人员组织](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [在 Azure 开发人员计划中创建项目](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>环境的源代码管理

源代码管理允许团队跟踪对代码库所做的更改，并检查代码的早期版本。

通常，源代码控制被认为是与软件代码携手并进。 底层基础架构如何？ 这让我们将基础结构作为代码，我们将使用 Azure 资源管理器模板或其他开源替代方法来声明定义我们的底层基础结构。

此示例严重依赖许多资源管理器模板 （JSON 文档） 和现有的二进制文件。 您可以将这些示例复制到存储库中，并将它们推送到 Azure DevOps。

此示例中使用的代码库结构类似于以下内容;

* 包含许多 Azure 资源管理器模板的**臂部模板**文件夹。 本文将介绍这些模板。
* **客户端应用程序**文件夹，它是[Azure Batch .NET 文件处理的副本，包含 ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)示例。 本文不需要这样做。
* **hpc 应用程序**文件夹，它是[ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)的 Windows 64 位版本。
* **管道**文件夹。 其中包含一个 YAML 文件，概述了我们的生成过程。 本文将讨论这一点。

本节假定您熟悉版本控制和设计资源管理器模板。 如果您不熟悉这些概念，请参阅以下页面了解更多信息。

* [什么是源代码管理？](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

此示例利用多个资源管理器模板来部署我们的解决方案。 为此，我们使用许多实现特定功能的功能功能（类似于单元或模块）的功能模板。 我们还使用端到端解决方案模板，该模板负责将这些底层功能整合在一起。 这种方法有几个好处：

* 基础功能模板可以单独进行单元测试。
* 基础功能模板可以定义为组织内部的标准，并在多个解决方案中重用。

在此示例中，有一个端到端解决方案模板 （deployment.json） 部署三个模板。 基础模板是功能模板，负责部署解决方案的特定方面。

![使用 Azure 资源管理器模板链接模板结构的示例](media/batch-ci-cd/ARMTemplateHierarchy.png)

我们将查看的第一个模板是 Azure 存储帐户。 我们的解决方案需要存储帐户才能将应用程序部署到我们的批处理帐户。 在为存储帐户构建资源管理器模板时，值得了解[Microsoft.存储资源类型的资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)。

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

接下来，我们将查看 Azure 批处理帐户模板。 Azure 批处理帐户充当跨池（计算机分组）运行大量应用程序的平台。 在为批处理帐户构建资源管理器模板时，值得了解 Microsoft 的[资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

下一个模板显示了创建 Azure 批处理池的示例（用于处理应用程序的后端计算机）。 在为批处理帐户池构建资源管理器模板时，值得了解 Microsoft 的[资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

最后，我们有一个类似于协调器的模板。 此模板负责部署功能模板。

您还可以在单独的文章中了解有关[创建链接的 Azure 资源管理器模板](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md)的更多信息。

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

基础结构和软件可以定义为代码，并位于同一存储库中。

对于此解决方案，ffmpeg 用作应用程序包。 ffmpeg 软件包可以[在这里](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)下载。

![示例 Git 存储库结构](media/batch-ci-cd/git-repository.jpg)

此存储库有四个主要部分：

* 将基础结构存储为代码**的手臂模板**文件夹
* 包含 ffmpeg 二进制文件的**hpc 应用程序**文件夹
* 包含生成**管道定义的管道**文件夹。
* **可选**：将存储 .NET 应用程序代码的**客户端应用程序**文件夹。 我们不会在示例中使用，但在您自己的项目中，您可能希望通过客户端应用程序执行 HPC 批处理应用程序的运行。

> [!NOTE]
> 这只是代码库结构的一个示例。 此方法用于演示应用程序、基础结构和管道代码存储在同一存储库中的目的。

现在，源代码已经设置，我们可以开始第一个生成。

## <a name="continuous-integration"></a>持续集成

[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)（在 Azure DevOps 服务中） 可帮助您为应用程序实现生成、测试和部署管道。

在管道的此阶段，通常会运行测试以验证代码并构建软件的适当部分。 测试的数量和类型以及运行的任何其他任务将取决于更广泛的生成和发布策略。

## <a name="preparing-the-hpc-application"></a>准备 HPC 应用程序

在此示例中，我们将重点介绍**hpc 应用程序**文件夹。 **hpc 应用程序**文件夹是从 Azure Batch 帐户中运行的 ffmpeg 软件。

1. 导航到 Azure DevOps 组织中 Azure 管道的生成部分。 创建新**管道**。

    ![创建新生成管道](media/batch-ci-cd/new-build-pipeline.jpg)

1. 创建生成管道有两个选项：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 [使用可视化设计器](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。 要使用此，请单击 **"新建管道**"页上的"使用可视化设计器"。

    b.保留“数据库类型”设置，即设置为“共享”。 [使用 YAML 生成](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 您可以通过单击"新建管道"页上的 Azure Repos 或 GitHub 选项创建新的 YAML 管道。 或者，您可以通过单击 Visual 设计器，然后使用 YAML 模板，将下面的示例存储在源代码管理中并引用现有的 YAML 文件。

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

1. 根据需要配置生成后，选择 **"保存&队列**"。 如果启用了连续集成（在**触发器**部分中），则生成将在对存储库进行新提交时自动触发，从而满足生成中设置的条件。

    ![现有生成管道的示例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 通过导航到 Azure 管道的 **"生成"** 部分，在 Azure DevOps 中查看生成进度的实时更新。 从生成定义中选择适当的生成。

    ![查看生成的实时输出](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果使用客户端应用程序执行 HPC 批处理应用程序，则需要为该应用程序创建单独的生成定义。 您可以在[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)文档中找到许多操作指南。

## <a name="continuous-deployment"></a>连续部署

Azure 管道还用于部署应用程序和基础基础结构。 [发布管道](https://docs.microsoft.com/azure/devops/pipelines/release)是支持持续部署并自动执行发布过程的组件。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>部署应用程序和底层基础结构

部署基础结构涉及许多步骤。 由于我们使用[链接模板](../azure-resource-manager/templates/linked-templates.md)，这些模板需要从公共终结点 （HTTP 或 HTTPS） 访问。 这可能是 GitHub 上的存储库、Azure Blob 存储帐户或其他存储位置。 上载的模板项目可以保持安全，因为它们可以在私有模式下保留，但使用某种形式的共享访问签名 （SAS） 令牌进行访问。 下面的示例演示如何使用 Azure 存储 Blob 中的模板部署基础结构。

1. 创建**新版本定义**，然后选择空定义。 然后，我们需要将新创建的环境重命名为与管道相关的环境。

    ![初始发布管道](media/batch-ci-cd/Release-0.jpg)

1. 创建对生成管道的依赖项，以获得 HPC 应用程序的输出。

    > [!NOTE]
    > 再次，请注意**源别名**，因为当在发布定义中创建任务时，将需要这样做。

    ![在适当的生成管道中创建到 HPC 应用程序包的项目链接](media/batch-ci-cd/Release-1.jpg)

1. 创建指向另一个项目的链接，这次是 Azure 存储库。 这是访问存储库中存储的资源管理器模板所必需的。 由于资源管理器模板不需要编译，因此无需通过生成管道推送它们。

    > [!NOTE]
    > 再次，请注意**源别名**，因为当在发布定义中创建任务时，将需要这样做。

    ![创建到 Azure 存储库的项目链接](media/batch-ci-cd/Release-2.jpg)

1. 导航到**变量**部分。 建议在管道中创建多个变量，因此您不会将相同的信息输入多个任务。 这些是本示例中使用的变量，以及它们如何影响部署。

    * **应用程序存储帐户名称**：存储帐户的名称，以保存 HPC 应用程序二进制文件
    * **批处理帐户应用程序名称**：Azure 批处理帐户中的应用程序名称
    * **批处理帐户名称**：Azure 批处理帐户的名称
    * **批处理帐户池名称**：执行处理的 VM 池的名称
    * **批处理应用程序 Id**：Azure 批处理应用程序的唯一 ID
    * **批处理应用程序版本**：批处理应用程序的语义版本（即 ffmpeg 二进制文件）
    * **位置**：要部署的 Azure 资源的位置
    * **资源组名称**：要创建的资源组的名称，以及资源的部署位置
    * **存储帐户名称**：存储帐户的名称，用于保存链接的资源管理器模板

    ![为 Azure 管道版本设置的变量示例](media/batch-ci-cd/Release-4.jpg)

1. 导航到开发人员环境的任务。 在下面的快照中，您可以看到六个任务。 这些任务将：下载压缩的 ffmpeg 文件、部署存储帐户以承载嵌套资源管理器模板、将这些资源管理器模板复制到存储帐户、部署批处理帐户和所需的依赖项、在Azure 批处理帐户并将应用程序包上载到 Azure 批处理帐户。

    ![用于将 HPC 应用程序发布到 Azure 批处理的任务示例](media/batch-ci-cd/Release-3.jpg)

1. 添加**下载管道项目（预览）** 任务并设置以下属性：
    * **显示名称：** 将应用程序包下载到代理
    * **要下载的项目的名称：hpc**应用程序
    * **下载路径 ：**$（系统.默认工作目录）

1. 创建存储帐户以存储您的项目。 可以使用解决方案的现有存储帐户，但对于独立示例和内容隔离，我们正在为我们的项目（特别是资源管理器模板）创建专用存储帐户。

    添加**Azure 资源组部署**任务并设置以下属性：
    * **显示名称：** 为资源管理器模板部署存储帐户
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **操作**：创建或更新资源组
    * **资源组**： $（资源组名称）
    * **地点**： $（位置）
    * **模板**： $（系统.artifactsDirectory）/**[您的 Azure RepoeeesourceAlias]**/arm 模板/存储帐户.json
    * **覆盖模板参数**： -帐户名称 $（存储帐户名称）

1. 将项目从源控件上载到存储帐户。 有一个 Azure 管道任务可以执行此操作。 作为此任务的一部分，可以将存储帐户容器 URL 和 SAS 令牌输出到 Azure 管道中的变量。 这意味着它可以在此代理阶段重复使用。

    添加**Azure 文件复制**任务并设置以下属性：
    * **来源：** $（系统.artifactsDirectory）/**[您的 Azure RepoeeesourceAlias]**/arm 模板/
    * **Azure 连接类型**： Azure 资源管理器
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **目标类型**： Azure Blob
    * **RM 存储帐户**： $（存储帐户名称）
    * **容器名称**：模板
    * **存储容器 URI**：模板容器 URI
    * **存储容器 SAS 令牌**：模板容器 SasToken

1. 部署协调器模板。 从前面回顾协调器模板，您会注意到除了 SAS 令牌之外，存储帐户容器 URL 还有参数。 您应该注意到，资源管理器模板中所需的变量要么位于发布定义的变量部分，要么从另一个 Azure 管道任务（例如，Azure Blob 复制任务的一部分）设置。

    添加**Azure 资源组部署**任务并设置以下属性：
    * **显示名称：** 部署 Azure 批处理
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **操作**：创建或更新资源组
    * **资源组**： $（资源组名称）
    * **地点**： $（位置）
    * **模板**： $（系统.artifactsDirectory）/**[您的 Azure RepoeeesourceAlias]**/arm 模板/部署.json
    * **覆盖模板参数**：```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

常见做法是使用 Azure 密钥保管库任务。 如果服务主体（连接到 Azure 订阅）具有适当的访问策略集，则可以从 Azure 密钥保管库下载机密，并将其用作管道中的变量。 机密的名称将与关联的值一起设置。 例如，可以在发布定义中引用 sshPassword 的机密和 $（sshPassword）。

1. 后续步骤调用 Azure CLI。 第一个用于在 Azure 批处理中创建应用程序。 并上传关联的包。

    添加**Azure CLI**任务并设置以下属性：
    * **显示名称：** 在 Azure 批处理帐户中创建应用程序
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **脚本位置**： 内联脚本
    * **内联脚本**：```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 第二步用于将关联的包上载到应用程序。 在我们的例子中，ffmpeg 文件。

    添加**Azure CLI**任务并设置以下属性：
    * **显示名称：** 将包上载到 Azure 批处理帐户
    * **Azure 订阅：** 选择相应的 Azure 订阅
    * **脚本位置**： 内联脚本
    * **内联脚本**：```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 应用程序包的版本号设置为变量。 如果覆盖包的早期版本适用于您，并且想要手动控制推送到 Azure Batch 的包的版本号，则此功能非常方便。

1. 通过选择 **"发布>创建新版本"来创建新版本**。 触发后，选择指向新版本的链接以查看状态。

1. 您可以通过选择环境下方的 **"日志"** 按钮来查看来自代理的实时输出。

    ![查看发布状态](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>测试环境

设置环境后，确认以下测试可以成功完成。

使用 PowerShell 命令提示符中的 Azure CLI 连接到新的 Azure 批处理帐户。

* 使用 登录到 Azure 帐户`az login`并按照说明进行身份验证。
* 现在对批处理帐户进行身份验证：`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的应用程序

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>检查池是否有效

```azurecli
az batch pool list
```

请注意`currentDedicatedNodes`此命令的输出中的值。 此值在下一次测试中调整。

#### <a name="resize-the-pool"></a>调整池大小

调整池大小，以便有可用于作业和任务测试的计算节点，请与池列表命令一起查看当前状态，直到调整大小完成并且有可用的节点

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>后续步骤

除了本文之外，还有两个教程使用 ffmpeg，使用 .NET 和 Python。 有关如何通过简单应用程序与 Batch 帐户进行交互的详细信息，请参阅这些教程。

* [使用 Python API 通过 Azure Batch 运行并行工作负荷](tutorial-parallel-python.md)
* [使用 .NET API 通过 Azure Batch 运行并行工作负荷](tutorial-parallel-dotnet.md)
