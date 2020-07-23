---
title: 在云中渲染场景
description: 教程 - 如何使用 Batch 渲染服务和 Azure 命令行界面通过 Arnold 来渲染 Autodesk 3ds Max 场景
ms.topic: tutorial
ms.date: 03/05/2020
ms.custom: mvc
ms.openlocfilehash: e78580cc2f95f14be53c0432df4eb4bd38450832
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82117125"
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>教程：使用 Azure Batch 渲染场景 

Azure Batch 提供云规模的渲染功能，按使用付费。 Azure Batch 支持渲染应用，包括 Autodesk Maya、3ds Max、Arnold 和 V-Ray。 本教程介绍如何执行相关步骤，以便使用 Azure 命令行界面通过 Batch 来渲染小型场景。 学习如何：

> [!div class="checklist"]
> * 将场景上传到 Azure 存储
> * 创建用于渲染的 Batch 池
> * 渲染单帧场景
> * 缩放池并渲染多帧场景
> * 下载渲染的输出

本教程使用 Batch，通过 [Arnold](https://www.autodesk.com/products/arnold/overview) 光线跟踪渲染器来渲染 3ds Max 场景。 Batch 池使用一个 Azure 市场映像，该映像中预安装了提供按使用付费的许可的图形和渲染应用程序。

## <a name="prerequisites"></a>先决条件

要以按用户付款模式使用 Batch 中的渲染应用程序，需要有一个即用即付订阅或其他 Azure 购买选项。 **如果使用的是提供货币额度的免费 Azure 套餐，则不支持按使用付费的许可。**

[GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene) 上提供了本教程的示例 3ds Max 场景，以及示例 Bash 脚本和 JSON 配置文件。 3ds Max 场景来自 [Autodesk 3ds Max 示例文件](https://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe)。 （提供的 Autodesk 3ds Max 示例文件已获得 Creative Commons Attribution-NonCommercial-Share Alike 许可。 版权所有 &copy; Autodesk, Inc.）

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.20 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-batch-account"></a>创建批处理帐户

在订阅中创建资源组、Batch 帐户和链接存储帐户（如果尚未这样做）。 

使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 以下示例在“eastus2”  位置创建名为“myResourceGroup”的资源组  。

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令在资源组中创建 Azure 存储帐户。 本教程使用该存储帐户来存储输入的 3ds Max 场景以及渲染的输出。

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
使用 [az batch account create](/cli/azure/batch/account#az-batch-account-create) 命令创建 Batch 帐户。 以下示例在 *myResourceGroup* 中创建名为 *mybatchaccount* 的 Batch 帐户，并链接已创建的存储帐户。  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

若要创建和管理计算池和作业，需使用 Batch 进行身份验证。 使用 [az batch account login](/cli/azure/batch/account#az-batch-account-login) 命令登录到帐户。 登录后，`az batch` 命令使用此帐户上下文。 以下示例使用基于 Batch 帐户名称和密钥的共享密钥身份验证。 Batch 还支持通过 [Azure Active Directory](batch-aad-auth.md) 进行身份验证，以便对单个用户或无人参与应用程序进行身份验证。

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>将场景上传到存储

若要将输入场景上传到存储，首先需访问存储帐户并为 Blob 创建目标容器。 若要访问 Azure 存储帐户，请导出 `AZURE_STORAGE_KEY` 和 `AZURE_STORAGE_ACCOUNT` 环境变量。 第一个 Bash shell 命令使用 [az storage account keys list](/cli/azure/storage/account/keys#az-storage-account-keys-list) 命令来获取第一个帐户密钥。 设置这些环境变量后，存储命令使用此帐户上下文。

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

现在，请在存储帐户中为场景文件创建 Blob 容器。 以下示例使用 [az storage container create](/cli/azure/storage/container#az-storage-container-create) 命令创建允许公开读取访问的名为 *scenefiles* 的 Blob 容器。

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

将场景 `MotionBlur-Dragon-Flying.max` 从 [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max) 下载到本地工作目录。 例如：

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

将场景文件从本地工作目录上传到 Blob 容器。 以下示例使用 [az storage blob upload-batch](/cli/azure/storage/blob#az-storage-blob-upload-batch) 命令，该命令可上传多个文件：

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>创建渲染池

使用 [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) 命令创建用于渲染的 Batch 池。 此示例在 JSON 文件中指定池设置。 在当前 shell 中，创建名为 *mypool.json* 的文件，然后复制并粘贴以下内容。 请确保正确复制所有文本。 （可以从 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json) 下载文件。）


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.3.8"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch 支持专用节点和[低优先级](batch-low-pri-vms.md)节点。可以在池中使用这其中的一种，或者两种都使用。 专用节点为池保留。 低优先级节点在 Azure 有剩余 VM 容量时以优惠价提供。 如果 Azure 没有足够的容量，低优先级节点会变得不可用。 

指定的池包含单个运行 Windows Server 映像的低优先级节点，所装软件适用于 Batch 渲染服务。 该池已获得使用 3ds Max 和 Arnold 进行渲染的许可。 在后面的步骤中，请扩展该池，增加节点数。

将 JSON 文件传递至 `az batch pool create` 命令即可创建该池：

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
池的预配需要数分钟。 若要查看池的状态，请运行 [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) 命令。 以下命令获取池的分配状态：

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

继续以下步骤，在池状态更改的情况下创建作业和任务。 如果分配状态为`steady`且节点处于运行状态，则说明池已完全预配好。  

## <a name="create-a-blob-container-for-output"></a>创建用于输出的 Blob 容器

在本教程的示例中，渲染作业中的每个任务都会创建一个输出文件。 在计划此作业之前，请在存储帐户中创建一个 Blob 容器，作为输出文件的目标。 以下示例使用 [az storage container create](/cli/azure/storage/container#az-storage-container-create) 命令创建可以公开读取访问的 *job-myrenderjob* 容器。 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

为了将输出文件写入到容器中，Batch 需要使用共享访问签名 (SAS) 令牌。 使用 [az storage account generate-sas](/cli/azure/storage/account#az-storage-account-generate-sas) 命令创建该令牌。 以下示例创建的令牌用于向帐户中的任何 Blob 容器写入内容，该令牌在 2020 年 11 月 15 日过期：

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2020-11-15
```

记下该命令返回的令牌，如下所示。 在稍后的步骤中会使用此令牌。

```
se=2020-11-15&sp=rw&sv=2019-09-24&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>渲染单帧场景

### <a name="create-a-job"></a>创建作业

使用 [az batch job create](/cli/azure/batch/job#az-batch-job-create) 命令创建可在池中运行的渲染作业。 作业一开始没有任务。

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>创建任务

使用 [az batch task create](/cli/azure/batch/task#az-batch-task-create) 命令在作业中创建渲染任务。 此示例在 JSON 文件中指定任务设置。 在当前 shell 中，创建名为 *myrendertask.json* 的文件，然后复制并粘贴以下内容。 请确保正确复制所有文本。 （可以从 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json) 下载文件。）

此任务指定一个 3ds Max 命令，用于渲染场景 *MotionBlur-DragonFlying.max* 的单个帧。

在 JSON 文件中修改 `blobSource` 和 `containerURL` 元素，使之包括存储帐户和 SAS 令牌的名称。 

> [!TIP]
> `containerURL` 以 SAS 令牌结尾，类似于：
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "httpUrl": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

使用以下命令将任务添加到作业：

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch 可以计划任务，让任务在池中的节点可用时运行。


### <a name="view-task-output"></a>查看任务输出

任务的运行需要数分钟。 请使用 [az batch task show](/cli/azure/batch/task#az-batch-task-show) 命令查看任务详细信息。

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

任务在计算节点上生成 *dragon0001.jpg*，并将其上传到存储帐户中的 *job-myrenderjob* 容器。 若要查看输出，请使用 [az storage blob download](/cli/azure/storage/blob#az-storage-blob-download) 命令将文件从存储下载到本地计算机。

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

在计算机上打开 *dragon.jpg*。 渲染的图像如下所示：

![渲染的龙第 1 帧](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>缩放池

现在请修改该池，为包含多个帧的更大型渲染作业做准备。 Batch 提供多种缩放计算资源的方式，包括可以在任务需求变化时添加或删除节点的[自动缩放](batch-automatic-scaling.md)。 对于这个基本的示例，请使用 [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) 命令将池中低优先级节点的数目增加到 *6*：

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

池的重设大小需要数分钟。 执行该过程时，请设置需要在现有的渲染作业中运行的后续任务。

## <a name="render-a-multiframe-scene"></a>渲染多帧场景

请使用 [az batch task create](/cli/azure/batch/task#az-batch-task-create) 命令在名为 *myrenderjob* 的作业中创建渲染任务，就像在单帧示例中一样。 在这里，请在名为 *myrendertask_multi.json* 的 JSON 文件中指定任务设置。 （可以从 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json) 下载文件。）这六个任务中，每个都会指定一个 Arnold 命令行，用于渲染 3ds Max 场景 *MotionBlur-DragonFlying.max* 的一个帧。

在当前 shell 中创建名为 *myrendertask_multi.json* 的文件，然后复制并粘贴已下载文件中的内容。 在 JSON 文件中修改 `blobSource` 和 `containerURL` 元素，使之包括存储帐户和 SAS 令牌的名称。 确保更改这六个任务中的每个任务的设置。 保存文件，然后运行以下命令，将任务排队：

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>查看任务输出

任务的运行需要数分钟。 使用 [az batch task list](/cli/azure/batch/task#az-batch-task-list) 命令查看任务的状态。 例如：

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

请使用 [az batch task show](/cli/azure/batch/task#az-batch-task-show) 命令查看各个任务的详细信息。 例如：

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
这些任务在计算节点上生成名为 *dragon0002.jpg* - *dragon0007.jpg* 的输出文件，并将其上传到存储帐户中的 *job-myrenderjob* 容器。 若要查看输出，请使用 [az storage blob download-batch](/cli/azure/storage/blob) 命令将文件下载到本地计算机上的某个文件夹。 例如：

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

打开计算机上的一个文件。 渲染的第 6 帧如下所示：

![渲染的龙第 6 帧](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、Batch 帐户、池和所有相关的资源，则可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。 删除资源，如下所示：

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 将场景上传到 Azure 存储
> * 创建用于渲染的 Batch 池
> * 使用 Arnold 渲染单帧场景
> * 缩放池并渲染多帧场景
> * 下载渲染的输出

若要详细了解云规模的渲染，请查看 Batch 渲染服务的选项。 

> [!div class="nextstepaction"]
> [Batch Rendering 服务](batch-rendering-service.md)
