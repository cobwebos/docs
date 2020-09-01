---
title: 概述 - 创建适用于 Azure 的 Linux 映像
description: 如何将 Linux VM 映像引入到 Azure 或新建映像以在 Azure 中使用。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 220aa4e0d545eedcd1eb0e6f5a6555b17a361da2
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815294"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>在 Azure 中引入和创建 Linux 映像

本概述介绍有关映像的基础概念以及如何在 Azure 中成功构建和使用 Linux。 在将自定义映像引入 Azure 之前，需要首先了解可用的类型和选项。

本文介绍映像决策点和要求以及关键概念，让你能够按照本文说明创建符合你所需规格的自定义映像。

## <a name="difference-between-managed-disks-and-images"></a>托管磁盘和映像之间的差异


Azure 支持将 VHD 引入到 Azure 平台，将其用作[托管磁盘](../faq-for-disks.md#managed-disks)或用作映像的源。 

Azure 托管磁盘是单个的 VHD。 可以使用现有 VHD 和根据其创建托管磁盘，或者从头开始创建一个空托管磁盘。 可以通过将磁盘附加到 VM 来借助托管磁盘创建 VM，但一个 VM 只能使用一个 VHD。 OS 属性无法修改，Azure 只会尝试开启 VM 和启动磁盘。 

Azure 映像可以由多个 OS 磁盘和数据磁盘组成。 使用托管映像创建 VM 时，该平台生成此映像的副本并使用它来创建 VM，因此，同一托管映像可以重复用于多个 VM。 Azure 还提供了高级映像管理功能，例如全局复制和通过[共享映像库](shared-image-galleries.md)进行版本控制。 



## <a name="generalized-and-specialized"></a>通用和专用映像

Azure 提供了两种主要映像类型：通用映像和专用映像。 通用和专用最初是 Windows 术语，之后被迁移应用到 Azure 中。 这两个类型定义了该平台在打开 VM 时如何处理 VM。 这两种类型各有一些优缺点和先决条件。 开始之前，首先需要明白需要哪种类型的映像。 下表总结了使用场景及相应需选择的类型：

| 场景      | 映像类型  | 存储选项 |
| ------------- |:-------------:| :-------------:| 
| 创建一个可配置用于多个 VM 的映像，我可以设置主机名和管理员用户以及在首次启动期间执行其他任务。 | 通用 | 共享映像库或独立的托管映像 |
| 使用 VM 快照或备份创建映像 | 专用 |共享映像库或托管磁盘 |
| 快速创建不需任何配置的映像来创建多个 VM |专用 |共享的映像库 |


### <a name="generalized-images"></a>通用映像

通用映像在首次启动时需要完成设置。 例如，在首次启动时，设置主机名、管理员用户和其他特定于 VM 的配置。 需要多次重用映像以及在创建期间传入参数时，此类映像非常有用。 如果通用映像包含 Azure 代理，则代理将处理参数，并向平台返回指示初始配置已完成的信号。 此过程称为“预配”。 

预配要求映像中包含配置程序。 以下两个配置程序可供使用：
- [Azure Linux 代理](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

有关创建映像的先决条件，请参阅[此处](./create-upload-generic.md)。


### <a name="specialized-images"></a>专用映像
此类映像经过完全配置且不需用 VM 参数和特殊参数。该平台仅打开 VM，你需要通过设置主机名等方式确保 VM 内的唯一性来避免同一 VNET 中存在 DNS 冲突。 

虽然此类映像无需预配代理，但是，你可能需要一些扩展处理功能。 为此，可以安装 Linux 代理，但请禁用预配选项。 即使无需预配代理，此类映像也必须满足 Azure 映像的[先决条件](./create-upload-generic.md)。


## <a name="image-storage-options"></a>映像存储选项
使用 Linux 映像时有两个选项可供选择：

- 使用托管映像在开发和测试环境中创建简单的 VM。
- 使用[共享映像库](shared-image-galleries.md)大规模创建和共享映像。


### <a name="managed-images"></a>托管映像

虽然托管映像可用于创建多个 VM，但它们具有许多限制。 托管映像仅能从通用源（VM 或 VHD）中进行创建。 托管映像仅可用于在相同区域中创建 VM，且不能跨订阅和租户实现共享。

托管映像可用于开发和测试环境（在这种环境下，单个区域和订阅中需使用几个简单的通用映像）。 

### <a name="azure-shared-image-gallery-sig"></a>Azure 共享映像库 (SIG)

[共享映像库](shared-image-galleries.md)推荐用于大规模创建、管理和共享映像。 共享映像库提供许多功能和特性，可帮助你围绕映像生成结构和组织：  

- 支持通用映像和专用映像。
- 支持第 1 代映像和第 2 代映像。
- 支持映像全局复制。
- 对映像进行版本控制和分组，以便于管理。
- 在支持可用性区域的区域中，支持具有区域冗余存储 (ZRS) 的高可用性映像。 ZRS 提高了针对区域性故障的恢复能力。
- 使用 RBAC 在订阅之间，甚至在 Active Directory (AD) 租户之间共享。
- 使用每个区域中的映像副本缩放部署。

概括而言，创建的 SIG 包含：
- 映像定义（保存映像组的容器）
- 映像版本（实际的映像）



## <a name="hyper-v-generation"></a>Hyper-V 代系

Azure 支持 Hyper-V 第 1 代 (Gen1) 和第 2 代 (Gen2)。Gen2 是最新一代的 Hyper-V，与 Gen1 相比提供了更多功能， 例如更大的内存、Intel Software Guard Extensions (Intel SGX) 和虚拟化持久性内存 (vPMEM)。 在本地运行的第 2 代 VM 具有 Azure 中尚不支持的一些特性。 有关详细信息，请参阅“特性和功能”部分。 有关详细信息，请参阅此[文章](../windows/generation-2.md)。 如果需要其他功能，请创建 Gen2 映像。

如果仍需要创建自己的映像，请确保映像符合[映像先决条件](./create-upload-generic.md)，并将其上传到 Azure。 以下是分发特定要求：


- [基于 CentOS 的分发版](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 和 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>后续步骤

了解如何创建[共享映像库](tutorial-custom-images.md)。
