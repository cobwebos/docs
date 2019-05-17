---
title: 在 Azure 上的第 2 代 Vm （预览版） |Microsoft Docs
description: Azure 的第 2 代 Vm 的概述
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
ms.date: 05/13/2019
ms.author: lahugh
ms.openlocfilehash: 9553612d5b9d0b646c5c25e3f719038ac27d4826
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596163"
---
# <a name="generation-2-vms-preview-on-azure"></a>在 Azure 上的第 2 代 Vm （预览版）

> [!IMPORTANT]
> 第 2 代 Vm 当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

第 2 代虚拟机 (Vm) 的支持现已在 Azure 上的公共预览版中可用。 无法更改虚拟机的代次后创建它。 因此，我们建议您查看的注意事项[此处](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)以及此页上选择某代之前的信息。

第 2 代 Vm 支持关键功能，例如： 增加的内存、 Intel® 软件防护扩展 (SGX)，以及虚拟永久性内存 (vPMEM)，这在第 1 代 Vm 上不受支持。 第 2 代虚拟机具有在 Azure 上目前尚不支持某些功能。 有关详细信息，请参阅[特性和功能](#features-and-capabilities)部分。 第 2 代虚拟机使用新的基于 UEFI 的引导体系结构与基于 BIOS 的体系结构使用的第 1 代 Vm。 与第 1 代 Vm 相比，第 2 代 Vm 可能会改进启动和安装时间。 第 2 代 Vm 的概述和一些第 1 代和第 2 代的主要差异，请参阅[应在 HYPER-V 中创建第 1 或 2 代虚拟机？](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

## <a name="generation-2-vm-sizes"></a>第 2 代 VM 大小

在 Azure 中的所有 VM 大小都支持的第 1 代 Vm。 Azure 现在提供公共预览版中的以下所选 VM 系列的第 2 代支持：

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series)和[Dsv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#gs-series)
* [Ls-系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)和[Lsv2 系列](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace 中的第 2 代 VM 映像

第 2 代 Vm 支持以下 Azure Marketplace 映像：

* Windows server 2019 数据中心
* Windows server 2016 数据中心
* Windows server 2012 R2 Datacenter
* Windows server 2012 数据中心

## <a name="on-premises-vs-azure-generation-2-vms"></a>在本地与 Azure 的第 2 代 Vm

Azure 当前不支持的一些功能，在本地 HYPER-V 支持的第 2 代 Vm。

| 第 2 代功能                | 在本地 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 安全启动                         | :heavy_check_mark:  | :x:   |
| 受防护的 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 基于虚拟化的安全 (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 格式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特性和功能

### <a name="generation-1-vs-generation-2-features"></a>第 1 代与第 2 代功能

| Feature | 第 1 代 | 第 2 代 |
|---------|--------------|--------------|
| 启动             | PCAT                      | UEFI                               |
| 磁盘控制器 | IDE                       | SCSI                               |
| VM 大小         | 适用于所有 VM 大小 | 高级存储仅支持虚拟机 |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 代与第 2 代功能

| 功能 | 第 1 代 | 第 2 代 |
|------------|--------------|--------------|
| OS 磁盘 > 2 TB                    | :x:                        | :heavy_check_mark: |
| 自定义磁盘/映像/交换 OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 虚拟机规模集的支持 | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/备份                        | :heavy_check_mark:         | :x:                |
| 共享的映像库              | :heavy_check_mark:         | :x:                |
| Azure 磁盘加密             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>创建第 2 代 VM

### <a name="marketplace-image"></a>Marketplace 映像

可以从 marketplace 映像 （它支持 UEFI 引导） 通过 Azure 门户或 Azure CLI 创建第 2 代 Vm。

`windowsserver-gen2preview`产品/服务包含仅 Windows 第 2 代映像。 这将避免与第 1 代与第 2 代映像相关的混淆。 若要创建生成 2 个 Vm，请选择**映像**此产品/服务，然后根据标准 VM 创建过程。

目前，以下 Windows 生成 2 个映像是 Azure Marketplace 中发布：

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

因为我们会持续添加其他支持第 2 代的映像，请参阅受支持的 marketplace 映像的列表的功能部分。

### <a name="managed-image-or-managed-disk"></a>托管的映像或托管的磁盘

生成 2 个 Vm 可以从托管的映像或托管的磁盘创建同样会创建第 1 代 VM。

### <a name="virtual-machine-scale-sets"></a>虚拟机规模集

此外可以使用虚拟机规模集创建 2 个 Vm 的代。 您可以创建生成 2 个 Vm 使用 Azure CLI 通过 Azure 虚拟机规模集。

## <a name="frequently-asked-questions"></a>常见问题

* **第 2 代 Vm 支持加速网络吗？**  
    是的第 2 代 Vm 支持[加速网络](../../virtual-network/create-vm-accelerated-networking-cli.md)。

* **在第 2 代上支持.vhdx？**  
    不可以，第 2 代 Vm 支持仅.vhd。

* **第 2 代 Vm 支持超高固态驱动器 (SSD) 吗？**  
    是的第 2 代 Vm 支持超高 SSD。

* **是否可以迁移从第 1 代到第 2 代 Vm？**  
    不可以，你创建它后无法更改 VM 的代。 如果需要虚拟机代次之间进行切换，您需要创建新的 VM 的不同的生成。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[第 2 代虚拟机的 HYPER-V 中](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。

* 了解如何[准备好的 VHD](prepare-for-upload-vhd-image.md)从本地上载到 Azure。
