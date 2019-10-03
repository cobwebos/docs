---
title: 适用于第2代 Vm 的 Azure 支持（预览版） |Microsoft Docs
description: 第 2 代 VM 的 Azure 支持概述
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/20/2019
ms.author: lahugh
ms.openlocfilehash: 6bd74fa299385acb1abe4b32db5d35366249eaa6
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173918"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Azure 上的第 2 代 VM 支持（预览版）

> [!IMPORTANT]
> 第 2 代 VM 的 Azure 支持目前以预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 中现已推出第 2 代虚拟机 (VM) 支持预览版。 创建虚拟机后无法更改其代系，因此，在选择代系之前，请先查看本页上的注意事项。

第 2 代 VM 支持第 1 代 VM 所不支持的某些关键特性。 这些特性包括更大的内存、Intel Software Guard Extensions (Intel SGX) 和虚拟化持久性内存 (vPMEM)。 第 2 代 VM 还具有 Azure 中不支持的某些特性。 有关详细信息，请参阅[特性和功能](#features-and-capabilities)部分。

第 2 代 VM 使用新的基于 UEFI 的启动体系结构，而不是使用第 1 代 VM 所用的基于 BIOS 的体系结构。 与第 1 代 VM 相比，第 2 代 VM 的启动时间和安装时间可能有所改善。 有关第 2 代 VM 的概述以及第 1 代与第 2 代之间的某些差异，请参阅[应在 Hyper-V 中创建第 1 代还是第 2 代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第 2 代 VM 的大小

Azure 中的所有 VM 大小都支持第 1 代 VM。 Azure 目前为以下选定 VM 系列提供第 2 代预览版支持：

* [B 系列](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dc-series)
* [Dsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series)和 [Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB-ACCT-WC 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hb-series)
* [HC 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hc-series)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [NCv2 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series)和[NCv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [ND 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)
* [NVv3 系列](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure 市场中的第 2 代 VM 映像

第 2 代 VM 支持以下市场映像：

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 数据中心
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4

## <a name="on-premises-vs-azure-generation-2-vms"></a>本地与Azure 第 2 代 VM

Azure 目前不支持本地 Hyper-V 对第 2 代 VM 所支持的某些特性。

| 第 2 代的特性                | 本地 Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| 安全启动                         | :heavy_check_mark:  | :x:   |
| 屏蔽 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 基于虚拟化的安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特性和功能

### <a name="generation-1-vs-generation-2-features"></a>第 1 代和第 2 代的特性

| 功能 | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| 启动             | PCAT         | UEFI |
| 磁盘控制器 | IDE          | SCSI |
| VM 大小         | 所有 VM 大小 | 仅限支持高级存储的 VM |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 代与第 2 代的功能

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁盘 > 2 TB                    | :x:                | :heavy_check_mark: |
| 自定义磁盘/映像/交换 OS         | :heavy_check_mark: | :heavy_check_mark: |
| 虚拟机规模集支持 | :heavy_check_mark: | :heavy_check_mark: |
| Azure 站点恢复               | :heavy_check_mark: | :x:                |
| 备份/还原                    | :heavy_check_mark: | :heavy_check_mark: |
| 共享映像库              | :heavy_check_mark: | :x:                |
| Azure 磁盘加密             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>创建第 2 代 VM

### <a name="marketplace-image"></a>市场映像

在 Azure 门户或 Azure CLI 中，可以从支持 UEFI 启动的市场映像创建第 2 代 VM。

#### <a name="azure-portal"></a>Azure 门户

Windows 和 SLES 的第2代映像包含在 Gen1 映像所在的同一个服务器服务中。 从流的角度来看，从 VM 的门户中选择产品/服务和 SKU。 如果 SKU 支持第1代和第2代映像，可以从 VM 创建流的 "*高级*" 选项卡中选择创建第2代 VM。

目前，以下 Sku 支持第1代和第2代映像：

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

选择 Windows Server SKU 作为产品/服务时，在 "**高级**" 选项卡中，可以选择创建**第1代**（BIOS）或**第2代**（UEFI） VM。 如果选择 "第**2**代"，请确保[第2代 Vm 支持](#generation-2-vm-sizes)"**基本**信息" 选项卡中所选的 VM 大小。

![选择第1代或第2代 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

还可以通过直接引用第1代或第2代 SKU 来使用 PowerShell 创建 VM。

例如，使用以下 PowerShell cmdlet 获取`WindowsServer`产品/服务中的 sku 列表。

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

如果要使用 Windows Server 2012 作为 OS 创建 VM，则会选择第1代（BIOS）或第2代（UEFI） VM SKU，如下所示：

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

有关最新的受支持市场映像列表，请参阅[特性和功能](#features-and-capabilities)部分。

### <a name="managed-image-or-managed-disk"></a>托管映像或托管磁盘

可以从托管映像或托管磁盘创建第 2 代 VM，就像创建第 1 代 VM 一样，

### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

还可以使用虚拟机规模集创建第 2 代 VM。 在 Azure CLI 中使用 Azure 规模集创建第 2 代 VM。

## <a name="frequently-asked-questions"></a>常见问题

* **第 2 代 VM 是否已在所有 Azure 区域中推出？**  
    是。 但是，并非所有[第 2 代 VM 大小](#generation-2-vm-sizes)都已在每个区域中推出。 第 2 代 VM 的可用性取决于 VM 大小的可用性。

* **第 1 代与第 2 代 VM 的价格是否有差别？**  
    否。

* **我有来自本地第2代 VM 的 .vhd 文件。能否在 Azure 中使用该 .vhd 文件创建第2代 VM？**
  是的，你可以将第2代 .vhd 文件引入 Azure 并使用它来创建第2代 VM。 使用以下步骤来执行此操作：
    1. 将 .vhd 上传到要在其中创建 VM 的同一区域中的存储帐户。
    1. 从 .vhd 文件创建托管磁盘。 将 Hyper-v 生成属性设置为 V2。 以下 PowerShell 命令在创建托管磁盘时设置 Hyper-v 生成属性。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 磁盘可用后，请通过附加此磁盘来创建 VM。 创建的 VM 将是第2代 VM。
    创建第2代 VM 时，可以选择通用化此 VM 的映像。 通过通用化映像，你可以使用它来创建多个虚拟机。

* **如何增大 OS 磁盘的大小？**  
  大于 2 TB 的 OS 磁盘是第 2 代 VM 的新配置。 默认情况下，第 2 代 VM 的 OS 磁盘小于 2 TB。 可将磁盘大小增大至 4 TB（建议的最大大小）。 使用 Azure CLI 或 Azure 门户增大 OS 磁盘大小。 有关如何以编程方式扩展磁盘的信息，请参阅[调整磁盘大小](expand-disks.md)。

  若要在 Azure 门户中增大 OS 磁盘大小：

  1. 在 Azure 门户中，转到 VM 属性页。
  1. 若要关闭并解除分配 VM，请选择“停止”按钮。
  1. 在“磁盘”部分，选择要增大的 OS 磁盘。
  1. 在“磁盘”部分，选择“配置”并将“大小”更新为所需的值。
  1. 返回到 VM 属性页并**启动** VM。

  你可能会看到一条警告，指出 OS 磁盘大于 2 TB。 该警告不适用于第 2 代 VM。 但是，不建议使用大于 4 TB 的 OS 磁盘大小。

* **第 2 代 VM 是否支持加速网络？**  
    是。 有关详细信息，请参阅[创建具有加速网络的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第 2 代是否支持 VHDX？**  
    否，第 2 代 VM 仅支持 VHD。

* **第2代 VM 是否支持 Azure 超级磁盘存储？**  
    是。

* **是否可将 VM 从第 1 代迁移到第 2 代？**  
    否，创建 VM 后无法更改其代系。 如果需要在 VM 代系之间切换，请创建一个不同代系的新 VM。

## <a name="next-steps"></a>后续步骤

* 了解 [Hyper-V 中的第 2 代虚拟机](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。
