---
title: 适用于第2代 Vm 的 Azure 支持 (预览版) |Microsoft Docs
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
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: 7cd938369cdc4f4ca711a442d89cd6ef3d6d08e8
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477084"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>支持 Azure 上的第2代 Vm (预览版)

> [!IMPORTANT]
> 适用于第2代 Vm 的 Azure 支持目前处于预览阶段。
> 此预览版本在提供时没有服务级别协议, 不建议用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 中的预览版现在提供对第2代虚拟机 (Vm) 的支持。 创建虚拟机后, 无法对其进行更改, 因此请在选择代之前查看此页上的注意事项。 

第2代 Vm 支持第1代 Vm 不支持的关键功能。 这些功能包括增加的内存、Intel 软件防护扩展 (Intel SGX) 和虚拟化永久性内存 (vPMEM)。 第2代 Vm 还具有 Azure 中不支持的某些功能。 有关详细信息, 请参阅 "[功能和功能](#features-and-capabilities)" 一节。

第2代 Vm 使用新的基于 UEFI 的启动体系结构, 而不是由第1代 Vm 使用的基于 BIOS 的体系结构。 与第1代 Vm 相比, 第2代 Vm 可能已改进了启动和安装时间。 有关第2代 Vm 和第1代和第2代之间的一些差异的概述, 请参阅是否[应在 hyper-v 中创建第1代或第2代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第2代 VM 大小

Azure 中的所有 VM 大小都支持第1代 Vm。 Azure 现在为以下选定 VM 系列提供预览版2支持:

* [B 系列](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Dsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Ls 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* [NCv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series)和[NCv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [ND 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第2代 VM 映像

第2代 Vm 支持以下 Marketplace 映像:

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 数据中心
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>本地与Azure 第2代 Vm

Azure 目前不支持第2代 Vm 的本地 Hyper-v 支持的某些功能。

| 第2代功能                | 本地 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全启动                         | :heavy_check_mark:  | :x:   |
| 受防护的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 基于虚拟化的安全性 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特性和功能

### <a name="generation-1-vs-generation-2-features"></a>第1代和第2代功能

| 功能 | 第 1 代 | 第2代 |
|---------|--------------|--------------|
| 引导             | PCAT                      | UEFI                               |
| 磁盘控制器 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 仅支持高级存储的 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>第1代和第2代功能

| 能力 | 第 1 代 | 第2代 |
|------------|--------------|--------------|
| OS 磁盘 > 2 TB                    | :x:                        | :heavy_check_mark: |
| 自定义磁盘/映像/交换操作系统         | :heavy_check_mark:         | :heavy_check_mark: |
| 虚拟机规模集支持 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| 共享映像库              | :heavy_check_mark:         | :x:                |
| Azure 磁盘加密             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>创建第2代 VM

### <a name="marketplace-image"></a>Marketplace 映像

在 Azure 门户或 Azure CLI 中, 可以从支持 UEFI 启动的 Marketplace 映像创建第2代 Vm。

此`windowsserver-gen2preview`产品/服务仅包含 Windows 第2代映像。 这种打包避免了第1代和第2代映像之间的混淆。 若要创建第2代 VM, 请选择 "此产品/服务中的**映像**", 并遵循标准过程来创建 vm。

目前, Marketplace 提供以下 Windows 第2代映像:

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

有关支持的 Marketplace 映像的当前列表, 请参阅 "[功能和功能](#features-and-capabilities)" 一节。

### <a name="managed-image-or-managed-disk"></a>托管映像或托管磁盘

你可以使用与创建第1代 VM 相同的方式从托管映像或托管磁盘创建第2代 VM。

### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

你还可以使用虚拟机规模集创建第2代 Vm。 在 Azure CLI 中, 使用 Azure 规模集创建第2代 Vm。

## <a name="frequently-asked-questions"></a>常见问题

* **第2代 Vm 是否适用于所有 Azure 区域？**  
    是的。 但并非所有[第2代 VM 大小](#generation-2-vm-sizes)都可在每个区域中使用。 第2代 VM 的可用性取决于 VM 大小的可用性。

* **第1代和第2代 Vm 之间是否存在价格差异？**  
   否。

* **如何实现增加操作系统磁盘大小？**  
  大于 2 TB 的 OS 磁盘是第2代 Vm 的新版本。 默认情况下, 第2代 Vm 的 OS 磁盘小于 2 TB。 最大可将磁盘大小增加到最大为 4 TB。 使用 Azure CLI 或 Azure 门户增加 OS 磁盘大小。 有关如何以编程方式扩展磁盘的信息, 请参阅[调整磁盘大小](expand-os-disk.md)。

  增加 Azure 门户的 OS 磁盘大小:

  1. 在 Azure 门户中, 请参阅 VM 属性页。
  1. 若要关闭并解除分配 VM, 请选择 "**停止**" 按钮。
  1. 在 "**磁盘**" 部分中, 选择要增加的 OS 磁盘。
  1. 在 "**磁盘**" 部分中, 选择 "**配置**", 并将**大小**更新为所需的值。
  1. 返回到 VM 属性页面并**启动**vm。
  
  你可能会看到大于 2 TB 的 OS 磁盘的警告。 警告不适用于第2代 Vm。 但是,*不建议使用*大于 4 TB 的 OS 磁盘大小。

* **第2代 Vm 是否支持加速网络？**  
    是的。 有关详细信息, 请参阅[创建具有加速网络的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **第2代支持 VHDX？**  
    不是, 第2代 Vm 仅支持 VHD。

* **第2代 Vm 是否支持 Azure Ultra 磁盘存储？**  
    是的。

* **是否可以将 VM 从第1代迁移到第2代？**  
    不可以, 在创建 VM 后, 无法更改其代。 如果需要在虚拟机之间进行切换, 请创建一个不同代的新 VM。

## <a name="next-steps"></a>后续步骤

* 了解[hyper-v 中的第2代虚拟机](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 了解如何[准备 VHD](prepare-for-upload-vhd-image.md)以从本地系统上传到 Azure。
