---
title: 使用共享映像库创建池-Azure Batch |Microsoft Docs
description: 使用共享映像库创建 Batch 池, 以将自定义映像设置为包含应用程序所需的软件和数据的计算节点。 自定义映像是配置计算节点以运行 Batch 工作负载的高效方法。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/14/2019
ms.author: lahugh
ms.openlocfilehash: 00da17512cbc2e713955ea83c7d9fa7517958169
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036688"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>使用共享映像库创建池

使用虚拟机配置创建 Azure Batch 池时，需指定一个虚拟机 (VM) 映像，为池中每个计算节点提供操作系统。 你可以使用受支持的 Azure Marketplace 映像创建虚拟机池, 或者使用[共享映像库](../virtual-machines/windows/shared-image-galleries.md)创建自定义映像。

## <a name="benefits-of-the-shared-image-gallery"></a>共享映像库的优点

使用自定义映像的共享映像库时, 可以控制操作系统类型和配置, 以及数据磁盘的类型。 共享映像可包括应用程序和引用数据, 这些数据在预配后就会在所有 Batch 池节点上可用。

你还可以根据需要为你的环境提供多个版本的映像。 使用映像版本创建 VM 时, 映像版本用于为 VM 创建新磁盘。 

使用共享映像可节省准备池的计算节点以运行 Batch 工作负荷的时间。 预配后, 可以使用 Azure Marketplace 映像并在每个计算节点上安装软件, 但使用共享映像通常效率更高。 此外, 你可以为共享映像指定多个副本, 因此, 当你创建包含多个 Vm (超过600个 Vm) 的池时, 你将在创建池时节省时间。

使用为你的方案配置的共享映像可提供以下优势:

* **跨区域使用相同的图像。** 可以在不同的区域中创建共享的映像副本, 以便所有池使用同一个映像。
* **配置操作系统 (OS)。** 可以自定义映像操作系统磁盘的配置。
* **预安装应用程序。** 在使用启动任务预配计算节点后, 在 OS 磁盘上预先安装应用程序比安装应用程序更高效且容易出错。
* **复制大量数据一次。** 将静态数据复制到托管映像的数据磁盘, 使其成为托管共享映像的一部分。 只需执行此操作一次，然后，数据可供池的每个节点使用。
* **将池增长到更大的大小。** 利用共享映像库, 可以创建更大的池, 以及自定义映像和更多共享映像副本。
* **比自定义映像更好的性能。** 使用共享映像, 池达到稳定状态所用的时间最快可达 25%, 并且 VM 空闲延迟最多可达 30%。
* **用于简化管理的图像版本控制和分组。** 映像分组定义包含有关创建映像的原因、其适用的操作系统以及有关使用映像的信息。 通过对图像进行分组, 可以更轻松地管理图像。 有关详细信息, 请参阅[映像定义](../virtual-machines/windows/shared-image-galleries.md#image-definitions)。

## <a name="prerequisites"></a>先决条件

* 一个 Azure Batch 帐户。 若要创建批处理帐户, 请参阅使用[Azure 门户](quick-create-portal.md)或[Azure CLI](quick-create-cli.md)的批处理快速入门。

* **共享图像库图像**。 有关准备共享映像的详细信息和步骤, 请参阅使用[Azure CLI 创建共享图像库](../virtual-machines/linux/shared-images.md)或[使用 Azure 门户创建共享图像库](../virtual-machines/linux/shared-images-portal.md)。

> [!NOTE]
> 共享映像必须与 Batch 帐户位于同一订阅中。 只要共享映像在与 Batch 帐户相同的区域中具有副本, 就可以在不同的区域中。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>使用 Azure CLI 从共享映像创建池

若要使用 Azure CLI 从共享映像创建池, 请使用`az batch pool create`命令。 在`--image`字段中指定共享映像 ID。 请确保 OS 类型和 SKU 与指定的版本匹配`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>使用从共享映像创建池C#

或者, 可以使用C# SDK 从共享映像创建池。

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="considerations-for-large-pools"></a>大型池的注意事项

如果计划使用共享映像创建包含数百或数千个 Vm 或更多 Vm 的池, 请使用以下指南。

* **共享图像库副本数。**  对于每个具有多达600实例的池, 建议至少保留一个副本。 例如, 如果要创建包含 3000 Vm 的池, 则应至少保留映像的5个副本。 我们始终建议保留比最低要求更多的副本, 以获得更好的性能。

* **调整超时**如果池包含固定数量的节点 (如果不自动缩放), 请根据池大小`resizeTimeout`增加池的属性。 对于每个 1000 Vm, 建议的大小调整超时至少为15分钟。 例如, 对于包含 2000 Vm 的池, 建议的大小调整超时至少为30分钟。

## <a name="next-steps"></a>后续步骤

* 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
