---
title: 使用共享映像库创建自定义池
description: 使用共享映像库创建 Batch 池，以便将自定义映像预配到计算节点，这些节点包含应用程序所需的软件和数据。 自定义映像是配置计算节点以运行 Batch 工作负载的高效方法。
ms.topic: article
ms.date: 08/28/2019
ms.openlocfilehash: 1a26aaecc5da0ef348b720919b04d86f8fcfbc70
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743576"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>使用共享映像库创建自定义池

使用虚拟机配置创建 Azure Batch 池时，需指定一个虚拟机 (VM) 映像，为池中每个计算节点提供操作系统。 可以使用受支持的 Azure 市场映像创建虚拟机池，也可以使用[共享映像库](../virtual-machines/windows/shared-image-galleries.md)创建自定义映像。

## <a name="benefits-of-the-shared-image-gallery"></a>共享映像库的优势

将共享映像库用于自定义映像时，可以控制操作系统类型和配置，以及数据磁盘的类型。 共享映像可以包含应用程序和引用数据，Batch 池节点预配好后即可使用这些数据。

也可根据环境的需要创建多个映像版本。 在使用某个映像版本创建 VM 时，将使用该映像版本来创建 VM 的新磁盘。

在准备池的计算节点以运行 Batch 工作负荷时，使用共享映像可以节省时间。 可以在每个预配后的计算节点上使用 Azure 市场映像并安装软件，但通常情况下，使用共享映像会更加高效。 另外，可以为共享映像指定多个副本。这样，当你创建包含多个 VM（600 个 VM 以上）的池时，就会节省创建池的时间。

使用根据方案配置的共享映像有几个优点：

