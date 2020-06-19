---
title: 从托管映像预配自定义池
description: 从托管映像资源创建 Batch 池，以使用软件和数据为应用程序预配计算节点。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: fbb336ff9d3d53cc53004c577e291afdba7702f6
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847984"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>使用托管映像创建虚拟机池

若要为 Batch 池的虚拟机 (VM) 创建自定义映像，可以使用托管映像创建[共享映像库](batch-sig-images.md)。 还支持只使用托管映像，但仅适用于最高 2019-08-01（含）的 API 版本。

> [!IMPORTANT]
> 在大多数情况下，应使用共享映像库创建自定义映像。 使用共享映像库可以更快地预配池、缩放更大数量的 VM 以及在预配 VM 时提高可靠性。 若要了解详细信息，请参阅[使用共享映像库创建自定义池](batch-sig-images.md)。

## <a name="prerequisites"></a>先决条件

- **托管映像资源**。 若要使用自定义映像创建虚拟机池，需在 Batch 帐户所在的同一 Azure 订阅和区域中使用或创建托管映像资源。 应该基于 VM 的 OS 磁盘快照及其附加的数据磁盘（可选）创建该映像。
  - 对创建的每个池使用唯一的自定义映像。
  - 若要使用 Batch API 创建包含映像的池，请指定映像的**资源 ID**，其格式为 `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`。
  - 托管映像资源应该在池的生存期内存在，以便能够纵向扩展，并可在删除池后将其删除。

- Azure Active Directory (Azure AD) 身份验证。 Batch 客户端 API 必须使用 Azure AD 身份验证。 有关 Azure AD 的 Azure Batch 支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。

## <a name="prepare-a-custom-image"></a>准备自定义映像

在 Azure 中，可以通过以下项来准备托管映像：

- Azure VM 的 OS 和数据磁盘快照
- 包含托管磁盘的通用 Azure VM
- 已上传到云的通用本地 VHD

若要使用托管映像可靠地缩放 Batch 池，建议仅使用第一种方法创建托管映像：使用 VM 磁盘的快照。 以下步骤展示了如何准备 VM、创建快照，然后基于该快照创建托管映像。

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

若要基于快照创建托管映像，请使用 Azure 命令行工具，例如 [az image create](/cli/azure/image) 命令。 可以通过指定 OS 磁盘快照并选择性地指定一个或多个数据磁盘快照来创建映像。

## <a name="create-a-pool-from-a-custom-image"></a>通过自定义映像创建池

找到托管映像的资源 ID 后，便可通过该映像创建自定义映像池。 以下步骤演示如何使用 Batch 服务或 Batch 管理创建自定义映像池。

> [!NOTE]
> 确保用于 Azure AD 身份验证的标识对映像资源拥有权限。 请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。
>
> 托管映像的资源必须在池的生存期内存在。 如果删除了基础资源，则无法缩放池。

### <a name="batch-service-net-sdk"></a>Batch 服务 .NET SDK

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>Batch 管理 REST API

REST API URI

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

请求正文

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>大型池的注意事项

如果你打算使用自定义映像创建包含数百个或更多 VM 的池，必须遵照前面的指导使用基于 VM 快照创建的映像。

另请注意以下注意事项：

- **大小限制** - 使用自定义映像时，Batch 会将池大小限制为 2500 个专用计算节点，或 1000 个低优先级节点。

  如果使用相同的映像（或基于同一基础快照的多个映像）来创建多个池，则池中的计算节点总数不能超过上述限制。 不建议将某个映像或其基础快照用于多个池。

  如果使用[入站 NAT 池](pool-endpoint-configuration.md)来配置池，可以降低限制。

- **调整超时** - 如果池包含固定数目的节点（不会自动缩放），请增大 resizeTimeout 属性的值，例如 20-30 分钟。 如果在超时期限内池未达到其目标大小，请再次执行[调整大小操作](/rest/api/batchservice/pool/resize)。

  如果你打算创建包含 300 个以上的计算节点的池，可能需要多次调整池大小才能达到目标大小。
  
使用[共享映像库](batch-sig-images.md)可以通过自定义映像和更多的共享映像副本创建更大的池。 使用共享映像，池达到稳定状态所用的时间最多可加快 25%，并且 VM 空闲延迟时间最多可缩短 30%。

## <a name="considerations-for-using-packer"></a>使用 Packer 的注意事项

只能通过用户订阅模式 Batch 帐户来直接使用 Packer 创建托管映像资源。 对于 Batch 服务模式帐户，需要首先创建 VHD，然后将 VHD 导入托管映像资源。 根据池分配模式（用户订阅或 Batch 服务），创建托管映像资源的步骤会有所不同。

确保用于创建托管映像的资源在引用自定义映像的任何池的生存期内存在。 否则可能会导致池分配失败和/或重设大小失败。

如果删除了图像或基础资源，则可能会收到类似于以下内容的错误：`There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`。 如果收到此错误，请确保未删除基础资源。

有关使用 Packer 创建 VM 的详细信息，请参阅[使用 Packer 构建 Linux 映像](../virtual-machines/linux/build-image-with-packer.md)或[使用 Packer 构建 Windows 映像](../virtual-machines/windows/build-image-with-packer.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[共享映像库](batch-sig-images.md)创建自定义池。
- 有关 Batch 的详细概述，请参阅 [Batch 服务工作流和资源](batch-service-workflow-features.md)。
