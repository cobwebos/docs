---
title: "根据自定义映像预配 Azure Batch 池 | Microsoft Docs"
description: "可根据自定义映像创建 Batch 池，以预配包含应用程序所需的软件和数据的计算节点。 自定义映像是配置计算节点以运行 Batch 工作负载的高效方法。"
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3d655766b4f2a5efb0c8c29ffa81a89f84b3e17c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---

# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>使用自定义映像创建虚拟机池

在 Azure Batch 中创建虚拟机池时，需指定一个虚拟机 (VM) 映像，为池中每个计算节点提供操作系统。 可通过使用 Azure Marketplace 映像，或提供已准备好的自定义映像 VHD 来创建虚拟机池。 提供自定义映像时，可在预配每个计算节点时控制如何配置操作系统。 自定义映像还可包括应用程序和引用数据，计算节点预配好后就可以使用这些数据。

在准备池的计算节点以运行 Batch 工作负载时，使用自定义映像可以节省大量时间。 虽然始终可以在每个计算节点（已预配后）上使用 Azure Marketplace 映像和安装软件，此方法不如使用自定义映像有效。 

使用为自己的方案配置的自定义映像的一些原因包括需要：

- **配置操作系统 (OS)** 可对自定义映像执行操作系统的任何特殊配置。 
- **安装大型应用程序。** 与先预配每个计算节点，然后在其上安装应用程序相比，在自定义映像上安装应用程序更加高效。
- **复制大量数据。** 如果将数据复制到自定义映像，仅需复制一次，而不用复制到每个计算节点，节省了时间和带宽。
- **在安装过程中重启 VM。** 重启 VM 可能是一个耗时的过程，尤其是有大量计算节点时。

## <a name="prerequisites"></a>先决条件

