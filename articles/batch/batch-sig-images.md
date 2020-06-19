---
title: 使用共享映像库创建自定义池
description: 自定义映像是配置计算节点以运行 Batch 工作负载的高效方法。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 6731086bfcbe6a671c579593791fb7467b280bca
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844482"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>使用共享映像库创建自定义池

使用虚拟机配置创建 Azure Batch 池时，需指定一个虚拟机 (VM) 映像，为池中每个计算节点提供操作系统。 可以使用受支持的 Azure 市场映像创建虚拟机池，或者使用[共享映像库](../virtual-machines/windows/shared-image-galleries.md)创建自定义映像。

## <a name="benefits-of-the-shared-image-gallery"></a>共享映像库的优点

使用共享映像库创建自定义映像时，可以控制操作系统类型和配置，以及数据磁盘的类型。 共享映像可以包含应用程序和引用数据，Batch 池节点预配好后即可使用这些数据。

还可根据环境的需要创建多个映像版本。 在使用映像版本创建 VM 时，将使用映像版本来创建 VM 的新磁盘。

在准备池的计算节点以运行 Batch 工作负荷时，使用共享映像可以节省时间。 可以在每个计算节点（预配后）上使用 Azure 市场映像和安装软件，但使用共享映像通常更加高效。 此外，还可以为共享映像指定多个副本，如此，在创建包含多个 VM（600 个以上 VM）的池时，可以节省创建池的时间。

使用根据方案配置的共享映像可提供几个优点：

- 在区域间使用相同的映像。 可以在不同区域间创建共享映像副本，以便所有池都使用同一个映像。
- 配置操作系统 (OS)。 可以自定义映像操作系统磁盘的配置。
- **预安装应用程序。** 在 OS 磁盘中预装应用程序，与使用启动任务预配计算节点后再安装应用程序相比，这种方法更加高效，且不容易出错。
- 一次复制大量的数据。 将静态数据复制到托管映像的数据磁盘，使这些数据成为托管的共享映像的一部分。 只需执行此操作一次，然后，数据可供池的每个节点使用。
- 扩大池的大小。 利用共享映像库，可以通过自定义映像和更多的共享映像副本创建更大的池。
- 性能优于自定义映像。 使用共享映像，池达到稳定状态所用的时间最多可加快 25%，并且 VM 空闲延迟时间最多可缩短 30%。
- 对映像进行版本控制和分组，以便于管理。 映像分组定义包含有关创建映像的原因、其适用的 OS 以及有关使用映像的信息。 通过对映像进行分组，可以更轻松地管理映像。 有关详细信息，请参阅[映像定义](../virtual-machines/windows/shared-image-galleries.md#image-definitions)。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> 需要使用 Azure AD 进行身份验证。 如果使用共享密钥身份验证，则将收到一个身份验证错误。  

- 一个 Azure Batch 帐户。 若要创建 Batch 帐户，请参阅 Batch 快速入门（使用 [Azure 门户](quick-create-portal.md)或 [Azure CLI](quick-create-cli.md)）。

- 共享映像库映像。 若要创建共享映像，需要具有或创建托管的映像资源。 应该基于 VM 的 OS 磁盘快照及其附加的数据磁盘（可选）创建该映像。

> [!NOTE]
> 共享映像必须与 Batch 帐户位于同一订阅中。 只要映像的副本与 Batch 帐户位于同一区域，该映像就可以位于不同区域中。

## <a name="prepare-a-custom-image"></a>准备自定义映像

在 Azure 中，可以通过以下项来准备自定义映像：

- Azure VM 的 OS 和数据磁盘快照
- 包含托管磁盘的通用 Azure VM
- 已上传到云的通用本地 VHD

> [!NOTE]
> 目前，Batch 仅支持通用共享映像。 此时无法通过专用共享映像创建自定义映像池。

以下步骤展示了如何准备 VM、创建快照，然后基于该快照创建映像。

### <a name="prepare-a-vm"></a>准备 VM

若要为映像创建新 VM，请使用 Batch 支持的第一方 Azure 市场映像作为托管映像的基础映像。 只有第一方映像才能用作基础映像。 若要获取 Azure Batch 支持的 Azure 市场映像参考的完整列表，请参阅[列出节点代理 SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 操作。

> [!NOTE]
> 不能使用具有附加许可和购买条款的第三方映像作为基础映像。 有关这些市场映像的信息，请参阅 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) 或 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) VM 指南。

