---
title: Azure 支持第 2 代 Vm （预览版） |Microsoft Docs
description: 第 2 代 vm 的 Azure 支持概述
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: lahugh
ms.openlocfilehash: d8991a6e3137953aae18b91f9cc19e81f0440c75
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151215"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>支持生成 2 个 Vm （预览版） 上的 Azure

> [!IMPORTANT]
> Azure 支持第 2 代 Vm 当前处于预览状态。
> 没有附带服务级别协议，提供此预览版本，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

现推出预览版，在 Azure 中的第 2 代虚拟机 (Vm) 的支持。 不能更改后创建的虚拟机的代次，因此选择生成之前，请查看此页上的注意事项。 

第 2 代 Vm 支持第 1 代虚拟机中不支持的主要功能。 这些功能包括增加的内存，Intel 软件防护扩展 (Intel SGX) 和虚拟化永久性内存 (vPMEM)。 第 2 代 Vm 还必须在 Azure 中目前尚不支持某些功能。 有关详细信息，请参阅[特性和功能](#features-and-capabilities)部分。

第 2 代虚拟机使用新的基于 UEFI 的引导体系结构，而不是基于 BIOS 的体系结构由生成 1 虚拟机。 与第 1 代 Vm 相比，第 2 代 Vm 可能会改进了启动和安装时间。 第 2 代 Vm 的概述和一些第 1 代和第 2 代之间的差异，请参阅[应在 HYPER-V 中创建第 1 或 2 代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第 2 代 VM 大小

在 Azure 中的所有 VM 大小都支持的第 1 代 Vm。 Azure 现在提供预览版对以下所选的 VM 系列的第 2 代支持：

* [Dsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#gs-series)
* [Ls-系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第 2 代 VM 映像

第 2 代 Vm 支持以下 Marketplace 映像：

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>在本地与Azure 的第 2 代 Vm

Azure 当前不支持的一些功能，在本地 HYPER-V 支持第 2 代 vm。

| 第 2 代功能                | 在本地 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全启动                         | :heavy_check_mark:  | :x:   |
| 受防护的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 基于虚拟化的安全 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特性和功能

### <a name="generation-1-vs-generation-2-features"></a>第 1 代和第 2 代功能

| Feature | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| 引导             | PCAT                      | UEFI                               |
| 磁盘控制器 | IDE                       | SCSI                               |
| VM 大小         | 所有 VM 大小 | 仅支持高级存储的 Vm |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 代和第 2 代功能

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁盘 > 2 TB                    | :x:                        | :heavy_check_mark: |
| 自定义磁盘/映像/交换 OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 虚拟机规模集的支持 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/backup                        | :heavy_check_mark:         | :x:                |
| 共享的映像库              | :heavy_check_mark:         | :x:                |
| Azure 磁盘加密             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>创建第 2 代 VM

### <a name="marketplace-image"></a>Marketplace 映像

在 Azure 门户或 Azure CLI 中，您可以创建生成 2 个 Vm 从 Marketplace 映像支持 UEFI 启动。

`windowsserver-gen2preview`产品/服务包含仅 Windows 第 2 代映像。 此打包可避免第 1 代和第 2 代映像之间产生混淆。 若要创建第 2 代 VM，选择**映像**从该产品/服务并遵循标准过程来创建 VM。

目前，市场上提供以下 Windows 生成 2 个映像：

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

请参阅[特性和功能](#features-and-capabilities)部分中的当前列表的受支持的 Marketplace 映像。

### <a name="managed-image-or-managed-disk"></a>托管的映像或托管的磁盘

您可以创建第 2 代 VM 从托管的映像或托管的磁盘相同的方式将创建第 1 代 VM。

### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

您还可以创建生成 2 个 Vm 使用虚拟机规模集。 Azure CLI 中使用 Azure 规模集创建 2 个 Vm 的代。

## <a name="frequently-asked-questions"></a>常见问题

* **生成所有 Azure 区域中可用的 2 个 Vm？**  
    是的。 而不是全部[第 2 代 VM 大小](#generation-2-vm-sizes)每个区域中都可用。 2 个 VM 的 VM 大小可用性取决于的生成的可用性。

* **是否有第 1 代和生成之间的价差的 2 个 Vm？**  
   不。

* **如何增加 OS 磁盘大小？**  
  OS 磁盘大于 2 TB 不熟悉第 2 代 Vm。 默认情况下，OS 磁盘是小于第 2 代 vm 的 2 TB。 可以增加磁盘大小最大为 4 TB 的建议最大值。 使用 Azure CLI 或 Azure 门户中，增加的 OS 磁盘大小。 有关如何以编程方式扩展磁盘的信息，请参阅[调整磁盘大小](expand-os-disk.md)。

  若要增加从 Azure 门户将 OS 磁盘大小：

  1. 在 Azure 门户中，转到 VM 属性页。
  1. 若要关闭并解除分配 VM，请选择**停止**按钮。
  1. 在中**磁盘**部分中，选择你想要增加的 OS 磁盘。
  1. 在中**磁盘**部分中，选择**配置**，并更新**大小**到所需的值。
  1. 返回到的 VM 属性页并**启动**VM。
  
  可能会看到一条警告对于 OS 磁盘大于 2 TB。 此警告不应用于第 2 代 Vm。 但是，OS 磁盘大小大于 4 TB 为*不建议这样做。*

* **生成 2 个 Vm 支持加速网络？**  
    是的。 有关详细信息，请参阅[创建具有加速网络的 VM](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **在第 2 代上支持 VHDX？**  
    否。 第 2 代 Vm 支持仅 VHD。

* **第 2 代 Vm 支持 Azure 超高磁盘存储吗？**  
    是的。

* **是否可以迁移虚拟机从第 1 代到第 2 代？**  
    不可以，在创建后无法更改虚拟机的代。 如果需要虚拟机代次之间进行切换，创建新的 VM 的不同的生成。

## <a name="next-steps"></a>后续步骤

* 了解如何[第 2 代虚拟机的 HYPER-V 中](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 了解如何[准备好的 VHD](prepare-for-upload-vhd-image.md)要从本地系统上传到 Azure。
