---
title: "根据自定义映像预配 Azure Batch 池 | Microsoft Docs"
description: "可根据自定义映像创建 Batch 池，以预配包含应用程序所需的软件和数据的计算节点。 自定义映像是配置计算节点以运行 Batch 工作负载的高效方法。"
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 10/11/2017
ms.author: v-dotren
ms.openlocfilehash: d62abd673f89fd51edba721119d1680762a60c76
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>使用托管的自定义映像创建虚拟机池 

使用虚拟机配置创建 Azure Batch 池时，需指定一个虚拟机 (VM) 映像，为池中每个计算节点提供操作系统。 可以使用 Azure Marketplace 映像或自定义映像（自行创建并配置的 VM 映像）创建虚拟机池。 自定义映像必须是 Batch 帐户所在的同一个 Azure 订阅和区域中的托管映像资源。

## <a name="why-use-a-custom-image"></a>为何使用自定义映像？
提供自定义映像时，可以控制操作系统配置，以及要使用的操作系统和数据磁盘的类型。 自定义映像可以包含应用程序和引用数据，Batch 池节点预配好后即可使用这些数据。

在准备池的计算节点以运行 Batch 工作负荷时，使用自定义映像可以节省时间。 虽然可以在每个计算节点（预配后）上使用 Azure Marketplace 映像和安装软件，但使用自定义映像可能更加高效。

使用根据方案配置的自定义映像可提供几个优点：

- **配置操作系统 (OS)**。 可对自定义映像的操作系统磁盘执行特殊的操作系统配置。 
- **预安装应用程序。** 可在 OS 磁盘中创建包含预装应用程序的自定义映像，与使用 StartTask 预配计算节点后再安装应用程序相比，这种方法更加高效，且不容易出错。
- **节省 VM 上的重新启动时间。** 安装应用程序通常需要重新启动 VM，这是一个耗时的过程。 预安装应用程序可节省重新启动时间。 
- **一次复制极大量的数据。** 可将静态数据复制到托管映像的数据磁盘，使这些数据成为托管的自定义映像的一部分。 只需执行此操作一次，然后，数据可供池的每个节点使用。
- **选择磁盘类型。** 可以从 VHD、Azure VM 的托管磁盘、这些磁盘的快照，或者自己配置的 Linux 或 Windows 安装创建托管的自定义映像。 可以为 OS 磁盘和数据磁盘使用高级存储。
- **将池扩大到任何大小。** 使用托管的自定义映像创建池时，池可以扩大到所请求的任何大小。 无需创建映像 Blob VHD 的副本即可适应 VM 的数目。 


## <a name="prerequisites"></a>先决条件

- **托管映像资源**。 若要使用自定义映像创建虚拟机池，需在 Batch 帐户所在的同一 Azure 订阅和区域中创建托管映像资源。 有关准备托管映像的选项，请参阅以下部分。
- **Azure Active Directory (AAD) 身份验证**。 Batch 客户端 API 必须使用 AAD 身份验证。 有关 Azure Batch 对 AAD 的支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。

    
## <a name="prepare-a-custom-image"></a>准备自定义映像
可以从 VHD、包含托管磁盘的 Azure VM 或 VM 快照准备托管映像。 

准备映像时，请记住以下要点：

* 确保用于预配 Batch 池的基础 OS 映像没有任何预安装的 Azure 扩展，例如自定义脚本扩展。 如果映像包含预安装的扩展，Azure 可能会在部署 VM 时遇到问题。
* 确保所提供的基础 OS 映像使用默认临时驱动器。 Batch 节点代理目前需要使用默认的临时驱动器。
* 在 Batch 池的生存期内，无法删除该池引用的托管映像资源。 如果删除托管映像资源，池无法进一步扩大。 

### <a name="to-create-a-managed-image"></a>创建托管映像
可以使用任何已准备好的现有 Windows 或 Linux 操作系统磁盘来创建托管映像。 例如，如果希望使用本地映像，请使用 AzCopy 或其他上传工具，将本地磁盘上传到 Batch 帐户所在的同一订阅和区域中的 Azure 存储帐户。 有关上传 VHD 和创建托管映像的详细步骤，请参阅 [Windows](../virtual-machines/windows/upload-generalized-managed.md) 或 [Linux](../virtual-machines/linux/upload-vhd.md) VM 相关的指导。

还可以从新的或现有的 Azure VM，或者 VM 快照准备托管映像。 

* 若要创建新的 VM，可将 Azure Marketplace 映像用作托管映像的基础映像，然后对其进行自定义。 

* 如果打算使用门户捕获映像，请确保使用托管磁盘创建 VM。 这是创建 VM 时的默认存储设置。

* VM 开始运行后，请通过 RDP（适用于 Windows）或 SSH（适用于 Linux）进行连接。 安装所需的任何软件，或复制所需数据，然后通用化 VM。  

有关通用化 Azure VM 和创建托管映像的步骤，请参阅 [Windows](../virtual-machines/windows/capture-image-resource.md) 或 [Linux](../virtual-machines/linux/capture-image.md) VM 相关的指导。

根据创建包含映像的 Batch 池的预期方式，需要准备好映像的以下标识符：

* 如果打算使用 Batch API 创建包含映像的池，则需要准备好映像的**资源 ID**，其格式为 `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`。 
* 如果打算使用门户，则需要准备好映像的**名称**。 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 门户中使用自定义映像创建池

如果已保存自定义映像，并且知道其资源 ID 或名称，则可以从该映像创建 Batch 池。 以下步骤说明如何从 Azure 门户创建池。

> [!NOTE]
> 若要使用某个 Batch API 创建池，请确保用于 AAD 身份验证的标识对映像资源拥有权限。 请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。
>

1. 导航到 Azure 门户中的批处理帐户。 此帐户必须与包含自定义映像的资源组在同一订阅和区域中。 
2. 在左侧的“设置”窗口中，选择“池”菜单项。
3. 在“池”窗口中，选择“添加”命令。
4. 在“添加池”窗口中，从“映像类型”下拉列表中选择“自定义映像(Linux/Windows)”。 在“自定义 VM 映像”下拉列表中，选择映像名称（资源 ID 的短格式）。
5. 为自定义映像选择正确的“发布服务器/产品/SKU”。
6. 指定剩余所需设置，包括“节点大小”、“目标专用节点”和“低优先级节点”，以及任何所需的可选设置。

    例如，对于 Microsoft Windows Server Datacenter 2016 自定义映像，会显示“添加池”窗口，如下所示：

    ![从自定义 Windows 映像添加池](media/batch-custom-images/add-pool-custom-image.png)
  
要检查现有池是否基于自定义映像，请查看“池”窗口的资源摘要部分中的“操作系统”属性。 如果池是从自定义映像创建的，该属性会设置为“自定义 VM 映像”。

与池关联的所有自定义映像已显示在池的“属性”窗口中。
 
## <a name="next-steps"></a>后续步骤

- 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
