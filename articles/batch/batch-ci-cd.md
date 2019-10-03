---
title: 使用 Azure Pipelines 生成和部署 HPC 解决方案-Azure Batch |Microsoft Docs
description: 了解如何为 Azure Batch 上运行的 HPC 应用程序部署生成/发布管道。
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 47665171ee5ae137e0503b3e5fa1d369aeabb356
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840055"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure Pipelines 来构建和部署 HPC 解决方案

Azure DevOps services 提供开发团队在构建自定义应用程序时使用的各种工具。 Azure DevOps 提供的工具可转换为高性能计算解决方案的自动生成和测试。 本文演示如何使用 Azure Pipelines 设置持续集成 (CI) 和持续部署 (CD), 以便在 Azure Batch 上部署的高性能计算解决方案。

Azure Pipelines 提供了一系列新式 CI/CD 过程, 用于构建、部署、测试和监视软件。 这些流程加快了软件交付, 使你能够专注于你的代码, 而不是支持基础结构和操作。

## <a name="create-an-azure-pipeline"></a>创建 Azure 管道

在此示例中, 我们将创建一个生成和发布管道, 用于部署 Azure Batch 基础结构并发布应用程序包。 假设代码是在本地开发的, 这是一般的部署流程:

![显示管道中的部署流的关系图](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>安装

若要按照本文中的步骤进行操作, 需要一个 Azure DevOps 组织和一个团队项目。

* [创建 Azure DevOps 组织](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [在 Azure DevOps 中创建项目](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>环境的源代码管理

源代码管理允许团队跟踪对基本代码所做的更改, 并检查代码的以前版本。

通常情况下, 源代码管理是指将软件代码手动进行。 底层基础结构怎么样？ 接下来, 我们将基础结构作为代码, 我们将使用 Azure 资源管理器模板或其他开源替代方法以声明方式定义我们的底层基础结构。

此示例很大程度上依赖于多个资源管理器模板 (JSON 文档) 和现有的二进制文件。 可以将这些示例复制到存储库中, 并将其推送到 Azure DevOps。

本示例中使用的 codebase 结构如下所示:

* **Arm 模板**文件夹, 其中包含多个 Azure 资源管理器模板。 本文介绍了这些模板。
* **客户端-应用程序**文件夹, 它是 ffmpeg 示例的[Azure Batch .net 文件处理](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)的副本。 本文不需要这样做。
* **Hpc 应用程序**文件夹, 它是[Ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)的 Windows 64 位版本。
* **管道**文件夹。 此文件包含一个 YAML 文件, 其中概述了我们的生成过程。 本文将对此进行讨论。

本部分假定您熟悉版本控制和设计资源管理器模板。 如果你不熟悉这些概念, 请参阅以下页面获取详细信息。

* [什么是源代码管理？](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [了解 Azure 资源管理器模板的结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

此示例利用多个资源管理器模板来部署解决方案。 为此, 我们使用多个功能模板 (类似于单位或模块) 来实现特定功能。 我们还使用端到端解决方案模板, 该模板负责将这些基础功能组合在一起。 此方法有几个好处:

* 可以单独测试基础功能模板。
* 基础功能模板可定义为组织内的标准, 并可在多个解决方案中重复使用。

在此示例中, 有一个用于部署三个模板的端到端解决方案模板 (deployment)。 底层模板是功能模板, 负责部署解决方案的特定方面。

![使用 Azure 资源管理器模板的链接模板结构的示例](media/batch-ci-cd/ARMTemplateHierarchy.png)

我们将查看的第一个模板适用于 Azure 存储帐户。 我们的解决方案需要一个存储帐户, 以便将应用程序部署到 Batch 帐户。 在为存储帐户生成资源管理器模板时, 值得注意的是[Microsoft 存储资源类型的资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions)。

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

接下来, 我们将查看 Azure Batch 帐户模板。 Azure Batch 帐户充当一种平台, 用于跨池 (计算机的分组) 运行多个应用程序。 值得注意的是, 在生成批处理帐户资源管理器模板时,[适用于 Microsoft 的资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

下一个模板显示了创建 Azure Batch 池 (用于处理应用程序的后端计算机) 的示例。 值得注意的是, 在生成 Batch 帐户池资源管理器模板时,[适用于 Microsoft 的资源管理器模板参考指南](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions)。

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

最后, 我们有一个类似于 orchestrator 的模板。 此模板负责部署功能模板。

你还可以在单独的文章中了解有关[创建链接的 Azure 资源管理器模板](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md)的详细信息。

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

基础结构和软件可以定义为代码, 并在相同的存储库中进行定位。

对于此解决方案, 将使用 ffmpeg 作为应用程序包。 可在[此处](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)下载 ffmpeg 包。

![Git 存储库结构示例](media/batch-ci-cd/git-repository.jpg)

此存储库有四个主要部分:

* 将基础结构存储为代码的**arm 模板**文件夹
* 包含 ffmpeg 二进制文件的**hpc 应用程序**文件夹
* **管道**文件夹, 其中包含生成管道的定义。
* 可选：用于存储 .NET 应用程序代码的**客户端应用程序**文件夹。 我们不会在示例中使用它, 但在你自己的项目中, 你可能希望通过客户端应用程序执行 HPC 批处理应用程序的运行。

> [!NOTE]
> 这只是基本代码结构的一个示例。 此方法用于演示应用程序、基础结构和管道代码存储在同一存储库中的目的。

现在, 已设置源代码, 接下来可以开始第一次生成。

## <a name="continuous-integration"></a>持续集成

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)在 Azure DevOps Services 中, 可帮助你实现应用程序的生成、测试和部署管道。

在管道的此阶段中, 通常会运行测试来验证代码并构建软件的相应部分。 测试的数量和类型以及你运行的任何其他任务将取决于你的更广泛的生成和发布策略。

## <a name="preparing-the-hpc-application"></a>准备 HPC 应用程序

在此示例中, 我们将重点放在**hpc 应用程序**文件夹上。 **Hpc 应用程序**文件夹是将从 Azure Batch 帐户中运行的 ffmpeg 软件。

1. 导航到 Azure DevOps 组织中 Azure Pipelines 的 "生成" 部分。 创建**新管道**。

    ![创建新的生成管道](media/batch-ci-cd/new-build-pipeline.jpg)

1. 可以通过两种方法创建生成管道:

    a. [使用可视化设计器](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav)。 为此, 请单击 "**新建管道**" 页上的 "使用可视化设计器"。

    b. [使用 YAML 生成](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops)。 可以通过单击 "新建管道" 页上的 "Azure Repos" 或 "GitHub" 选项来创建新的 YAML 管道。 或者, 你可以在源代码管理中存储下面的示例, 并通过单击 "可视化设计器", 然后使用 YAML 模板引用现有的 YAML 文件。

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

1. 根据需要配置生成后, 选择 "**保存 & 队列**"。 如果已启用持续集成 (在 "**触发器**" 部分中), 则生成将在创建存储库的新提交时自动触发, 并满足生成中设置的条件。

    ![现有生成管道的示例](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 通过导航到 Azure Pipelines 的 "**生成**" 部分, 在 Azure DevOps 中查看生成进度的实时更新。 从生成定义中选择相应的生成。

    ![查看生成中的实时输出](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果使用客户端应用程序执行 HPC 批处理应用程序, 则需要为该应用程序创建单独的生成定义。 在[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops)文档中可以找到许多操作方法指南。

## <a name="continuous-deployment"></a>持续部署

Azure Pipelines 还用于部署应用程序和底层基础结构。 [版本管道](https://docs.microsoft.com/azure/devops/pipelines/release)是用于实现持续部署并自动执行发布过程的组件。

### <a name="deploying-your-application-and-underlying-infrastructure"></a>部署应用程序和底层基础结构

部署基础结构涉及多个步骤。 由于我们使用了[链接模板](../azure-resource-manager/resource-group-linked-templates.md), 因此需要从公共终结点 (HTTP 或 HTTPS) 访问这些模板。 这可能是 GitHub 上的存储库、Azure Blob 存储帐户或其他存储位置。 上传的模板项目可以保持安全, 因为它们可以保存在专用模式下, 但使用某种形式的共享访问签名 (SAS) 令牌进行访问。 以下示例演示了如何使用 Azure 存储 blob 中的模板部署基础结构。

1. 创建**新的发布定义**, 并选择一个空定义。 接下来, 我们需要将新创建的环境重命名为与管道相关的内容。

    ![初始发布管道](media/batch-ci-cd/Release-0.jpg)

1. 在生成管道上创建依赖项, 以获取 HPC 应用程序的输出。

    > [!NOTE]
    > 再次记录**源别名**, 因为在发布定义内创建任务时需要用到它。

    ![在适当的生成管道中创建指向 HPCApplicationPackage 的项目链接](media/batch-ci-cd/Release-1.jpg)

1. 创建到其他项目的链接, 这一次是 Azure 存储库。 这对于访问存储在存储库中的资源管理器模板是必需的。 由于资源管理器模板不需要编译, 因此不需要通过生成管道来推送这些模板。

    > [!NOTE]
    > 再次记录**源别名**, 因为在发布定义内创建任务时需要用到它。

    ![创建指向 Azure Repos 的项目链接](media/batch-ci-cd/Release-2.jpg)

1. 导航到 "**变量**" 部分。 建议在管道中创建多个变量, 因此不会在多个任务中输入相同的信息。 下面是此示例中使用的变量, 以及它们如何影响部署。

    * **applicationStorageAccountName**:用于保存 HPC 应用程序二进制文件的存储帐户的名称
    * **batchAccountApplicationName**:Azure Batch 帐户中的应用程序名称
    * **batchAccountName**:Azure Batch 帐户的名称
    * **batchAccountPoolName**:执行处理的 Vm 池的名称
    * **batchApplicationId**:Azure Batch 应用程序的唯一 ID
    * **batchApplicationVersion**:批处理应用程序的语义版本 (即 ffmpeg 二进制文件)
    * **location**：要部署的 Azure 资源的位置
    * **resourceGroupName**:要创建的资源组的名称以及要将资源部署到的位置
    * **storageAccountName**：保存链接资源管理器模板的存储帐户的名称

    ![为 Azure Pipelines 版本设置的变量示例](media/batch-ci-cd/Release-4.jpg)

1. 导航到开发环境的任务。 在下面的快照中, 可以看到六个任务。 这些任务将: 下载压缩的 ffmpeg 文件、部署用于托管嵌套资源管理器模板的存储帐户、将这些资源管理器模板复制到存储帐户、部署批处理帐户和所需的依赖项、在中创建应用程序Azure Batch 帐户并将应用程序包上传到 Azure Batch 帐户。

    ![用于释放 HPC 应用程序以 Azure Batch 的任务示例](media/batch-ci-cd/Release-3.jpg)

1. 添加 "**下载管道项目 (预览版)** " 任务并设置下列属性:
    * **显示名称:** 将 ApplicationPackage 下载到代理
    * **要下载的项目的名称:** hpc-应用程序
    * 要**下载到的路径**: $ (system.defaultworkingdirectory)

1. 创建存储帐户以存储你的项目。 可以使用解决方案中的现有存储帐户, 但对于自包含示例和内容隔离, 我们将为我们的项目 (特别是资源管理器模板) 生成专用存储帐户。

    添加**Azure 资源组部署**任务, 并设置以下属性:
    * **显示名称:** 为资源管理器模板部署存储帐户
    * **Azure 订阅:** 选择相应的 Azure 订阅
    * **操作**：创建或更新资源组
    * **资源组**: $ (resourceGroupName)
    * **位置**: $ (位置)
    * **Template**: $ (ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/storageAccount.json
    * **替代模板参数**:-accountName $ (storageAccountName)

1. 将源代码管理中的项目上传到存储帐户。 有一个 Azure 管道任务来执行此任务。 在此任务中, 可以将存储帐户容器 URL 和 SAS 令牌输出到 Azure Pipelines 中的变量。 这意味着它可以在此代理阶段重复使用。

    添加 " **Azure 文件复制**" 任务并设置以下属性:
    * **Source:** $ (ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/
    * **Azure 连接类型**:Azure 资源管理器
    * **Azure 订阅:** 选择相应的 Azure 订阅
    * **目标类型**：Azure Blob
    * **RM 存储帐户**: $ (storageAccountName)
    * **容器名称**: 模板
    * **存储容器 URI**: templateContainerUri
    * **存储容器 SAS 令牌**: templateContainerSasToken

1. 部署 orchestrator 模板。 再次调用 orchestrator 模板, 你会注意到除了 SAS 令牌外, 还存在存储帐户容器 URL 的参数。 你应注意到, 资源管理器模板中所需的变量已保存在发布定义的 variables 节中, 或已从另一 Azure Pipelines 任务 (例如, Azure Blob 复制任务的一部分) 进行了设置。

    添加**Azure 资源组部署**任务, 并设置以下属性:
    * **显示名称:** 部署 Azure Batch
    * **Azure 订阅:** 选择相应的 Azure 订阅
    * **操作**：创建或更新资源组
    * **资源组**: $ (resourceGroupName)
    * **位置**: $ (位置)
    * **Template**: $ (ArtifactsDirectory)/ **{YourAzureRepoArtifactSourceAlias}** /arm-templates/deployment.json
    * **替代模板参数**:```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

常见的做法是使用 Azure Key Vault 任务。 如果服务主体 (与 Azure 订阅的连接) 设置了适当的访问策略, 则它可以从 Azure Key Vault 下载机密, 并在管道中将其用作变量。 密码的名称将设置为关联值。 例如, 可以在发布定义中使用 $ (sshPassword) 来引用 sshPassword 的机密。

1. 接下来的步骤调用 Azure CLI。 第一种用于在 Azure Batch 中创建应用程序。 并上传关联的包。

    添加**Azure CLI**任务并设置以下属性:
    * **显示名称:** 在 Azure Batch 帐户中创建应用程序
    * **Azure 订阅:** 选择相应的 Azure 订阅
    * **脚本位置**:内联脚本
    * **内联脚本**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. 第二步用于将关联的包上传到应用程序。 在本例中为 ffmpeg 文件。

    添加**Azure CLI**任务并设置以下属性:
    * **显示名称:** 将包上载到 Azure Batch 帐户
    * **Azure 订阅:** 选择相应的 Azure 订阅
    * **脚本位置**:内联脚本
    * **内联脚本**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > 应用程序包的版本号设置为变量。 如果覆盖包的早期版本适用, 并且你想要手动控制推送到 Azure Batch 的包的版本号, 这会很方便。

1. 通过选择 "**发布 > 创建新版本**, 创建新发布。 触发后, 选择新发布的链接以查看状态。

1. 可以通过选择环境下面的 "**日志**" 按钮, 查看代理的实时输出。

    ![查看发布的状态](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>测试环境

设置环境后, 确认以下测试是否可以成功完成。

使用 PowerShell 命令提示符中的 Azure CLI 连接到新的 Azure Batch 帐户。

* 登录到 Azure 帐户`az login` , 并按照说明进行身份验证。
* 现在对 Batch 帐户进行身份验证:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的应用程序

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>检查池是否有效

```azurecli
az batch pool list
```

请注意此命令`currentDedicatedNodes`的输出中的值。 此值在下一测试中进行调整。

#### <a name="resize-the-pool"></a>调整池大小

调整池大小, 使计算节点可用于作业和任务测试, 请使用 pool list 命令查看当前状态, 直到完成调整大小并且有可用节点

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>后续步骤

除本文外, 还提供了两个教程, 使用 .NET 和 Python ffmpeg。 请参阅以下教程, 详细了解如何通过简单应用程序与 Batch 帐户交互。

* [使用 Python API Azure Batch 运行并行工作负荷](tutorial-parallel-python.md)
* [使用 .NET API Azure Batch 运行并行工作负荷](tutorial-parallel-dotnet.md)
