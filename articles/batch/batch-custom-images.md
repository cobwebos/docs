---
title: 从托管映像预配自定义池-Azure Batch |Microsoft Docs
description: 从托管映像资源创建 Batch 池，以使用应用程序的软件和数据预配计算节点。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: lahugh
ms.openlocfilehash: 82a5f79ca7b7a16cd8f7294ebd1f70816b40ad82
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "73519263"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>使用托管映像创建虚拟机池

若要为 Batch 池的虚拟机（Vm）创建自定义映像，可以使用[共享映像库](batch-sig-images.md)或*托管映像*资源。

> [!TIP]
> 在大多数情况下，应使用共享映像库创建自定义映像。 使用共享映像库，可以更快地预配池、缩放更大数量的 Vm，并在预配 Vm 时提高可靠性。 若要了解详细信息，请参阅[使用共享映像库创建自定义池](batch-sig-images.md)。

## <a name="prerequisites"></a>必备组件

- **托管映像资源**。 若要使用自定义映像创建虚拟机池，需在 Batch 帐户所在的同一 Azure 订阅和区域中使用或创建托管映像资源。 应该基于 VM 的 OS 磁盘快照及其附加的数据磁盘（可选）创建该映像。 有关准备托管映像的详细信息和步骤，请参阅以下部分。
  - 对创建的每个池使用唯一的自定义映像。
  - 若要使用 Batch API 创建包含映像的池，请指定映像的**资源 ID**，其格式为 `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`。 若要使用门户，请使用映像的**名称**。  
  - 托管映像资源应该在池的生存期内存在，以便能够纵向扩展，并可在删除池后将其删除。

- **Azure Active Directory (AAD) 身份验证**。 Batch 客户端 API 必须使用 AAD 身份验证。 有关 Azure Batch 对 AAD 的支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。

## <a name="prepare-a-custom-image"></a>准备自定义映像

在 Azure 中，可以从以下项准备托管映像：

- Azure VM 的 OS 和数据磁盘的快照
- 带托管磁盘的通用 Azure VM
- 已将通用本地 VHD 上传到云

若要使用自定义映像可靠缩放 Batch 池，我们建议仅使用第一种方法创建托管映像：使用 VM 磁盘的快照。 参阅以下步骤来准备 VM、创建快照，然后基于该快照创建映像。

### <a name="prepare-a-vm"></a>准备 VM

若要为映像创建新 VM，请使用 Batch 支持的第一方 Azure 市场映像作为托管映像的基础映像。 仅第一方映像可以用作基础映像。 若要获取 Azure Batch 支持的 Azure 市场映像参考的完整列表，请参阅[列出节点代理 SKU](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 操作。

> [!NOTE]
> 不能使用具有附加许可和购买条款的第三方映像作为基础映像。 有关这些市场映像的信息，请参阅 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) 或 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM 指南。

- 确保使用托管磁盘创建 VM。 这是创建 VM 时的默认存储设置。
- 不要在 VM 上安装自定义脚本扩展等 Azure 扩展。 如果映像包含预装的扩展，在部署 Batch 池时 Azure 可能会遇到问题。
- 使用附加的数据磁盘时，需要从 VM 中装载和格式化磁盘，才能使用它们。
- 确保所提供的基础 OS 映像使用默认临时驱动器。 Batch 节点代理目前需要使用默认的临时驱动器。
- VM 开始运行后，请通过 RDP（适用于 Windows）或 SSH（适用于 Linux）进行连接。 安装所需的任何软件，或复制所需的数据。  

### <a name="create-a-vm-snapshot"></a>创建 VM 快照

快照是 VHD 的完整只读副本。 若要创建 VM OS 磁盘或数据磁盘的快照，可以使用 Azure 门户或命令行工具。 有关创建快照的步骤和选项，请参阅适用于 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 或 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM 的指导。

### <a name="create-an-image-from-one-or-more-snapshots"></a>基于一个或多个快照创建映像

若要基于快照创建托管映像，请使用 Azure 命令行工具，例如 [az image create](/cli/azure/image) 命令。 可以通过指定 OS 磁盘快照并选择性地指定一个或多个数据磁盘快照来创建映像。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 门户中使用自定义映像创建池