- 确保使用托管磁盘创建 VM。 这是创建 VM 时的默认存储设置。
- 不要在 VM 上安装自定义脚本扩展等 Azure 扩展。 如果映像包含预装的扩展，在部署 Batch 池时 Azure 可能会遇到问题。
- 使用附加的数据磁盘时，需要在 VM 中装载并格式化这些磁盘，然后才能使用这些磁盘。
- 确保所提供的基础 OS 映像使用默认临时驱动器。 Batch 节点代理目前需要使用默认的临时驱动器。
- VM 开始运行后，请通过 RDP（适用于 Windows）或 SSH（适用于 Linux）进行连接。 安装所需的任何软件，或复制所需的数据。  

### <a name="create-a-vm-snapshot"></a>创建 VM 快照

快照是 VHD 的完整只读副本。 若要创建 VM OS 磁盘或数据磁盘的快照，可以使用 Azure 门户或命令行工具。 有关创建快照的步骤和选项，请参阅适用于 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 或 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 的指导。

### <a name="create-an-image-from-one-or-more-snapshots"></a>基于一个或多个快照创建映像

若要基于快照创建托管映像，请使用 Azure 命令行工具，例如 [az image create](/cli/azure/image) 命令。 通过指定 OS 磁盘快照并选择性地指定一个或多个数据磁盘快照来创建映像。

### <a name="create-a-shared-image-gallery"></a>创建共享映像库

成功创建托管映像后，需要创建一个共享映像库，以便可以使用自定义映像。 若要了解如何为映像创建共享映像库，请参阅[使用 Azure CLI 创建共享映像库](../virtual-machines/linux/shared-images.md)或[使用 Azure 门户创建共享映像库](../virtual-machines/linux/shared-images-portal.md)。

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>使用 Azure CLI 通过共享映像创建池

若要使用 Azure CLI 通过共享映像创建池，请使用 `az batch pool create` 命令。 在 `--image` 字段中指定共享映像 ID。 确保 OS 类型和 SKU 与 `--node-agent-sku-id` 指定的版本匹配

> [!NOTE]
> 需要使用 Azure AD 进行身份验证。 如果使用共享密钥身份验证，则将收到一个身份验证错误。  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>使用 C# 通过共享映像创建池

或者，可以使用 C# SDK 通过共享映像创建池。

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

## <a name="create-a-pool-from-a-shared-image-using-python"></a>使用 Python 通过共享映像创建池

还可以使用 Python SDK 通过共享映像创建池： 

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>使用 Azure 门户通过共享映像创建池

使用以下步骤在 Azure 门户中通过共享映像创建池。

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 转到“Batch 帐户”并选择帐户。
1. 依次选择“池”和“添加”，以创建新池 。
1. 在“映像类型”部分中，选择“共享映像库” 。
1. 使用托管映像的相关信息完成剩余部分。
1. 选择“确定”。

![使用门户通过共享映像创建池。](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>大型池的注意事项

如果计划使用共享映像创建包含数百或数千或者更多 VM 的池，请使用以下指南。

- 共享映像库副本数。  对于具有多达 600 个实例的每个池，建议至少保留一个副本。 例如，如果要创建包含 3000 个 VM 的池，则应至少保留映像的 5 个副本。 我们始终建议保留比最低要求更多的副本，以便获得更好的性能。

- 调整超时。 如果池包含固定数量的节点（如果不自动缩放），请根据池的大小增加池的 `resizeTimeout` 属性。 对于每 1000 个 VM，建议的调整超时值至少为 15 分钟。 例如，对于包含 2000 个 VM 的池，建议的调整超时值至少为 30 分钟。

## <a name="next-steps"></a>后续步骤

- 有关 Batch 的详细概述，请参阅 [Batch 服务工作流和资源](batch-service-workflow-features.md)。
- 了解[共享映像库](../virtual-machines/windows/shared-image-galleries.md)。