* **在不同的区域中使用相同的映像。** 可以跨不同的区域创建共享映像副本，这样所有的池就可以利用同一映像。
* **配置操作系统 (OS)。** 可以自定义映像操作系统磁盘的配置。
* **预安装应用程序。** 在 OS 磁盘中预装应用程序，与使用启动任务预配计算节点后再安装应用程序相比，这种方法更加高效，且不容易出错。
* **一次复制大量的数据。** 将静态数据复制到托管映像的数据磁盘，使这些数据成为托管的共享映像的一部分。 只需执行此操作一次，然后，数据可供池的每个节点使用。
* **扩大池的大小。** 可以通过共享映像库创建更大的池，其中包含自定义映像和更多共享映像副本。
* **性能优于自定义映像。** 使用共享映像时，池达到稳定状态所需的时间最多可以缩短 25%，VM 空闲延迟最多可以缩短 30%。
* **进行映像版本控制和分组，以便于管理。** 映像分组定义包含的信息涉及创建映像的原因、它适用于哪个 OS，以及映像的用法。 对映像分组可以方便映像的管理。 有关详细信息，请参阅[映像定义](../virtual-machines/windows/shared-image-galleries.md#image-definitions)。

## <a name="prerequisites"></a>必备条件

> [!NOTE]
> 需要使用 Azure AD 进行身份验证。 如果使用共享密钥身份验证，则会出现身份验证错误。  

* **** 一个 Azure Batch 帐户。 若要创建 Batch 帐户，请参阅 Batch 快速入门（使用 [Azure 门户](quick-create-portal.md)或 [Azure CLI](quick-create-cli.md)）。

* **一个共享映像库映像**。 若要创建共享映像，需要具有或创建托管映像资源。 应该基于 VM 的 OS 磁盘快照及其附加的数据磁盘（可选）创建该映像。 有关详细信息，请参阅[准备托管映像](#prepare-a-managed-image)。

> [!NOTE]
> 共享映像必须与 Batch 帐户属于同一订阅。 共享映像可以位于不同的区域中，前提是它在 Batch 帐户所在的区域中有副本。

## <a name="prepare-a-managed-image"></a>准备托管映像

在 Azure 中，可以通过以下项准备托管映像：

* Azure VM 的 OS 和数据磁盘的快照
* 带托管磁盘的通用 Azure VM
* 已上传到云的通用本地 VHD

若要使用自定义映像可靠缩放 Batch 池，我们建议仅使用第一种方法创建托管映像：使用 VM 磁盘的快照。** 参阅以下步骤来准备 VM、创建快照，然后基于该快照创建映像。

### <a name="prepare-a-vm"></a>准备 VM

若要为映像创建新 VM，请使用 Batch 支持的第一方 Azure 市场映像作为托管映像的基础映像。 仅第一方映像可以用作基础映像。 若要获取 Azure Batch 支持的 Azure 市场映像参考的完整列表，请参阅[列出节点代理 SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 操作。

> [!NOTE]
> 不能使用具有附加许可和购买条款的第三方映像作为基础映像。 有关这些市场映像的信息，请参阅 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) 或 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM 指南。

* 确保使用托管磁盘创建 VM。 这是创建 VM 时的默认存储设置。
* 不要在 VM 上安装自定义脚本扩展等 Azure 扩展。 如果映像包含预装的扩展，在部署 Batch 池时 Azure 可能会遇到问题。
* 使用附加的数据磁盘时，需要从 VM 中装载和格式化磁盘，才能使用它们。
* 确保所提供的基础 OS 映像使用默认临时驱动器。 Batch 节点代理目前需要使用默认的临时驱动器。
* VM 开始运行后，请通过 RDP（适用于 Windows）或 SSH（适用于 Linux）进行连接。 安装所需的任何软件，或复制所需的数据。  

### <a name="create-a-vm-snapshot"></a>创建 VM 快照

快照是 VHD 的完整只读副本。 若要创建 VM OS 磁盘或数据磁盘的快照，可以使用 Azure 门户或命令行工具。 有关创建快照的步骤和选项，请参阅适用于 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 或 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 的指导。

### <a name="create-an-image-from-one-or-more-snapshots"></a>基于一个或多个快照创建映像

若要基于快照创建托管映像，请使用 Azure 命令行工具，例如 [az image create](/cli/azure/image) 命令。 通过指定 OS 磁盘快照并有选择性地指定一个或多个数据磁盘快照来创建映像。

### <a name="create-a-shared-image-gallery"></a>创建共享映像库

成功创建托管映像后，需要创建一个共享映像库，使自定义映像可用。 若要了解如何创建映像的共享映像库，请参阅使用[Azure CLI 创建共享图像库](../virtual-machines/linux/shared-images.md)或[使用 Azure 门户创建共享图像库](../virtual-machines/linux/shared-images-portal.md)。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>使用 Azure CLI 从共享映像创建池

若要使用 Azure CLI 从共享映像创建池，请使用 `az batch pool create` 命令。 在 `--image` 字段中指定共享映像 ID。 确保 OS 类型和 SKU 与通过 `--node-agent-sku-id` 指定的版本匹配

> [!NOTE]
> 需要使用 Azure AD 进行身份验证。 如果使用共享密钥身份验证，则会出现身份验证错误。  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>使用 C# 从共享映像创建池

也可使用 C# SDK 从共享映像创建池。

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

## <a name="create-a-pool-from-a-shared-image-using-python"></a>使用 Python 从共享映像创建池

还可以使用 Python SDK 从共享映像创建池： 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>使用 Azure 门户从共享映像创建池

在 Azure 门户中，使用以下步骤从共享映像创建池。

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 转到“Batch 帐户”****，然后选择你的帐户。
1. 选择“池”，然后选择“添加”，以便创建新池。********
1. 在“映像类型”部分，选择“共享映像库”。********
1. 使用托管映像的相关信息完成剩余部分。
1. 选择“确定”  。

![通过门户使用共享映像创建池。](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>大型池的注意事项

如果打算使用共享映像创建包含数百或数千 VM 或更多 VM 的池，请按以下指南操作。

* **共享映像库副本数目。**  对于实例数多达 600 的每个池，我们建议至少保留一个副本。 例如，如果创建的池包含 3000 个 VM，则应保留至少 5 个映像副本。 我们始终建议保留超出最低要求的副本数，以便获取更佳性能。

* **调整超时。** 如果池包含固定数目的节点（如果池不会自动缩放），请根据池大小增大池的 `resizeTimeout` 属性的值。 建议将每 1000 个 VM 的重设大小超时设置为至少 15 分钟。 例如，如果池包含 2000 个 VM，建议将重设大小超时设置为至少 30 分钟。

## <a name="next-steps"></a>后续步骤

* 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
