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
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 1248aeeaa159789b008eb003c2cd7babe0432919
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---

# <a name="use-a-custom-image-to-create-a-pool"></a>使用自定义映像创建池

在 Azure Batch 中创建池时，需指定一个虚拟机 (VM) 映像，为池中每个计算节点提供操作系统配置。 可通过使用 Azure Marketplace 映像，或提供已准备好的自定义映像创建池。 提供自定义映像时，可在预配每个计算节点时控制如何配置操作系统。 自定义映像还可包括应用程序和引用数据，计算节点预配好后就可以使用这些数据。

在准备池的计算节点以运行 Batch 工作负载时，使用自定义映像可以节省大量时间。 虽然始终可以在每个计算节点（已预配后）上使用 Azure Marketplace 映像和安装软件，此方法不如使用自定义映像有效。 如果需要安装大型应用程序、复制大量数据，或在安装过程期间需要重新启动 VM，请考虑使用配置的自定义映像以满足需求。  

## <a name="prerequisites"></a>先决条件

- **使用“用户订阅”池分配模式创建的 Batch 帐户。** 若要使用自定义映像预配虚拟机池，请使用“用户订阅”[池分配模式](batch-api-basics.md#pool-allocation-mode)创建 Batch 帐户。 使用此模式，Batch 池将分配到帐户所在的订阅中。 有关在创建 Batch 帐户时如何设置池分配模式的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)中的[帐户](batch-api-basics.md#account)部分。

- 一个 Azure 存储帐户。 若要使用自定义映像创建虚拟机配置池，需要一个或多个标准 Azure 存储帐户来存储自定义 VHD 映像。 自定义映像存储为 Blob。 若要在创建池时引用自定义映像，请为 [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf) 属性的 [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) 属性指定自定义映像 VHD Blob 的 URI。

    确保存储帐户满足以下条件：   
    
    - 包含自定义映像 VHD Blob 的存储帐户必须与批处理帐户（用户订阅）位于同一订阅中。
    - 指定的存储帐户必须与批处理帐户位于同一区域。
    - 当前仅支持标准型通用存储帐户。 未来将支持 Azure 高级存储。
    - 可以指定具有多个自定义 VHD Blob 的一个存储帐户，也可以指定每个都有一个 Blob 的多个存储帐户。 建议使用多个存储帐户来获得更好的性能。
    - 一个唯一的自定义映像 VHD Blob 最多可支持 40 个 Linux VM 实例或 20 个 Windows VM 实例。 可创建 VHD Blob 的副本以创建具有更多 VM 的池。 例如，具有 200 个 Windows VM 的池需要为 **osDisk** 属性指定 10 个唯一的 VHD Blob。
    
## <a name="prepare-a-custom-image"></a>准备自定义映像

若要准备用于 Batch 的自定义映像，必须使现有安装的 Linux 或 Windows 通用化。 通用化操作系统安装将删除计算机特定的信息。 其结果是可在其他计算机或 VM 上安装映像。  

可将 Azure Marketplace 映像用作自定义映像的基础映像。 若要自定义基础映像，请创建 Azure VM，并向其添加应用程序或数据。 然后，通用化 VM 以用作自定义映像。   

有关从 Azure VM 准备自定义 Linux 映像的信息，请参阅[如何创建虚拟机或 VHD 的映像](../virtual-machines/linux/capture-image.md)。 

若要了解如何通过 Azure VM 准备自定义 Windows 映像，请参阅[使用 Azure PowerShell 创建自定义 VM 映像](../virtual-machines/windows/tutorial-custom-images.md)和 [Sysprep（系统准备）概述](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。 

> [!IMPORTANT]
> 在准备自定义映像时，请牢记以下原则：
> - 确保用于预配 Batch 池的基础 OS 映像没有任何预安装的 Azure 扩展，例如自定义脚本扩展。 如果映像包含预安装的扩展，Azure 可能会在部署 VM 时遇到问题。
> - 确保所提供的基础 OS 映像使用默认的临时驱动器，因为 Batch 节点代理目前需要使用默认的临时驱动器。
>
>
    
## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 门户中使用自定义映像创建池

使用 Azure 门户从自定义映像创建池：

1. 导航到 Azure 门户中的批处理帐户。
2. 在“设置”边栏选项卡中，选择“池”菜单项。
3. 在“池”边栏选项卡中，选择“添加”命令；随即将显示“添加池”边栏选项卡。
4. 从“映像类型”下拉列表中选择“自定义映像 (Linux/Windows)”。 门户将显示“自定义映像”选取器。 从同一容器中选择一个或多个 VHD，然后单击“选择”按钮。 
   未来版本中会有针对来自不同存储帐户和不同容器的选择 VHD 的支持。
5. 为自定义 VHD 选择正确的“发布服务器/优惠/Sku”，选择所需的“缓存”模式，然后填写用于该池的所有其他参数。
6. 若要检查池是否基于自定义映像，请查看“池”边栏选项卡的资源摘要部分中的“操作系统”属性。 此属性的值应为“自定义 VM 映像”。
7. 与池相关联的所有自定义 VHD 都显示在池的“属性”边栏选项卡中。
 
## <a name="next-steps"></a>后续步骤

- 有关 Batch 深入概述的信息，请参阅[使用 Batch 开发大规模并行计算解决方案](batch-api-basics.md)。
- 有关创建 Batch 帐户的信息，请参阅[使用 Azure 门户创建 Batch 帐户](batch-account-create-portal.md)。
