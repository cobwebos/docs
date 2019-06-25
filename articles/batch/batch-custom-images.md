---
title: 基于自定义映像预配 Azure Batch 池 | Microsoft Docs
description: 基于自定义映像创建 Batch 池，以预配包含应用程序所需的软件和数据的计算节点。 自定义映像是配置计算节点以运行 Batch 工作负载的高效方法。
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 886dea0e53519870aaa27dea721a9eb78515cf86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706330"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>使用自定义映像创建虚拟机池 

使用虚拟机配置创建 Azure Batch 池时，需指定一个虚拟机 (VM) 映像，为池中每个计算节点提供操作系统。 可以使用受支持的 Azure 市场映像或自定义映像（自行创建并配置的 VM 映像）创建虚拟机池。 自定义映像必须是 Batch 帐户所在的同一个 Azure 订阅和区域中的托管映像资源。 

## <a name="benefits-of-custom-images"></a>自定义映像的优点

提供自定义映像时，可以控制操作系统配置，以及要使用的操作系统和数据磁盘的类型。 自定义映像可以包含应用程序和引用数据，Batch 池节点预配好后即可使用这些数据。

在准备池的计算节点以运行 Batch 工作负荷时，使用自定义映像可以节省时间。 虽然可以在每个计算节点（预配后）上使用 Azure 市场映像和安装软件，但使用自定义映像可能更加高效。

使用根据方案配置的自定义映像可提供几个优点：

- **配置操作系统 (OS)** 。 可以自定义映像操作系统磁盘的配置。 
- **预安装应用程序。** 在 OS 磁盘中预装应用程序，与使用启动任务预配计算节点后再安装应用程序相比，这种方法更加高效，且不容易出错。
- **节省 VM 上的重新启动时间。** 安装应用程序通常需要重新启动 VM，这是一个耗时的过程。 预安装应用程序可节省重新启动时间。 
- **一次复制极大量的数据。** 将静态数据复制到托管映像的数据磁盘，使这些数据成为托管的自定义映像的一部分。 只需执行此操作一次，然后，数据可供池的每个节点使用。
- **选择磁盘类型。** 可以为 OS 磁盘和数据磁盘使用高级存储。
- **扩大池的大小。** 使用托管的自定义映像创建池时，该池可以扩大，而无需创建映像 Blob VHD 的副本。

## <a name="prerequisites"></a>必备组件

- **托管映像资源**。 若要使用自定义映像创建虚拟机池，需在 Batch 帐户所在的同一 Azure 订阅和区域中使用或创建托管映像资源。 应该基于 VM 的 OS 磁盘快照及其附加的数据磁盘（可选）创建该映像。 有关准备托管映像的详细信息和步骤，请参阅以下部分。
  - 对创建的每个池使用唯一的自定义映像。
  - 若要使用 Batch API 创建包含映像的池，请指定映像的**资源 ID**，其格式为 `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`。 若要使用门户，请使用映像的**名称**。  
  - 托管映像资源应该在池的生存期内存在，以便能够纵向扩展，并可在删除池后将其删除。

- **Azure Active Directory (AAD) 身份验证**。 Batch 客户端 API 必须使用 AAD 身份验证。 有关 Azure Batch 对 AAD 的支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。

## <a name="prepare-a-custom-image"></a>准备自定义映像

在 Azure 中，可以基于 Azure VM 的 OS 和数据磁盘快照、包含托管磁盘的通用化 Azure VM 或者上传的通用化本地 VHD 来准备托管映像。 若要使用自定义映像可靠缩放 Batch 池，我们建议仅使用第一种方法创建托管映像：使用 VM 磁盘的快照。  参阅以下步骤来准备 VM、创建快照，然后基于该快照创建映像。

### <a name="prepare-a-vm"></a>准备 VM

若要为映像创建新 VM，请使用 Batch 支持的第一方 Azure 市场映像作为托管映像的基础映像。 仅第一方映像可以用作基础映像。 若要获取 Azure Batch 支持的 Azure 市场映像参考的完整列表，请参阅[列出节点代理 SKU](/rest/api/batchservice/account/listnodeagentskus) 操作。

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