- **使用“用户订阅”池分配模式创建的 Batch 帐户。** 若要使用自定义映像预配虚拟机池，请使用“用户订阅”[池分配模式](batch-api-basics.md#pool-allocation-mode)创建 Batch 帐户。 使用此模式，Batch 池将分配到帐户所在的订阅中。 有关在创建 Batch 帐户时如何设置池分配模式的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)中的[帐户](batch-api-basics.md#account)部分。

- 一个 Azure 存储帐户。 要使用自定义映像创建虚拟机池，需要在同一订阅和区域中拥有标准的通用 Azure 存储帐户。 如果从 Azure VM 中创建自定义映像，则会将映像复制到 VM 的操作系统磁盘所在的存储帐户，无需创建单独的存储帐户。 
    
## <a name="prepare-a-custom-image"></a>准备自定义映像

若要准备用于 Batch 的自定义映像，必须使现有安装的 Linux 或 Windows 通用化。 通用化操作系统安装将删除计算机特定的信息。 其结果是可在其他计算机或 VM 上安装映像。  

> [!IMPORTANT]
> Batch 目前不支持使用 Azure 托管映像来预配池。 用于预配池的自定义映像必须存储在 Azure 存储中。 
>
> 在准备自定义映像时，请牢记以下几点：
> - 确保用于预配 Batch 池的基础 OS 映像没有任何预安装的 Azure 扩展，例如自定义脚本扩展。 如果映像包含预安装的扩展，Azure 可能会在部署 VM 时遇到问题。
> - 确保所提供的基础 OS 映像使用默认的临时驱动器，因为 Batch 节点代理目前需要使用默认的临时驱动器。
>
>

可使用任何已准备好的现有 Windows 或 Linux 映像作为自定义映像。 例如，如果希望使用本地映像，请使用 [AzCopy](../storage/storage-use-azcopy.md) 或其他上传工具，将映像上传到与 Batch 帐户在同一订阅和区域中的 Azure 存储帐户。

还可以从新的或现有 Azure VM 准备自定义映像。 如果要创建新的 VM，可将 Azure Marketplace 映像用作自定义映像的基础映像，然后对其进行自定义。 若要自定义基础映像，请创建 Azure VM，并向其添加应用程序或数据。 然后，通用化 VM 以用作自定义映像，并将其保存到 Azure 存储。 

要从 Azure VM 准备自定义映像，请执行以下步骤：

1. 从 Azure Marketplace 映像创建非托管 Azure VM。 Azure Marketplace 包括适用于 [Windows](../virtual-machines/windows/quick-create-portal.md) 和 [Linux](../virtual-machines/linux/quick-create-portal.md) 的映像。
    
    在 VM 创建过程的步骤 3 中，请确保为“存储”下的“使用托管磁盘”选项选择“否”。 另外，请记下 VM 操作系统磁盘的存储帐户名称，因为 Azure 会在此存储帐户中存储自定义映像：

    ![创建非托管 VM 并记下存储帐户名称](media/batch-custom-images/vm-create-storage.png)
 
2. 完成创建 VM 的过程，并等待 Azure 分配它。 下面的映像表明 Azure 门户中某个 VM 的状态为正在运行：

    ![从 Marketplace 映像创建 VM](media/batch-custom-images/vm-status-running.png)

3. VM 开始运行后，请通过 RDP（适用于 Windows）或 SSH（适用于 Linux）进行连接。 安装所需的任何软件，或复制所需数据，然后通用化 VM。 请按照 [Generalize the VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm)（通用化 VM）中描述的步骤进行操作。 
   
4. 请按照相关步骤[登录到 Azure PowerShell](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell)。 要安装 Azure PowerShell，请参阅 [Overview of Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0)（Azure PowerShell 概述）。 

5. 接下来，请按照[解除分配 VM 并将状态设置为通用化](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized)中的步骤进行操作。 

    在 Azure 门户中，请注意 VM 已解除分配：

    ![确保 VM 已解除分配](media/batch-custom-images/vm-status-deallocated.png)

6.  使用 [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) PowerShell cmdlet 创建 VM 映像，并将其保存到 Azure 存储。 请遵循[创建映像](../virtual-machines/windows/sa-copy-generalized.md#create-the-image)中的说明操作。
    
    VM 映像保存在创建 VM 时创建的 Azure 存储帐户中，如此过程的步骤 1 所示。 Save-AzureRmVMImage cmdlet 将映像保存到该存储帐户的“系统”容器中。 `-DestinationContainername` 参数为“系统”容器内的一个虚拟目录命名。 `-VHDNamePrefix` 参数为 blob 名称指定前缀。 此前缀与 blob 名称之间带有连字符。 

    例如，假设使用以下参数调用 Save-AzureRmVMImage：  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    生成的图像的保存位置和 blob 名称如下图所示：

    ![VHD 在系统容器中的保存位置](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Azure 非托管 VM 会针对不同用途创建多个存储帐户。 如果创建 VM 时未记下操作系统磁盘的存储容器名称，请查找包含“系统”容器的相关存储帐户。 通过“系统”容器导航，使用为“Save-AzureRmVMImage”命令指定的值查找自定义映像。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 门户中使用自定义映像创建池

如果已保存自定义映像，并且知道其位置，可以从该映像创建 Batch 池。 请按照下列步骤在 Azure 门户中创建池：

1. 导航到 Azure 门户中的批处理帐户。 此帐户必须与包含自定义映像的存储帐户在同一订阅和区域中。 
2. 在左侧的“设置”窗口中，选择“池”菜单项。
3. 在“池”窗口中，选择“添加”命令。
4. 在“添加池”窗口中，从“映像类型”下拉列表中选择“自定义映像(Linux/Windows)”。 门户将显示“自定义映像”选取器。 导航到自定义映像所在的存储帐户，然后从容器中选择所需 VHD。 
5. 为自定义 VHD 选择正确的发布服务器/付费选项/SKU。
6. 指定剩余所需设置，包括“节点大小”、“目标专用节点”和“低优先级节点”，以及任何所需的可选设置。

    例如，对于 Microsoft Windows Server Datacenter 2016 自定义映像，显示“添加池”窗口，如下图所示：

    ![从自定义 Windows 映像添加池](media/batch-custom-images/add-pool-custom-image.png)
  
要检查现有池是否基于自定义映像，请查看“池”窗口的资源摘要部分中的“操作系统”属性。 如果池是从自定义映像创建的，该属性会设置为“自定义 VM 映像”。

与池关联的所有自定义 VHD 都显示在池的“属性”窗口中。
 
## <a name="next-steps"></a>后续步骤

- 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
- 有关创建 Batch 帐户的信息，请参阅[使用 Azure 门户创建 Batch 帐户](batch-account-create-portal.md)。
