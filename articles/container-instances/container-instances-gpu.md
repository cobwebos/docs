---
title: 部署已启用 GPU 的 Azure 容器实例
description: 了解如何使用 GPU 资源部署 Azure 容器实例，以运行计算密集型容器应用。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 08a3cd8841b6b867a2dd22078fca3543d2067830
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147892"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>部署使用 GPU 资源的容器实例

要在 Azure 容器实例上运行某些计算密集型工作负载，请使用“GPU 资源”部署[容器组](container-instances-container-groups.md)。 组中的容器实例可以在运行容器工作负载（例如 CUDA 和深度学习应用程序）的同时访问一个或多个 NVIDIA Tesla GPU。

本文介绍如何在使用[YAML 文件](container-instances-multi-container-yaml.md)或[资源管理器模板](container-instances-multi-container-group.md)部署容器组时添加 GPU 资源。 当使用 Azure 门户部署容器实例时，还可以指定 GPU 资源。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

在预览版中，在容器组中使用 GPU 资源时应用下列限制。 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

以后还会不断增添对其他区域的支持。

**支持的 OS 类型**：仅限 Linux

**其他限制**：在将容器组部署到[虚拟网络](container-instances-vnet.md)中时不能使用 GPU 资源。

## <a name="about-gpu-resources"></a>关于 GPU 资源

### <a name="count-and-sku"></a>计数和 SKU

若要在容器实例中使用 GPU，请使用以下信息指定 GPU 资源：

* **Count** -gpu 数： **1**、 **2**或**4**。
* **SKU** -GPU SKU： **K80**、 **P100**或**V100**。 每个 SKU 都映射到以下支持 Azure GPU 的 VM 系列中的 NVIDIA Tesla GPU：

  | SKU | VM 系列 |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

部署 GPU 资源时，请设置适合工作负荷的 CPU 和内存资源，直到上表中显示的最大值。 这些值当前大于没有 GPU 资源的容器组中可用的 CPU 和内存资源。  

### <a name="things-to-know"></a>使用须知

* **部署时间** - 创建包含 GPU 资源的容器组最多需要 8-10 分钟。 这是因为需为预配和配置 Azure 中的 GPU VM 留出更多时间。 

* **定价** - 类似于不含 GPU 资源的容器组，Azure 对具有 GPU 资源的容器组的持续时间内消耗的资源收费。 持续时间自容器开始拉取第一个容器的映像起开始计算，至容器组终止为止。 它不包括部署容器组的时间。

  请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/container-instances/)。

* **CUDA 驱动程序** - 具有 GPU 资源的容器实例使用 NVIDIA CUDA 驱动程序和容器运行时进行预配，因此可以使用专为 CUDA 工作负载开发的容器映像。

  在此阶段，我们支持 CUDA 9.0。 例如，你可以对 Docker 文件使用以下基本映像：
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow： 1.12.0-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML 示例

添加 GPU 资源的一种方式就是使用 [YAML 文件](container-instances-multi-container-yaml.md)部署容器组。 将以下 YAML 复制到名为 gpu-deploy-aci.yaml 的新文件中，然后保存该文件。 此 YAML 创建名为 gpucontainergroup 的容器组并使用 K80 GPU 指定容器实例。 该实例运行示例 CUDA 矢量添加应用程序。 请求的资源足以运行工作负载。

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

用[az container create][az-container-create]命令部署容器组，并为 `--file` 参数指定 YAML 文件名。 需要提供支持 GPU 资源的资源组名称和容器组位置（例如 eastus）。  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

部署需要数分钟才能完成。 然后，容器启动并运行 CUDA 矢量添加操作。 运行[az container logs][az-container-logs]命令以查看日志输出：

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

输出：

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>资源管理器模板示例

使用 GPU 资源部署容器组的另一种方式是使用[资源管理器模板](container-instances-multi-container-group.md)。 首先，创建名为 `gpudeploy.json` 的文件，再将以下 JSON 复制到其中。 此示例使用 V100 GPU 部署一个容器实例，该 GPU 针对 MNIST 数据集运行[TensorFlow](https://www.tensorflow.org/)定型作业。 请求的资源足以运行工作负载。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

使用 [az group deployment create][az-group-deployment-create] 命令部署模板。 需要提供支持 GPU 资源的在区域（例如 eastus）中创建的资源组的名称。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

部署需要数分钟才能完成。 然后，容器启动并运行 TensorFlow 作业。 运行[az container logs][az-container-logs]命令以查看日志输出：

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

输出：

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>清理资源

由于使用 GPU 资源可能很昂贵，请确保容器在长时间内运行不会发生意外。 监视 Azure 门户中的容器，或使用[az container show][az-container-show]命令检查容器组的状态。 例如：

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

处理完所创建的容器实例后，使用以下命令将其删除：

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>后续步骤

* 详细了解有关使用 [YAML 文件](container-instances-multi-container-yaml.md)或[资源管理器模板](container-instances-multi-container-group.md)部署容器组的信息。
* 详细了解 Azure 中的 [GPU 最佳化的 VM 大小](../virtual-machines/linux/sizes-gpu.md)。


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