若要基于快照创建托管映像，请使用 Azure 命令行工具，例如 [az image create](/cli/azure/image) 命令。 可以通过指定 OS 磁盘快照并选择性地指定一个或多个数据磁盘快照来创建映像。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 门户中使用自定义映像创建池

如果已保存自定义映像，并且知道其资源 ID 或名称，请基于该映像创建 Batch 池。 以下步骤说明如何从 Azure 门户创建池。

> [!NOTE]
> 若要使用某个 Batch API 创建池，请确保用于 AAD 身份验证的标识对映像资源拥有权限。 请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。
>
> 托管映像的资源必须在池的生存期内存在。 如果删除了基础资源，将无法缩放池。 

1. 导航到 Azure 门户中的批处理帐户。 此帐户必须与包含自定义映像的资源组在同一订阅和区域中。 
2. 在左侧的“设置”  窗口中，选择“池”  菜单项。
3. 在“池”窗口中，选择“添加”命令。  
4. 在“添加池”窗口中，从“映像类型”下拉列表中选择“自定义映像(Linux/Windows)”。    在“自定义 VM 映像”下拉列表中，选择映像名称（资源 ID 的短格式）。 
5. 为自定义映像选择正确的“发布服务器/产品/SKU”。 
6. 指定剩余所需设置，包括“节点大小”、“目标专用节点”和“低优先级节点”，以及任何所需的可选设置。   

    例如，对于 Microsoft Windows Server Datacenter 2016 自定义映像，会显示“添加池”窗口，如下所示： 

    ![从自定义 Windows 映像添加池](media/batch-custom-images/add-pool-custom-image.png)
  
要检查现有池是否基于自定义映像，请查看“池”窗口的资源摘要部分中的“操作系统”属性。   如果池是从自定义映像创建的，该属性会设置为“自定义 VM 映像”。 

与池关联的所有自定义映像已显示在池的“属性”窗口中。 

## <a name="considerations-for-large-pools"></a>大型池的注意事项

如果你打算使用自定义映像创建包含数百个或更多 VM 的池，必须遵照前面的指导使用基于 VM 快照创建的映像。

另请注意以下几点：

- **大小限制** - 使用自定义映像时，Batch 会将池大小限制为 2500 个专用计算节点，或 1000 个低优先级节点。

  如果使用相同的映像（或基于同一基础快照的多个映像）来创建多个池，则池中的计算节点总数不能超过上述限制。 不建议将某个映像或其基础快照用于多个池。

  如果使用[入站 NAT 池](pool-endpoint-configuration.md)来配置池，可以降低限制。

- **调整超时** - 如果池包含固定数目的节点（不会自动缩放），请增大 resizeTimeout 属性的值，例如 20-30 分钟。 如果在超时期限内池未达到其目标大小，请再次执行[调整大小操作](/rest/api/batchservice/pool/resize)。

  如果你打算创建包含 300 个以上的计算节点的池，可能需要多次调整池大小才能达到目标大小。

## <a name="considerations-for-using-packer"></a>使用 Packer 的注意事项

直接使用 Packer 创建托管的映像资源只能通过用户订阅模式批处理帐户完成。 批处理服务模式下帐户，您需要创建 VHD，然后将 VHD 导入到托管的映像资源。 具体取决于你池分配模式 （用户订阅或 Batch 服务），你的步骤来创建托管的映像资源而异。

请确保用于创建托管的映像资源存在的任何引用自定义映像的池的生存期。 如果不这样做可以导致池分配失败和/或调整大小失败。 

如果删除的图像或基础资源，则您可能会遇到错误类似于： `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`。 如果发生这种情况，请确保尚未删除基础资源。

使用 Packer 创建 VM 的详细信息，请参阅[Linux 使用 Packer 生成映像](../virtual-machines/linux/build-image-with-packer.md)或[Windows 使用 Packer 生成映像](../virtual-machines/windows/build-image-with-packer.md)。

## <a name="next-steps"></a>后续步骤

- 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