如果已保存自定义映像，并且知道其资源 ID 或名称，请基于该映像创建 Batch 池。 以下步骤说明如何从 Azure 门户创建池。

> [!NOTE]
> 若要使用某个 Batch API 创建池，请确保用于 AAD 身份验证的标识对映像资源拥有权限。 请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。
>
> 托管映像的资源必须在池的生存期内存在。 如果删除了基础资源，将无法缩放池。

1. 导航到 Azure 门户中的批处理帐户。 此帐户必须与包含自定义映像的资源组在同一订阅和区域中。
2. 在左侧的“设置”窗口中，选择“池”菜单项。
3. 在“池”窗口中，选择“添加”命令。
4. 在“添加池”窗口中，从“映像类型”下拉列表中选择“自定义映像(Linux/Windows)”。 在“自定义 VM 映像”下拉列表中，选择映像名称（资源 ID 的短格式）。
5. 为自定义映像选择正确的“发布服务器/产品/SKU”。
6. 指定剩余的必需设置，包括**节点大小**、**目标专用节点**和**低优先级节点**，以及任何所需的可选设置。

    例如，对于 Microsoft Windows Server Datacenter 2016 自定义映像，会显示“添加池”窗口，如下所示：

    ![从自定义 Windows 映像添加池](media/batch-custom-images/add-pool-custom-image.png)
  
要检查现有池是否基于自定义映像，请查看“池”窗口的资源摘要部分中的“操作系统”属性。 如果池是从自定义映像创建的，该属性会设置为“自定义 VM 映像”。

与池关联的所有自定义映像已显示在池的“属性”窗口中。

## <a name="considerations-for-large-pools"></a>大型池的注意事项

如果你打算使用自定义映像创建包含数百个或更多 VM 的池，必须遵照前面的指导使用基于 VM 快照创建的映像。

另外，请注意以下事项：

- **大小限制** - 使用自定义映像时，Batch 会将池大小限制为 2500 个专用计算节点，或 1000 个低优先级节点。

  如果使用相同的映像（或基于同一基础快照的多个映像）来创建多个池，则池中的计算节点总数不能超过上述限制。 不建议将某个映像或其基础快照用于多个池。

  如果使用[入站 NAT 池](pool-endpoint-configuration.md)来配置池，可以降低限制。

- **重设大小超时** - 如果池包含固定数目的节点（不会自动缩放），请增大池的 resizeTimeout 属性的值，例如增大到 20-30 分钟。 如果在超时期限内池未达到其目标大小，请再次执行[调整大小操作](/rest/api/batchservice/pool/resize)。

  如果你打算创建包含 300 个以上的计算节点的池，可能需要多次调整池大小才能达到目标大小。
  
通过使用[共享映像库](batch-sig-images.md)，你可以使用自定义映像和更多共享映像副本创建更大的池。 使用共享映像时，池达到稳定状态所需的时间最多可以缩短 25%，VM 空闲延迟最多可以缩短 30%。

## <a name="considerations-for-using-packer"></a>使用 Packer 的注意事项

只能使用用户订阅模式批处理帐户来创建直接使用 Packer 的托管映像资源。 对于 Batch 服务模式帐户，需要首先创建 VHD，然后将 VHD 导入到托管映像资源。 根据池分配模式（用户订阅或批处理服务），创建托管映像资源的步骤会有所不同。

确保用于创建托管映像的资源存在于引用自定义映像的任何池的生存期。 否则，可能会导致池分配失败和/或重设故障大小。

如果删除了图像或基础资源，可能会收到类似于以下内容的错误： `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`。 如果收到此错误，请确保未删除基础资源。

有关使用 Packer 创建 VM 的详细信息，请参阅使用[Packer 生成 Linux 映像](../virtual-machines/linux/build-image-with-packer.md)或[使用 Packer 生成 Windows 映像](../virtual-machines/windows/build-image-with-packer.md)。

## <a name="next-steps"></a>后续步骤

有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
