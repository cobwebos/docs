---
title: 适用于第2代 Vm 的 Azure 支持（预览版） |Microsoft Docs
description: 第2代 Vm 的 Azure 支持概述
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/16/2019
ms.author: lahugh
ms.openlocfilehash: 1b906a9e3ce803b7f319a920f29b5a60f62c47b1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550168"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>支持 Azure 上的第2代 Vm （预览版）

> [!IMPORTANT]
> 适用于第2代 Vm 的 Azure 支持目前处于预览阶段。
> 此预览版本在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 中的预览版现在提供对第2代虚拟机（Vm）的支持。 创建虚拟机后，无法对其进行更改，因此请在选择代之前查看此页上的注意事项。 

第2代 Vm 支持第1代 Vm 不支持的关键功能。 这些功能包括增加的内存、Intel 软件防护扩展（Intel SGX）和虚拟化永久性内存（vPMEM）。 第2代 Vm 还具有 Azure 中不支持的某些功能。 有关详细信息，请参阅 "[功能和功能](#features-and-capabilities)" 一节。

第2代 Vm 使用新的基于 UEFI 的启动体系结构，而不是由第1代 Vm 使用的基于 BIOS 的体系结构。 与第1代 Vm 相比，第2代 Vm 可能已改进了启动和安装时间。 有关第2代 Vm 和第1代和第2代之间的一些差异的概述，请参阅是否[应在 hyper-v 中创建第1代或第2代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第2代 VM 大小

Azure 中的所有 VM 大小都支持第1代 Vm。 Azure 现在为以下选定 VM 系列提供预览版2支持：

* [B 系列](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DC 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* [Dsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB-ACCT-WC 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [HC 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* [NCv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series)和[NCv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [ND 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [NVv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

> [!NOTE]
> 由于 Mv2 系列仅适用于第2代 VM 映像，因此 Mv2 系列 vm 的第2代 VM 映像已正式发布。 Mv2 系列 Vm 不支持第1代 VM 映像。 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第2代 VM 映像

第2代 Vm 支持以下 Marketplace 映像：

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4

## <a name="on-premises-vs-azure-generation-2-vms"></a>本地与 Azure 第2代 Vm

Azure 目前不支持第2代 Vm 的本地 Hyper-v 支持的某些功能。

| 第2代功能                | 本地 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全启动                         | :heavy_check_mark:  | :x:   |
| 受防护的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 基于虚拟化的安全性（VBS） | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特性和功能

### <a name="generation-1-vs-generation-2-features"></a>第1代和第2代功能

| Feature | 第 1 代 | 第2代 |
|---------|--------------|--------------|
| 引导             | PCAT                      | UEFI                               |
| 磁盘控制器 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 仅支持高级存储的 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>第1代和第2代功能

| 功能 | 第 1 代 | 第2代 |
|------------|--------------|--------------|
| OS 磁盘 > 2 TB                    | :x:                | :heavy_check_mark: |
| 自定义磁盘/映像/交换操作系统         | :heavy_check_mark: | :heavy_check_mark: |
| 虚拟机规模集支持 | :heavy_check_mark: | :heavy_check_mark: |
| Azure 站点恢复               | :heavy_check_mark: | :x:                |
| 备份/还原                    | :heavy_check_mark: | :heavy_check_mark: |
| 共享映像库              | :heavy_check_mark: | :heavy_check_mark: |
| Azure 磁盘加密             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>创建第2代 VM

### <a name="marketplace-image"></a>Marketplace 映像

在 Azure 门户或 Azure CLI 中，可以从支持 UEFI 启动的 Marketplace 映像创建第2代 Vm。

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

例如，使用以下 PowerShell cmdlet 获取 `WindowsServer` 产品中的 Sku 列表。

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

如果要使用 Windows Server 2012 作为 OS 创建 VM，则会选择第1代（BIOS）或第2代（UEFI） VM SKU，如下所示：

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

有关支持的 Marketplace 映像的当前列表，请参阅 "[功能和功能](#features-and-capabilities)" 一节。

### <a name="managed-image-or-managed-disk"></a>托管映像或托管磁盘

你可以使用与创建第1代 VM 相同的方式从托管映像或托管磁盘创建第2代 VM。

### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

你还可以使用虚拟机规模集创建第2代 Vm。 在 Azure CLI 中，使用 Azure 规模集创建第2代 Vm。

## <a name="frequently-asked-questions"></a>常见问题

* **第2代 Vm 是否适用于所有 Azure 区域？**  
    可以。 但并非所有[第2代 VM 大小](#generation-2-vm-sizes)都可在每个区域中使用。 第2代 VM 的可用性取决于 VM 大小的可用性。

* **第1代和第2代 Vm 之间是否存在价格差异？**  
   不。

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

* **如何实现增加操作系统磁盘大小？**  
  大于 2 TB 的 OS 磁盘是第2代 Vm 的新版本。 默认情况下，第2代 Vm 的 OS 磁盘小于 2 TB。 最大可将磁盘大小增加到最大为 4 TB。 使用 Azure CLI 或 Azure 门户增加 OS 磁盘大小。 有关如何以编程方式扩展磁盘的信息，请参阅[调整磁盘大小](expand-os-disk.md)。

  增加 Azure 门户的 OS 磁盘大小：

  1. 在 Azure 门户中，请参阅 VM 属性页。
  1. 若要关闭并解除分配 VM，请选择 "**停止**" 按钮。
  1. 在 "**磁盘**" 部分中，选择要增加的 OS 磁盘。
  1. 在 "**磁盘**" 部分中，选择 "**配置**"，并将**大小**更新为所需的值。
  1. 返回到 VM 属性页面并**启动**vm。
  
  你可能会看到大于 2 TB 的 OS 磁盘的警告。 警告不适用于第2代 Vm。 但是，*不建议使用*大于 4 TB 的 OS 磁盘大小。

* **第2代 Vm 是否支持加速网络？**  
    可以。 有关详细信息，请参阅[创建具有加速网络的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第2代支持 VHDX？**  
    不是，第2代 Vm 仅支持 VHD。

* **第2代 Vm 是否支持 Azure Ultra 磁盘存储？**  
    可以。

* **是否可以将 VM 从第1代迁移到第2代？**  
    不可以，在创建 VM 后，无法更改其代。 如果需要在虚拟机之间进行切换，请创建一个不同代的新 VM。

## <a name="next-steps"></a>后续步骤

* 了解[hyper-v 中的第2代虚拟机](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 了解如何[准备 VHD](prepare-for-upload-vhd-image.md)以从本地系统上传到 Azure。
