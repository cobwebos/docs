---
title: "将 VMware VM 和物理服务器复制到 Azure（旧版经典门户）| Microsoft 文档"
description: "介绍如何在经典门户中，使用 Azure Site Recovery 将本地 VM 和 Windows/Linux 物理服务器复制到 Azure 以进行传统部署。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: f980fb52-8ec2-4dd9-85e2-8e52e449f1ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 325be23cffc9c728a8af6f92a0f3dce6d31da4ae
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>在经典门户（旧版）中使用 Azure Site Recovery 将 VMware 虚拟机和物理服务器复制到 Azure
> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-vmware-to-azure.md)
> * [经典门户](site-recovery-vmware-to-azure-classic.md)
> * [经典门户（旧版）](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

欢迎使用 Azure Site Recovery！ 本文介绍如何在经典门户中，使用 Azure Site Recovery 将本地 VMware 虚拟机或 Windows/Linux 物理服务器复制到 Azure 以进行传统部署。

## <a name="overview"></a>概述
组织需要制定 BCDR 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。 BCDR 策略应保持业务数据的安全性和可恢复性，并确保在发生灾难时工作负荷持续可用。

站点恢复是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。 当主要位置发生故障时，可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，可以故障转移回到主要位置。 在 [什么是 Azure Site Recovery？](site-recovery-overview.md)

> [!WARNING]
> 本文介绍的是**旧版说明**。 进行新部署时请勿参考本文， 相反，[请按这些说明操作](site-recovery-vmware-to-azure.md)，在 Azure 门户中部署 Site Recovery，或[参考这些说明](site-recovery-vmware-to-azure-classic.md)，在经典门户中配置增强型部署。 如果已使用本文所述方法进行了部署，我们建议在经典部门中迁移到增强型部署。
>
>

## <a name="migrate-to-the-enhanced-deployment"></a>迁移到增强型部署
仅已使用本文中的说明部署了站点恢复时，本部分才有关。

若要迁移现有的部署，需执行以下操作：

1. 在本地站点中部署新的站点恢复组件。
2. 配置凭据以自动发现新配置服务器上的 VMware VM。
3. 通过新的配置服务器发现 VMware 服务器。
4. 通过新的配置服务器创建新的保护组。

开始之前：

* 建议设置迁移的维护时段。
* 只有在现有保护组已在旧部署过程中创建的情况下，“**迁移计算机**”选项才可用。
* 完成迁移步骤以后，可能需要 15 分钟或更长的时间才能完成凭据的刷新以及虚拟机的发现和刷新（刷新后才能将其添加到保护组）。 可以手动刷新，而不必等待。

按如下方式进行迁移：

1. 了解[经典门户中的增强型部署](site-recovery-vmware-to-azure-classic.md)。 查看增强的[体系结构](site-recovery-vmware-to-azure-classic.md)和[先决条件](site-recovery-vmware-to-azure-classic.md)。
2. 从当前复制的计算机中卸载移动服务。 将这些计算机添加到新保护组时，会在它们上面安装新版本的服务。
3. 下载[保管库注册密钥](site-recovery-vmware-to-azure-classic.md)，并[运行统一安装向导](site-recovery-vmware-to-azure-classic.md)，以便安装配置服务器、进程服务器和主目标服务器组件。 详细了解[容量规划](site-recovery-vmware-to-azure-classic.md)。
4. [设置凭据](site-recovery-vmware-to-azure-classic.md)供 Site Recovery 用于访问 VMware 服务器，以自动发现 VMware VM。 了解[所需权限](site-recovery-vmware-to-azure-classic.md)。
5. 添加 [vCenter 服务器或 vSphere 主机](site-recovery-vmware-to-azure-classic.md)。 可能需要 15 分钟以上，服务器才会出现在站点恢复门户中。
6. 创建[新的保护组](site-recovery-vmware-to-azure-classic.md)。 门户进行刷新以发现和显示虚拟机的时间可能长达 15 分钟。 如果不想等待，则可突出显示管理服务器名称（请勿单击），然后单击“**刷新**”。
7. 在新的保护组下，单击“**迁移计算机**”。

    ![添加帐户](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)
8. 在“**选择计算机**”中，选择要从中迁移计算机的保护组，以及要迁移的计算机。

    ![添加帐户](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)
9. 在“**配置目标设置**”中，指定是否希望对所有计算机使用相同的设置，并选择进程服务器和 Azure 存储帐户。 如果未设置单独的进程服务器，则这是配置服务器的 IP 地址。

    ![添加帐户](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] 用于部署 Site Recovery 的存储帐户不支持跨同一订阅中的资源组或跨订阅[迁移存储帐户](../resource-group-move-resources.md)。

1. 在“**指定帐户**”中，选择已为进程服务器创建的用于访问计算机的帐户，从而推送新版移动服务。

   ![添加帐户](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)
2. Site Recovery 会将已复制数据迁移到所提供的 Azure 存储帐户。 （可选）可以重复使用在旧部署中使用的存储帐户。
3. 作业完成后，虚拟机会自动同步。 同步完成后，可以从旧保护组中删除虚拟机。
4. 所有计算机都迁移完以后，可以删除旧保护组。
5. 请记住，在同步完成后，需要指定计算机的故障转移属性以及 Azure 网络设置。
6. 如果有现成的恢复计划，可通过“**迁移恢复计划**”选项将其迁移到增强型部署。 只有在所有受保护的计算机迁移完后，才能执行此操作。

   ![添加帐户](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

> [!NOTE]
> 完成迁移后，请继续阅读[与增强型部署相关的文章](site-recovery-vmware-to-azure-classic.md)。 这篇旧版文章的其余部分已不再相关，无需遵循其中介绍的其他任何步骤**。
>
>

## <a name="what-do-i-need"></a>我需要做好哪些准备？
此图显示了部署组件。

![新保管库](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

以下是需要做好的准备：

| **组件** | **部署** | **详细信息** |
| --- | --- | --- |
| **配置服务器** |在与 Site Recovery 相同的订阅中部署一个 Azure 标准 A3 虚拟机。 |配置服务器可在 Azure 中协调受保护的计算机、进程服务器和主目标服务器之间的通信。 在执行故障转移时，它可在 Azure 中设置复制并协调恢复。 |
| **主目标服务器** |Azure 虚拟机 - 可以是基于 Windows Server 2012 R2 库映像（用于保护 Windows 计算机）的 Windows 服务器，也可以是基于 OpenLogic CentOS 6.6 库映像（用于保护 Linux 计算机）的 Linux 服务器。<br/><br/> 有三个大小选项 - 标准 A4、标准 D14 和标准 DS4。<br/><br/> 该服务器连接到配置服务器所在的 Azure 网络。<br/><br/> 在站点恢复门户中设置 |使用在 Azure 存储帐户的 Blob 存储上创建的附加 VHD 从受保护计算机接收并保留复制的数据。<br/><br/> 标准 DS4 专门用于针对需要通过高级存储帐户持续提供高性能和低延迟的工作负荷配置保护。 |
| **进程服务器** |运行 Windows Server 2012 R2 的本地虚拟或物理服务器<br/><br/> 我们建议将处理服务器放置在与要保护的计算机相同的网络和 LAN 网段中，但是，它可以运行在不同网络上，只要受保护的计算机对其拥有 L3 网络可见性即可。<br/><br/> 在站点恢复门户中设置它，并将其注册到配置服务器。 |受保护的虚拟机将复制数据发送到本地进程服务器。 它具有基于磁盘的缓存，用于缓存所收到的复制数据。 对此类数据执行大量操作。<br/><br/> 优化数据，具体方法为先缓存、压缩和加密数据，然后将数据发送到主目标服务器。<br/><br/> 处理移动服务的推送安装。<br/><br/> 它执行 VMware 虚拟机的自动发现。 |
| **本地计算机** |本地 VMware 虚拟机，或运行 Windows 或 Linux 的物理服务器。 |配置要应用到一个或多个计算机的复制设置。 可以将故障转移单个计算机，更常见的做法是在恢复方案中聚合多个计算机。 |
| **移动服务** |在要保护的每个虚拟机或物理服务器上安装<br/><br/> 可以手动安装，也可以在启用计算机复制时由进程服务器自动推送并安装。 |移动服务在初始复制（重新同步）期间将数据发送到进程服务器。 当计算机处于受保护的状态后（重新同步完成后），移动服务将捕获内存中的磁盘写入，并将其发送到进程服务器。 Windows 服务器的应用程序一致性是使用 VSS 实现的。 |
| **Azure Site Recovery 保管库** |使用 Azure 订阅创建 Site Recovery 保管库，并在保管库中注册服务器。 |保管库可协调和安排本地站点与 Azure 之间的数据复制、故障转移和恢复。 |
| **复制机制** |**通过 Internet** 通过 Internet 使用安全 SSL/TLS 通道通信，并将数据从受保护的本地服务器复制到 Azure。 这是默认选项。<br/><br/> **VPN/ExpressRoute** - 通过 VPN 连接通信，并在本地服务器与 Azure 之间复制数据。 需要在本地站点与 Azure 网络之间设置站点到站点 VPN 或 ExpressRoute 连接。<br/><br/> 需要选择在站点恢复部署期间要如何进行复制。 配置机制后，无法在不影响现有计算机的复制的前提下更改该机制。 |无论是哪个选项，都不需要在受保护的计算机上打开任何入站网络端口。 所有网络通信都从本地站点启动。 |

## <a name="capacity-planning"></a>容量规划
需要考虑的主要方面：

* **源环境** - VMware 基础结构、源计算机设置和要求。
* **组件服务器** - 进程服务器、配置服务器和主目标服务器

### <a name="considerations-for-the-source-environment"></a>有关源环境的注意事项
* **磁盘大小上限** - 可以在虚拟机中附加的磁盘的当前大小上限为 1 TB。 因此，可以复制的源磁盘的最大大小也限制为 1 TB。
* **每个源的大小上限** - 单个源计算机的大小上限是 31 TB（包含 31 个磁盘，以及为主目标服务器预配的 D14 实例）。
* **每个主目标服务器的源数目** - 可以使用单个主目标服务器保护多个源计算机。 但是，无法通过多个主目标服务器保护单个计算机，因为在磁盘复制时，镜像磁盘大小的 VHD 在 Azure Blob 存储上创建，并作为数据磁盘附加到主目标服务器。  
* **每个源的每日更改率上限** - 考虑每个源的推荐更改率时，需要考虑三个因素。 对于基于目标的考虑因素，源上每个操作的目标磁盘需要两个 IOPS。 这是因为目标磁盘上会发生旧数据的读取和新数据的写入。
  * **进程服务器支持的每日更改率** - 一个源计算机不能跨越多个进程服务器。 单个进程服务器可以支持多达 1 TB 的每日更改率。 因此 1 TB 是源计算机支持的每日数据更改率上限。
  * **目标磁盘支持的最大吞吐量** - 每个源磁盘的最大改动不能超过 144 GB/天（8K 写入大小）。 请参阅主目标部分中的表，以获取各种写入大小的目标的吞吐量与 IOPS。 必须将此数字除以 2，因为每个源 IOP 在目标磁盘上生成 2 个 IOPS。 请参阅 [Azure 可缩放性和性能目标](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)，了解如何配置高级存储帐户的目标。
  * **存储帐户支持的最大吞吐量** - 一个源不能跨越多个存储帐户。 假设某个存储帐户每秒可接受最多 20,000 个请求，并且每个源 IOP 在主目标服务器上生成 2 个 IOPS，则建议将源的 IOPS 数目保留为 10,000。 请参阅 [Azure 可缩放性和性能目标](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)，了解如何配置高级存储帐户的源。

### <a name="considerations-for-component-servers"></a>有关组件服务器的注意事项
表 1 汇总了配置服务器与主目标服务器的虚拟机大小。

| **组件** | **部署的 Azure 实例** | **核心数** | **内存** | **最大磁盘数** | **磁盘大小** |
| --- | --- | --- | --- | --- | --- |
| 配置服务器 |标准 A3 |4 |7 GB |8 |1023 GB |
| 主目标服务器 |标准 A4 |8 |14 GB |16 |1023 GB |
| 标准 D14 |16 |112 GB |32 |1023 GB | |
| 标准 DS4 |8 |28 GB |16 |1023 GB | |

**表 1**

#### <a name="process-server-considerations"></a>进程服务器注意事项
进程服务器的大小通常取决于跨所有受保护工作负荷的每日更改率。

* 需要有足够的计算容量才能执行内联压缩和加密等任务。
* 处理服务器使用基于磁盘的缓存。 请确保提供建议的缓存空间和磁盘吞吐量，以便在出现网络瓶颈或服务中断时能够存储数据更改。
* 确保带宽足以让进程服务器能够将数据上传到主目标服务器，从而持续提供数据保护。

表 2 提供了进程服务器的指导摘要。

| **数据更改率** | **CPU** | **内存** | **缓存磁盘大小** | **缓存磁盘吞吐量** | **带宽入口/出口** |
| --- | --- | --- | --- | --- | --- |
| < 300 GB |4 个 vCPU（2 个插槽 * 2 个核心 @ 2.5GHz） |4 GB |600 GB |每秒 7 到 10 MB |30 Mbps/21 Mbps |
| 300 到 600 GB |8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5GHz） |6 GB |600 GB |每秒 11 到 15 MB |60 Mbps/42 Mbps |
| 600 GB 到 1 TB |12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5GHz） |8 GB |600 GB |每秒 16 到 20 MB |100 Mbps/70 Mbps |
| > 1 TB |部署另一个进程服务器 | | | | |

**表 2**

其中：

* 入口是下载带宽（源与进程服务器之间的 Intranet）。
* 出口是上载带宽（进程服务器与主目标服务器之间的 Internet）。 出口数字假设平均进程服务器压缩率为 30%。
* 对于缓存磁盘，建议为所有进程服务器提供一个至少有 128 GB 的独立 OS 磁盘。
* 缓存磁盘吞吐量已使用以下存储进行基准测试：8 个采用 RAID 10 配置的 10K RPM SAS 驱动器。

#### <a name="configuration-server-considerations"></a>配置服务器注意事项
每个配置服务器最多支持 100 个包含 3 到 4 个卷的源计算机。 如果部署较大，我们建议部署另一个配置服务器。 有关配置服务器的默认虚拟机属性，请参阅“表 1”。

#### <a name="master-target-server-and-storage-account-considerations"></a>主目标服务器与存储帐户注意事项
每个主目标服务器的存储包括 OS 磁盘、保留卷和数据磁盘。 保留驱动器可以维护 Site Recovery 门户中定义的特定时段内的磁盘更改日志。  有关主目标的虚拟机服务器属性，请参阅“表 1”。 表 3 显示了如何使用 A4 的磁盘。

| **实例** | **OS 磁盘** | **保留** | **数据磁盘** |
| --- | --- | --- | --- |
| **保留** |**数据磁盘** | | |
| 标准 A4 |1 个磁盘 (1 * 1023 GB) |1 个磁盘 ( 1 * 1023 GB) |15 个磁盘 (15 * 1023 GB) |
| 标准 D14 |1 个磁盘 (1 * 1023 GB) |1 个磁盘 ( 1 * 1023 GB) |31 个磁盘 (15 * 1023 GB) |
| 标准 DS4 |1 个磁盘 (1 * 1023 GB) |1 个磁盘 ( 1 * 1023 GB) |15 个磁盘 (15 * 1023 GB) |

**表 3**

主目标服务器的容量规划取决于：

* Azure 存储空间性能和限制
  * 单个存储帐户中标准层 VM 的高度使用的磁盘数目上限约为 40（每个磁盘 20,000/500 IOPS）。 了解[标准存储帐户的可缩放性目标](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)和[高级存储帐户的可缩放性目标](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)。
* 每日更改率
* 保留卷存储。

请注意：

* 一个源不能跨越多个存储帐户。 此限制适用于属于配置保护时选择的存储帐户的数据磁盘。 OS 和保留磁盘通常属于自动部署的存储帐户。
* 所需的保留存储卷取决于每日更改率和保留天数。 每个主目标服务器所需的保留存储 = 每日来自源的变换总计 * 保留天数。
* 每个主目标服务器只有一个保留卷。 保留卷在附加到主目标服务器的磁盘之间共享。 例如：
  * 如果有一台包含 5 个磁盘的源计算机，并且在源上的每个磁盘生成 120 IOPS（8K 大小），则相当于每个磁盘 240 IOPS（在每个源的目标磁盘上生成 2 个 IO 操作）。 每个磁盘 IOPS 限制 500，在 Azure 中是 240 IOPS。
  * 在保留卷上，这会变成 120 * 5 = 600 IOPS，而这可能会造成瓶颈。 在此情况下，合理的策略是将更多磁盘添加到保留卷，并将这些磁盘分散成 RAID 条带配置。 由于 IOPS 分散在多个驱动器之间，因此可以提高性能。 要添加到保留卷的驱动器数目如下：
    * 来自源环境的 IOPS 总计 / 500
    * 每日来自源环境的变换总计（未压缩）/ 287 GB。 287 GB 是目标磁盘每日支持的最大吞吐量。 此度量值根据因数为 8K 的写入大小而异，因为在此情况下，8K 是假设的写入大小。 例如，如果写入大小为 4K，则吞吐量为 287/2。 同理，如果写入大小为 16K，则吞吐量为 287*2。
* 所需的存储帐户数 = 源 IOPS 总计/10000。

## <a name="before-you-start"></a>开始之前
| **组件** | **要求** | **详细信息** |
| --- | --- | --- |
| **Azure 帐户** |需要一个 [Microsoft Azure](https://azure.microsoft.com/) 帐户。 可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 | |
| **Azure 存储** |需要使用 Azure 存储帐户来存储复制的数据<br/><br/> 帐户应为[标准异地冗余存储帐户](../storage/common/storage-redundancy.md#geo-redundant-storage)或[高级存储帐户](../storage/common/storage-premium-storage.md)。<br/><br/> 该帐户必须位于 Azure Site Recovery 服务所在的同一区域，并与同一订阅相关联。 我们不支持跨资源组移动使用[新 Azure 门户](../storage/common/storage-create-storage-account.md)创建的存储帐户。<br/><br/> 若要了解详细信息，请参阅 [Microsoft Azure 存储简介](../storage/common/storage-introduction.md) | |
| **Azure 虚拟网络** |需要 Azure 虚拟网络，配置服务器和主目标服务器将部署在该网络上。 它应该位于 Azure Site Recovery 保管库所在的订阅和区域中。 如果要通过 ExpressRoute 或 VPN 连接复制数据，Azure 虚拟网络必须通过 ExpressRoute 连接或站点到站点 VPN 连接到本地网络。 | |
| **Azure 资源** |确保有足够的 Azure 资源用于部署所有组件。 详细了解 [Azure 订阅限制](../azure-subscription-service-limits.md)。 | |
| **Azure 虚拟机** |要保护的虚拟机应符合 [Azure 先决条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。<br/><br/> **磁盘计数** - 单个受保护的服务器最多可以支持 31 个磁盘<br/><br/> **磁盘大小** - 单个磁盘的容量不能超过 1023 GB<br/><br/> **群集** - 不支持群集服务器<br/><br/> **启动** - 不支持统一可扩展固件接口 (UEFI)/可扩展固件接口 (EFI) 启动<br/><br/> **卷** - 不支持 Bitlocker 加密卷<br/><br/> **服务器名称** - 名称应包含 1 到 63 个字符（字母、数字和连字符）。 名称必须以字母或数字开头，并以字母或数字结尾。 在计算机受到保护后，可以修改 Azure 名称。 | |
| **配置服务器** |在订阅中针对配置服务器创建基于 Azure Site Recovery Windows Server 2012 R2 库映像的标准 A3 虚拟机。 它将作为第一个实例在新的云服务中创建。 如果选择“公共 Internet”作为配置服务器的连接类型，将使用保留的公共 IP 地址创建云服务。<br/><br/> 安装路径应该只包含英文字符。 | |
| **主目标服务器** |Azure 虚拟机（标准 A4、D14 或 DS4）。<br/><br/> 安装路径应该只包含英文字符。 例如，对于运行 Linux 的主目标服务器，路径应为 **/usr/local/ASR**。 | |
| **进程服务器** |可以在运行带有最新更新的 Windows Server 2012 R2 的物理或虚拟机上部署处理服务器。 在 C:/ 上安装。<br/><br/> 我们建议将该服务器放置在要保护的计算机所在的网络和子网上。<br/><br/> 在进程服务器上安装 VMware vSphere CLI 5.5.0。 进程服务器上必须有 VMware vSphere CLI 组件，才能发现 vCenter 服务器管理的虚拟机或 ESXi 主机上运行的虚拟机。<br/><br/> 安装路径应该只包含英文字符。<br/><br/> 不支持 ReFS 文件系统。 | |
| **VMware** |用于管理 VMware vSphere 虚拟机监控程序的 VMware vCenter 服务器。 它应该运行的是带有最新更新的 vCenter 5.1 或 5.5 版。<br/><br/> 一个或多个包含要保护的 VMware 虚拟机的 vSphere 虚拟机监控程序。 虚拟机监控程序应该运行的是包含最新更新程序的 ESX/ESXi 版本 5.1 或 5.5。<br/><br/> VMware 虚拟机应已安装并在运行 VMware 工具。 | |
| **Windows 计算机** |运行 Windows 的受保护的物理服务器或 VMware 虚拟机有许多要求。<br/><br/> 支持的 64 位操作系统：**Windows Server 2012 R2**、**Windows Server 2012** 或 **Windows Server 2008 R2 SP1 及更高版本**。<br/><br/> 主机名、安装点、设备名称、Windows 系统路径（例如 C:\Windows）仅限使用英文字符。<br/><br/> 操作系统应安装在 C:\ 驱动器上。<br/><br/> 仅支持基本磁盘。 不支持动态磁盘。<br/><br/> 受保护的计算机上的防火墙规则应允许其访问 Azure 中的配置服务器和主目标服务器。p><p>需要提供管理员帐户（必须是 Windows 计算机上的本地管理员），才能在 Windows 服务器上推送安装移动服务。 如果提供的帐户不是域帐户，则需要在本地计算机上禁用远程用户访问控制。 为此，请在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下添加值为 1 的 LocalAccountTokenFilterPolicy DWORD 注册表项。 若要从 CLI 添加注册表项，请打开 cmd 或 powershell 并输入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。 [详细了解](https://msdn.microsoft.com/library/aa826699.aspx)访问控制。<br/><br/> 在故障转移后，如果要通过远程桌面连接到 Azure 中的 Windows 虚拟机，请确保对本地计算机启用远程桌面。 如果不是通过 VPN 连接，防火墙规则应允许通过 Internet 进行远程桌面连接。 | |
| **Linux 计算机** |支持的 64 位操作系统：**Centos 6.4、6.5、6.6**、**Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel 第 3 版 (UEK3)）**、**SUSE Linux Enterprise Server 11 SP3**。<br/><br/> 受保护的计算机上的防火墙规则应允许其访问 Azure 中的配置服务器和主目标服务器。<br/><br/> 受保护的计算机上的 /etc/hosts 文件应包含将本地主机名映射到与所有 NIC 相关联的 IP 地址的条目 <br/><br/> 如果要在故障转移后使用安全外壳客户端 (ssh) 连接运行 Linux 的 Azure 虚拟机，请确保将受保护的计算机上的安全外壳服务设置为在系统启动时自动启动，并且防火墙规则允许与其建立 ssh 连接。<br/><br/> 主机名、装载点、设备名称，以及 Linux 系统路径和文件名（例如 /etc/；/usr）只能采用英文形式。<br/><br/> 可以对使用以下存储的本地计算机启用保护：-<br>文件系统：EXT3、ETX4、ReiserFS、XFS<br>多路径软件设备映射程序（多路径）<br>卷管理器：LVM2<br>不支持使用 HP CCISS 控制器存储的物理服务器。 | |
| **第三方** |在这种情况下，一些部署组件依赖第三方软件才能正常工作。 有关完整列表，请参阅[第三方软件通知和信息](#third-party) | |

### <a name="network-connectivity"></a>网络连接
配置本地站点与部署基础结构组件（配置服务器、主目标服务器）的 Azure 虚拟网络之间的网络连接时，可以使用两个选项。 在部署配置服务器之前，需要决定所要使用的网络连接选项。 需要在部署时选择此设置。 以后无法更改它。

**Internet：**本地服务器（进程服务器、受保护的计算机）与 Azure 基础结构组件服务器（配置服务器、主目标服务器）之间的通信和数据复制是通过从本地到配置服务器和主目标服务器上的公共终结点的安全 SSL/TLS 连接进行的。 （唯一的例外是 TCP 端口 9080 上的进程服务器与主目标服务器之间的连接未加密。 在此连接上，只会交换与用于复制设置的复制协议相关的控制信息。）

![部署示意图 - Internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN**：本地服务器（进程服务器、受保护的计算机）与 Azure 基础结构组件服务器（配置服务器、主目标服务器）之间的通信和数据复制是通过从本地网络到部署配置服务器和主目标服务器的 Azure 虚拟网络的 VPN 连接进行的。 确保本地网络通过 ExpressRoute 连接或站点到站点 VPN 连接来与 Azure 虚拟网络连接。

![部署示意图 - VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)

## <a name="step-1-create-a-vault"></a>步骤 1：创建保管库
1. 登录到[管理门户](https://portal.azure.com)。
2. 展开“**数据服务**” > “**恢复服务**”，并单击“**Site Recovery 保管库**”。
3. 单击“**新建**” > “**快速创建**”。
4. 在“名称”中，输入一个友好名称以标识此保管库。
5. 在“区域” 中，为保管库选择地理区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”
6. 单击“创建保管库” 。

    ![新保管库](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

检查状态栏以确认保管库已成功创建。 保管库以“**活动**”状态列在主要的“**恢复服务**”页上。

## <a name="step-2-deploy-a-configuration-server"></a>步骤 2：部署配置服务器
### <a name="configure-server-settings"></a>配置服务器设置
1. 在“恢复服务”页中，单击保管库以打开“快速启动”页  。 也可随时使用该图标打开“快速启动”。

    ![“快速启动”图标](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)
2. 在下拉列表中，选择“**在使用 VMware/物理服务器的本地站点与 Azure 之间**”。
3. 在“**准备目标(Azure)资源**”中，单击“**部署配置服务器**”。

    ![部署配置服务器](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)
4. 在“**新配置服务器详细信息**”中，指定以下信息：

   * 配置服务器的名称和用于连接它的凭据。
   * 在“网络连接类型”下拉列表中，选择“**公共 Internet**”或“**VPN**”。 请注意，应用此设置后无法对其进行修改。
   * 选择该服务器所在的 Azure 网络。 如果使用 VPN，请确保 Azure 网络按预期连接到本地网络。
   * 指定要分配到该服务器的内部 IP 地址和子网。 请注意，任何子网中的前四个 IP 地址保留给 Azure 内部使用。 请使用任何其他可用的 IP 地址。

     ![部署配置服务器](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)
5. 在单击“**确定**”时，会在订阅中针对配置服务器创建基于 Azure Site Recovery Windows Server 2012 R2 库映像的标准 A3 虚拟机。 它将作为第一个实例在新的云服务中创建。 如果选择通过 Internet 建立连接，则会使用保留的公共 IP 地址创建云服务。 可以在“**作业**”选项卡中监视进度。

    ![监视进度](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)
6. 如果要通过 Internet 建立连接，请在部署配置服务器后，在 Azure 门户中的“**虚拟机**”页上记录分配给它的公共 IP 地址。 然后，在“终结点”选项卡中，记录映射到专用端口 443 的公用 HTTPS 端口。 稍后将主目标服务器和处理服务器注册到配置服务器时，需要用到这些信息。 配置服务器使用以下终结点部署：

   * HTTPS：公用端口用于协调通过 Internet 在组件服务器与 Azure 之间进行的通信。 专用端口 443 用于协调通过 VPN 在组件服务器与 Azure 之间进行的通信。
   * 自定义：公用端口用于通过 Internet 进行的故障回复工具通信。 专用端口 9443 用于通过 VPN 进行的故障回复工具通信。
   * PowerShell：专用端口 5986
   * 远程桌面：专用端口 3389

   ![VM 终结点](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

   > [!WARNING]
   > 请勿删除在配置服务器部署期间创建的任何终结点的公用或专用端口号。
   >
   >

配置服务器使用保留的 IP 地址部署在自动创建的 Azure 云服务中。 需要使用保留地址，以确保在云服务上的虚拟机（包括配置服务器）重启后，配置服务器云服务 IP 地址保持不变。 在停用配置服务器后，必须手动将保留的公共 IP 地址取消保留，否则会继续保留。 每个订阅保留的公共 IP 地址数默认限制为 20 个。 [详细了解](../virtual-network/virtual-networks-reserved-private-ip.md)保留 IP 地址。

### <a name="register-the-configuration-server-in-the-vault"></a>在保管库中注册配置服务器
1. 在“**快速启动**”页中，依次单击“**准备目标资源**” > “**下载注册密钥**”。 此时会自动生成密钥文件。 该文件在生成后的 5 天内有效。 将它复制到配置服务器。
2. 在“**虚拟机**”中，从虚拟机列表中选择配置服务器。 打开“**仪表板**”选项卡，并单击“**连接**”。 **打开**下载的 RDP 文件，使用远程桌面登录配置服务器。 如果使用 VPN，请使用内部 IP 地址（部署配置服务器时指定的地址）从本地站点进行远程桌面连接。 首次登录时，Azure Site Recovery 安装向导会自动运行。

    ![注册](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)
3. 在“**第三方软件安装**”中，单击“**我接受**”，下载并安装 MySQL。

    ![MySQL 安装](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)
4. 在“**MySQL 服务器详细信息**”中，创建用于登录 MySQL 服务器实例的凭据。

    ![MySQL 凭据](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)
5. 在“**Internet 设置**”中，指定配置服务器连接 Internet 的方式。 请注意：

   * 如果希望使用自定义代理，则应当在安装该提供程序之前设置它。
   * 单击“**下一步**”时，会运行测试来检查代理连接。
   * 如果确实使用自定义代理，或者默认代理要求进行身份验证，则需要输入代理详细信息，包括地址、端口和凭据。
   * 应可通过代理访问以下 URL：
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * 如果采用了基于 IP 地址的防火墙规则，请确保这些规则设置为，允许配置服务器与 [Azure 数据中心 IP 范围](https://msdn.microsoft.com/library/azure/dn175718.aspx)和 HTTPS (443) 协议中所述的 IP 地址通信。 必须你将打算使用的 Azure 区域以及美国西部的 IP 范围加入允许列表。

     ![代理注册](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)
6. 在“**提供程序错误消息本地化设置**”中，指定错误消息的显示语言。

    ![错误消息注册](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)
7. 在“**Azure Site Recovery 注册**”中，浏览并选择已复制到服务器的密钥文件。

    ![密钥文件注册](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)
8. 在向导的完成页上选择以下选项：

   * 选择“**启动帐户管理对话框**”，指定在完成向导后应该打开“管理帐户”对话框。
   * 选择“为 Cspsconfigtool 创建桌面图标”，在配置服务器上添加桌面快捷方式，以便能够随时打开“管理帐户”对话框，而无需重新运行向导。

     ![完成注册](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)
9. 单击“**完成**”，完成向导。 此时会生成一个通行短语。 将其复制到安全位置。 需要它进行身份验证，并将处理服务器和主目标服务器注册到配置服务器。 它还可用于确保配置服务器通信中的通道完整性。 可以重新生成该通行短语，但是此后，需要使用新通行短语重新注册主目标服务器和进程服务器。

    ![通行短语](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

注册后，保管库中的“**配置服务器**”页上会列出此配置服务器。

### <a name="set-up-and-manage-accounts"></a>设置和管理帐户
在部署期间，Site Recovery 将请求以下操作的凭据：

* 一个 VMware 帐户，可让站点恢复自动发现 vCenter 服务器或 vSphere 主机上的 VM。
* 添加要保护的计算机，以便  Site Recovery 可在这些计算机上安装移动服务时。

注册配置服务器后，可以打开“**管理帐户**”对话框，添加和管理要用于这些操作的帐户。 可通过多种方式实现此目的：

* 打开在安装程序最后一页上的对话框中选择为配置服务器 (cspsconfigtool) 创建的快捷方式。
* 打开配置服务器安装程序完成页上的对话框。

1. 在“**管理帐户**”中，单击“**添加帐户**”。 还可以修改和删除现有帐户。

    ![管理帐户](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)
2. 在“**帐户详细信息**”中，指定要在 Azure 中使用的帐户名和凭据（域/用户名）。

    ![管理帐户](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>连接到配置服务器
可通过两种方式连接到配置服务器：

* 通过 VPN 站点到站点或 ExpressRoute 连接
* 通过 Internet

请注意：

* Internet 连接将虚拟机的终结点与服务器的公用虚拟 IP 地址结合使用。
* VPN 连接将服务器的内部 IP 地址与终结点的专用端口结合使用。
* 确定是要从本地服务器通过 VPN 连接还是 Internet 连接到 Azure 中运行的各种组件服务器（配置服务器、主目标服务器，以访问控制和复制数据），这项决定是最终性的。 以后无法更改此设置。 如果这样做，将需要重新部署方案并重新保护计算机。  

## <a name="step-3-deploy-the-master-target-server"></a>步骤 3：部署主目标服务器
1. 依次单击“**准备目标(Azure)资源**” > “**部署主目标服务器**”。
2. 指定主目标服务器的详细信息和凭据。 该服务器将部署在配置服务器所在的同一个 Azure 网络中。 在单击完成时，将使用 Windows 或 Linux 库映像创建 Azure 虚拟机。

    ![目标服务器设置](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

请注意，任何子网中的前四个 IP 地址保留给 Azure 内部使用。 请指定任何其他可用的 IP 地址。

> [!NOTE]
> 如果为需要持续性的高 I/O 性能和低延迟才能使用[高级存储帐户](../storage/common/storage-premium-storage.md)托管 I/O 密集型工作负荷的工作负荷配置保护时，请选择“标准 DS4”。
>
>

1. 将使用这些终结点创建 Windows 主目标服务器 VM。 请注意，仅当通过 Internet 进行连接时，才创建公共终结点。

   * 自定义：进程服务器使用公用端口来通过 Internet 发送复制数据。 处理服务器使用专用端口 9443 来通过 VPN 将复制数据发送到主目标服务器。
   * 自定义 1：进程服务器使用公用端口来通过 Internet 发送元数据。 处理服务器使用专用端口 9080 来通过 VPN 将元数据发送到主目标服务器。
   * PowerShell：专用端口 5986
   * 远程桌面：专用端口 3389
2. 将使用这些终结点创建 Linux 主目标服务器 VM。 请注意，仅通过 Internet 进行连接时，才创建公共终结点。

   * 自定义：进程服务器使用公用端口通过 Internet 发送复制数据。 处理服务器使用专用端口 9443 来通过 VPN 将复制数据发送到主目标服务器。
   * 自定义 1：进程服务器使用公用端口通过 Internet 发送元数据。 处理服务器使用专用端口 9080 来通过 VPN 将元数据发送到主目标服务器
   * SSH：专用端口 22

     > [!WARNING]
     > 请勿删除或更改在主目标服务器部署期间创建的任何终结点的公用或专用端口号。
     >
     >
3. 在“**虚拟机**”中，等待虚拟机启动。

   * 如果它是 Windows 服务器，请记下远程桌面详细信息。
   * 如果它是 Linux 服务器，并且要通过 VPN 连接，请记下虚拟机的内部 IP 地址。 如果要通过 Internet 连接，请记下公用 IP 地址。
4. 登录到该服务器以完成安装，并将其注册到配置服务器。
5. 如果运行的是 Windows，请执行以下操作：

   1. 启动到虚拟机的远程桌面连接。 在首次登录时，会在 PowerShell 窗口中运行一个脚本。 请勿将其关闭。 在它完成之后，主机代理配置工具会自动打开以注册服务器。
   2. 在“**主机代理配置**”中，指定配置服务器的内部 IP 地址和端口 443。 可以使用内部地址和专用端口 443，即使你没有通过 VPN 连接，因为虚拟机附加到配置服务器所在的 Azure 网络。 选中“**使用 HTTPS**”。 输入以前记下的配置服务器的密码。 单击“**确定**”，注册服务器。 可以忽略 NAT 选项。 这些选项不会用到。
   3. 如果估计的保留驱动器要求超过 1 TB，可以使用虚拟磁盘和[存储空间](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)来配置保留卷 (R:)

   ![Windows 主目标服务器](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)
6. 如果运行的是 Linux，请执行以下操作：

   1. 在安装主目标服务器之前，请确保已安装最新的 Linux 集成服务 (LIS)。 有关最新版本的 LIS 和安装说明，请单击[此处](https://www.microsoft.com/download/details.aspx?id=46842)。 安装 LIS 后重新启动计算机。
   2. 在“**准备目标(Azure)资源**”中，单击“**下载并安装其他软件(仅适用于 Linux 主目标服务器)**”。 使用 sftp 客户端将下载的 tar 文件复制到虚拟机。 也可以登录到已部署的 Linux 主目标服务器，并使用 *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* 下载文件。
   3. 使用安全外壳客户端登录到服务器。 如果已通过 VPN 连接到 Azure 网络，请使用内部 IP 地址。 否则，请使用外部 IP 地址和 SSH 公用终结点。
   4. 运行 **tar –xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
      ![Linux 主目标服务器](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)，从 gzip 压缩的安装程序中提取文件
   5. 确保位于 tar 文件内容提取到的目录中。
   6. 使用命令 **echo *`<passphrase>`* >passphrase.txt**，将配置服务器密码复制到本地文件
   7. 运行命令“**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**”。

      ![注册目标服务器](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)
7. 等待几分钟 (10-15)，并在“ **服务器**” > “**配置服务器**”页上，检查主目标服务器是否作为注册服务器列在“**服务器详细信息**”选项卡中。如果运行的是 Linux，并且它未注册，请从 /usr/local/ASR/Vx/bin/hostconfigcli 再次运行主机配置工具。 需要以 root 身份运行 chmod 以设置访问权限。

    ![验证目标服务器](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

> [!NOTE]
> 在注册完成之后，最多需要 15 分钟，主目标服务器才会列在门户中。 若要立即进行更新，请单击“**配置服务器**”页上的“**刷新**”。
>
>

## <a name="step-4-deploy-the-on-premises-process-server"></a>步骤 4：部署本地进程服务器
在开始之前，建议在进程服务器上配置静态 IP 地址，以保证在重新启动后可持续使用相同的地址。

1. 依次单击“快速启动”>“**在本地安装进程服务器**” > “**下载并安装进程服务器**”。

    ![安装进程服务器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)
2. 将下载的 zip 文件复制到要将处理服务器安装到的服务器。 该 zip 文件包含两个安装文件：

   * Microsoft-ASR_CX_TP_8.4.0.0_Windows*
   * Microsoft-ASR_CX_8.4.0.0_Windows*
3. 解压缩存档，然后将安装文件复制到该服务器上的某个位置。
4. 运行 **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** 安装文件，并按相关说明操作。 此时会安装部署所需的第三方组件。
5. 然后，运行 **Microsoft-ASR_CX_8.4.0.0_Windows***。
6. 在“**服务器模式**”页上，选择“**进程服务器**”。
7. 在“**环境详细信息**”页上，执行以下操作：

    - 如果想要保护 VMware 虚拟机，请单击“**是**”
    - 如果只想保护物理服务器，则无需在进程服务器上安装 VMware vCLI。 请单击“**否**”并继续。

1. 安装 VMware vCLI 时，请注意以下事项：

   * **仅支持 VMware vSphere CLI 5.5.0**。 进程服务器无法与 vSphere CLI 的其他版本或更新配合使用。
   * 单击[此处](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)下载 vSphere CLI 5.5.0。
   * 如果在开始安装进程服务器之前刚刚安装了 vSphere CLI，并且安装程序未检测到它，请等待最多五分钟的时间再重新尝试安装。 这可以确保执行 vSphere CLI 检测所需的所有环境变量已正确初始化。
2. 在“**进程服务器的 NIC 选择范围**”中，选择进程服务器应该使用的网络适配器。

   ![选择适配器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)
3. 在“**配置服务器详细信息**”中：

   * 对于 IP 地址和端口，如果要通过 VPN 连接，请指定配置服务器的内部 IP 地址和端口 443。 否则，请指定公用虚拟 IP 地址和映射的公用 HTTP 终结点。
   * 键入配置服务器的密码。
   * 如果要在使用自动推送安装服务时禁用验证，请取消选中“**验证移动服务软件签名**”。 签名验证需要来自处理器的 Internet 连接。
   * 单击“资源组名称” 的 Azure 数据工厂。

   ![注册配置服务器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)
4. 在“**选择安装驱动器**”中，选择缓存驱动器。 进程服务器需要至少有 600 GB 可用空间的缓存驱动器。 然后单击“安装”。

   ![注册配置服务器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)
5. 请注意，可能需要重新启动服务器才能完成安装。 在“**配置服务器**” > “**服务器详细信息**”中，检查进程服务器是否已显示，且已在保管库中成功注册。

> [!NOTE]
> 注册完成之后，最多需要 15 分钟，配置服务器下才会列出进程服务器。 若要立即更新，请单击配置服务器页面底部的刷新按钮以刷新配置服务器。
>
>

![验证进程服务器](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

如果在注册进程服务器时未对移动服务禁用签名验证，则稍后可以按以下方式执行该操作：

1. 以管理员身份登录到处理服务器，并打开文件 C:\pushinstallsvc\pushinstaller.conf 以进行编辑。 在 **[PushInstaller.transport]** 部分下，添加下面这行代码：**SignatureVerificationChecks=”0”**。 保存并关闭该文件。
2. 重新启动 InMage PushInstall 服务。

## <a name="step-5-update-site-recovery-components"></a>步骤 5：更新站点恢复组件
站点恢复组件不定时更新。 当有新的更新可用时，应按以下顺序安装更新：

1. 配置服务器
2. 进程服务器
3. 主目标服务器
4. 故障回复工具 (vContinuum)

### <a name="obtain-and-install-the-updates"></a>获取和安装更新
1. 可以从 Site Recovery **仪表板**获取配置服务器、进程服务器和主目标服务器的更新。 对于 Linux 安装，请从 gzip 压缩过的安装程序中提取文件，并运行命令“sudo ./install”以安装更新。
2. [下载](http://go.microsoft.com/fwlink/?LinkID=533813)故障回复工具 (vContinuum) 的最新更新程序。
3. 如果运行的虚拟机或物理服务器上已安装移动服务，可以按如下所示获取服务更新：

   * **选项 1**：下载更新程序：
     * [Windows Server（仅限 64 位）](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
     * [CentOS 6.4、6.5、6.6（仅限 64 位）](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
     * [Oracle Enterprise Linux 6.4、6.5（仅限 64 位）](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
     * [SUSE Linux Enterprise Server SP3（仅限 64 位）](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
     * 在更新进程服务器之后，进程服务器上的 C:\pushinstallsvc\repository 文件夹中会提供移动服务的更新版本。
   * **选项 2**：如果计算机上安装了旧版移动服务，可以从管理门户自动升级计算机上的移动服务。

     1. 确保进程服务器已更新。
     2. 请确保受保护的计算机符合自动推送移动服务的[先决条件](#install-the-mobility-service-automatically)，以便更新程序可以正常运行。
     3. 选择保护组，选中受保护的计算机，并单击“**更新移动服务**”。 仅当有更高版本的移动服务时，此按钮才可用。

         ![选择 vCenter 服务器](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

在“选择帐户”中，指定用于更新受保护服务器上的移动服务的管理员帐户。 单击“确定”并等待触发的作业完成。

## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>步骤 6：添加 vCenter 服务器或 vSphere 主机
1. 依次单击“**服务器**” > “**配置服务器**”>“配置服务器”>“**添加 vCenter Server**”，添加 vCenter 服务器或 vSphere 主机。

    ![选择 vCenter 服务器](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)
2. 指定服务器或主机的详细信息，选择要用于发现它的处理服务器。

   * 如果 vCenter 服务器不在默认端口 443 上运行，请指定 vCenter 服务器运行所在的端口号。
   * 进程服务器必须位于 vCenter 服务器/vSphere 主机所在的网络上，并且应安装有 VMware vSphere CLI 5.5.0。

     ![vCenter 服务器设置](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)
3. 发现完成之后，vCenter 服务器会在“配置服务器详细信息”下方列出。

    ![vCenter 服务器设置](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)
4. 如果使用非管理员帐户添加服务器或主机，请确保该帐户具有以下权限：

   * vCenter 帐户应该已启用数据中心、数据存储、文件夹、主机、网络、资源、存储视图、虚拟机和 vSphere 分布式交换机权限。
   * vSphere 主机帐户应该已启用数据中心、数据存储、文件夹、主机、网络、资源、虚拟机和 vSphere 分布式交换机权限

## <a name="step-7-create-a-protection-group"></a>步骤 7：创建保护组
1. 依次打开“**受保护的项**” > “**保护组**” > “**创建保护组**”。

    ![创建保护组](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)
2. 在“**指定保护组设置**”页上，指定保护组的名称，并选择要在其上创建该组的配置服务器。

    ![保护组设置](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)
3. 在“**指定复制设置**”页上，配置要对该组中所有计算机使用的复制设置。

    ![保护组复制](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)
4. 设置：

   * **多 VM 一致性**：如果启用此设置，可以跨保护组中的计算机创建共享的应用程序一致性恢复点。 当保护组中的所有计算机运行的是相同工作负荷时，此设置最为有用。 所有计算机都将恢复到相同数据点。 仅适用于 Windows 服务器。
   * **RPO 阈值**：当连续数据保护复制 RPO 超过配置的 RPO 阈值时，系统会生成警报。
   * **恢复点保留期**：指定保留时段。 受保护的计算机可在此时段内恢复到任何点。
   * **应用程序一致性快照频率**：指定创建包含应用程序一致性快照的恢复点的频率。

可以监视保护组，因为它们是在“**受保护的项**”页上创建的。

## <a name="step-8-set-up-machines-you-want-to-protect"></a>步骤 8：设置想要保护的计算机
需在要保护的虚拟机和物理服务器上安装移动服务。 可通过两种方式实现此目的：

* 从进程服务器自动推送并在每台计算机上安装该服务。
* 手动安装该服务。

### <a name="install-the-mobility-service-automatically"></a>自动安装移动服务
在向保护组添加计算机时，进程服务器会自动推送移动服务，并将其安装在每个计算机上。

**在 Windows 服务器上自动推送安装移动服务：**

1. 按[第 5 步：安装最新更新程序](#step-5-install-latest-updates)中所述操作，安装进程服务器的最新更新程序，同时确保进程服务器可用。
2. 确保源计算机和进程服务器之间已建立网络连接，并且可从进程服务器访问源计算机。  
3. 将 Windows 防火墙配置为允许“**文件和打印机共享**”和“**Windows Management Instrumentation**”。 在 Windows 防火墙设置中，选择“允许应用或功能通过防火墙”选项，并选择应用程序，如下图所示。 对于属于某个域的计算机，可以使用 GPO 配置防火墙策略。

    ![防火墙设置](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)
4. 用于执行推式安装的帐户必须位于要保护的计算机上的“管理员”组中。 这些凭据仅用于推送移动服务的安装，在向保护组添加计算机时，将要提供这些凭据。
5. 如果提供的帐户不是域帐户，需要在本地计算机上禁用远程用户访问控制。 为此，请在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下添加值为 1 的 LocalAccountTokenFilterPolicy DWORD 注册表项。 若要从 CLI 添加注册表项，请打开 cmd 或 powershell 并输入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。

**在 Linux 服务器上自动推送安装移动服务：**

1. 按[第 5 步：安装最新更新程序](#step-5-install-latest-updates)中所述操作，安装进程服务器的最新更新程序，同时确保进程服务器可用。
2. 确保源计算机和进程服务器之间已建立网络连接，并且可从进程服务器访问源计算机。  
3. 确保帐户是源 Linux 服务器上的 root 用户。
4. 确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有 NIC 关联的 IP 地址的条目。
5. 在要保护的计算机上安装最新的 openssh、openssh-server 和 openssl 包。
6. 确保 SSH 已启用且正在端口 22 上运行。
7. 在 sshd_config 文件中启用 SFTP 子系统与密码身份验证，如下所示：

   * a) 以 root 身份登录。
   * b) 在文件 /etc/ssh/sshd_config 中，找到以 **PasswordAuthentication** 开头的行。
   * c) 取消注释该行，并将值从“no”更改为“yes”。

       ![Linux 移动服务](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)
   * d) 找到以 Subsystem 开头的行，并取消注释该行。

       ![Linux 推送移动服务](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    
8. 确保支持源计算机 Linux 变体。

### <a name="install-the-mobility-service-manually"></a>手动安装移动服务
用于安装移动服务的软件包位于进程服务器上的 C:\pushinstallsvc\repository 中。 请登录进程服务器，并根据下表将相应的安装包复制到源计算机：

| 源操作系统 | 进程服务器上的移动服务包 |
| --- | --- |
| Windows Server（仅限 64 位） |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4、6.5、6.6（仅限 64 位） |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3（仅限 64 位） |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz` |
| Oracle Enterprise Linux 6.4、6.5（仅限 64 位） |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |

**若要在 Windows 服务器上手动安装移动服务**，请执行以下操作：

1. 将上表所列进程服务器目录路径中的 **Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** 包复制到源计算机。
2. 在源计算机上运行该可执行文件以安装移动服务。
3. 遵循安装程序说明操作。
4. 选择“**移动服务**”作为角色，并单击“**下一步**”。

    ![安装移动服务](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)
5. 将安装目录保留为默认安装路径，然后单击“**安装**”。
6. 在“**主机代理配置**”中，指定配置服务器的 IP 地址与 HTTPS 端口。

   * 如果通过 Internet 进行连接，请指定公共虚拟 IP 地址和公共 HTTPS 终结点作为端口。
   * 如果通过 VPN 进行连接，请指定内部 IP 地址和端口 443。 选中“**使用 HTTPS**”。

     ![安装移动服务](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)
7. 指定配置服务器通行短语，然后单击“**确定**”将移动服务注册到配置服务器。

**若要通过命令行运行，请执行以下操作：**

1. 将通行短语从 CX 复制到服务器上的“C:\connection.passphrase”文件中，并运行此命令。 在本示例中，CX 是 104.40.75.37，HTTPS 端口是 62519：

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**在 Linux 服务器上手动安装移动服务**：

1. 根据上表，将相应的 tar 存档从进程服务器复制到源计算机。
2. 打开 shell 程序，并通过执行 `tar -xvzf Microsoft-ASR_UA_8.2.0.0*` 将压缩的 tar 存档解压缩到本地路径
3. 通过 shell 输入 *`echo <passphrase> >passphrase.txt`*，在 tar 存档内容解压缩到的本地目录中创建 passphrase.txt 文件。
4. 输入 *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*，安装移动服务。
5. 指定 IP 地址和端口：

   * 如果通过 Internet 连接到配置服务器，请在 `<IP address>` 和 `<port>` 中指定配置服务器的虚拟公共 IP 地址和公共 HTTPS 终结点。
   * 如果通过 VPN 建立连接，请指定内部 IP 地址和端口 443。

**若要通过命令行运行，请执行以下操作**：

1. 将通行短语从 CX 复制到服务器上的“passphrase.txt”文件中，并运行此命令。 在本示例中，CX 是 104.40.75.37，HTTPS 端口是 62519：

在生产服务器上安装：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

在目标服务器上安装：

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

> [!NOTE]
> 如果将计算机添加到已运行适当版本移动服务的保护组，则会跳过推送安装。
>
>

## <a name="step-9-enable-protection"></a>步骤 9：启用保护
要启用保护，请将虚拟机和物理服务器添加到保护组。 开始之前，请注意：

* 系统会每隔 15 分钟发现虚拟机一次，在发现之后，最多需要 15 分钟时间虚拟机才会出现在 Azure Site Recovery 中。
* 虚拟机上的环境更改（例如 VMware 工具安装）最多也可能需要 15 分钟时间才能在 Site Recovery 中更新。
* 可以在“**配置服务器**”页上 vCenter 服务器/ESXi 主机的“**上次联系时间**”字段中检查上次发现的时间。
* 如果已创建保护组，并在其后添加 vCenter 服务器或 ESXi 主机，则 Azure Site Recovery 门户需要 15 分钟刷新，而虚拟机也需要 15 分钟才会列在“**向保护组添加计算机**”对话框中。
* 如果要立即将计算机添加到保护组，而不想等待完成计划的发现，请选中配置服务器（请勿单击），然后单击“**刷新**”按钮。
* 将虚拟机或物理机添加到保护组时，进程服务器会自动推送并在源服务器上安装移动服务（如果尚未安装）。
* 为了使自动推送机制发挥作用，请确保已按上一步所述设置受保护的计算机。

按如下所示添加计算机：

1. 依次单击“**受保护的项**” > “**保护组**” > “**计算机**” > “**添加计算机**”。 作为最佳实践，我们建议，保护组应镜像工作负荷，以便于向相同组中添加运行特定应用程序的计算机。
2. 如果要保护物理服务器，请在“**添加物理计算机**”向导的“**选择虚拟机**”中，提供 IP 地址和友好名称。 然后选择操作系统系列。

    ![添加 V-Center 服务器](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)
3. 如果要保护 VMware 虚拟机，请在“**选择虚拟机**”中，选择负责管理虚拟机（或其运行所在的 EXSi 主机）的 vCenter 服务器，然后选择计算机。

    ![添加 V-Center 服务器](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)    
4. 在“**指定目标资源**”中，选择用于复制的主目标服务器和存储，并选择是否应该对所有工作负荷使用设置。 为需要持续性的高 IO 性能和低延迟才能托管 IO 密集型工作负荷的工作负荷配置保护时，请选择“[高级存储帐户](../storage/common/storage-premium-storage.md)”。 如果希望工作负荷磁盘使用高级存储帐户，则需使用 DS 系列的主目标。 非 DS 系列的主目标无法使用高级存储磁盘。

   > [!NOTE]
   > 我们不支持跨资源组移动使用[新 Azure 门户](../storage/common/storage-create-storage-account.md)创建的存储帐户。
   >
   >

    ![vCenter 服务器](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)
5. 在“**指定帐户**”中，选择用于在受保护的计算机上安装移动服务的帐户。 需要使用帐户凭据来自动安装移动服务。 如果无法选择帐户，请确保按步骤 2 中所述设置一个。 请注意，Azure 无法访问此帐户。 对于 Windows 服务器，帐户应该对源服务器具有管理员权限。 对于 Linux，帐户必须是 root。

    ![Linux 凭据](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)
6. 单击复选标记以完成向保护组添加计算机，并对每个计算机启动初始复制。 可以在“**作业**”页上监视状态。

    ![添加 V-Center 服务器](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)
7. 此外，可以单击“**受保护的项**”>“保护组名称”>“**虚拟机**”来监视保护状态。 初始复制完成并且计算机同步数据后，状态显示为“**受保护**”。

    ![虚拟机作业](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)

### <a name="set-protected-machine-properties"></a>设置受保护的计算机属性
1. 在计算机的状态为“**受保护**”后，可以配置其故障转移属性。 在“保护组详细信息”中，选择计算机，并打开“**配置**”选项卡。
2. 在故障转移后，可以在 Azure 中修改要提供给计算机的名称，以及 Azure 虚拟机大小。 还可以选择故障转移后要将计算机连接到的 Azure 网络。

   > [!NOTE]
   > 用于部署 Site Recovery 的网络不支持跨同一订阅中的资源组或跨订阅[迁移网络](../resource-group-move-resources.md)。
   >
   >

    ![设置虚拟机属性](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

请注意：

* Azure 计算机的名称必须符合 Azure 要求。
* 默认情况下，Azure 中的复制虚拟机不连接到 Azure 网络。 如果希望复制的虚拟机进行通信，请确保为其设置相同的 Azure 网络。
* 如果调整了 VMware 虚拟机或物理服务器上卷的大小，它将进入关键状态。 如果需要修改大小，请执行以下操作：

  * a) 更改大小设置。
  * b) 在“**虚拟机**”选项卡中选择虚拟机，并单击“**删除**”。
  * c) 在“**删除虚拟机**”中，选择“**禁用保护(用于恢复深化和重设卷大小)**”。 此选项将禁用保护，但在 Azure 中保留恢复点。

      ![设置虚拟机属性](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)
  * d) 为虚拟机重新启用保护。 在重新启用保护时，调整过大小的卷的数据会被传输到 Azure。

## <a name="step-10-run-a-failover"></a>步骤 10：运行故障转移
目前只能为受保护的 VMware 虚拟机和物理服务器运行非计划的故障转移。 注意以下事项：

* 启动故障转移之前，请确保配置服务器和主目标服务器正常运行， 否则故障转移会失败。
* 在执行非计划的故障转移期间，源计算机不会关闭。 执行非计划的故障转移会停止受保护服务器的数据复制。 需要从保护组中删除计算机并重新添加，才能在非计划的故障转移完成之后再次开始保护计算机。
* 如果想要故障转移而不丢失任何数据，请确保主站点虚拟机在启动故障转移之前已关闭。

1. 在“**恢复计划**”页上，添加恢复计划。 指定该计划的详细信息，并选择“**Azure**”作为目标。

    ![配置恢复计划](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)
2. 在“**选择虚拟机**”中，选择保护组，并选择该组中要添加到恢复计划的计算机。 [详细了解](site-recovery-create-recovery-plans.md)恢复计划。

    ![添加虚拟机](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)
3. 如果需要，可以自定义该计划以创建组，并排列顺序，恢复计划中的计算机以该顺序进行故障转移。 还可以添加手动操作和脚本的提示。 在恢复到 Azure 时，可以使用 [Azure 自动化 Runbook](site-recovery-runbook-automation.md) 添加脚本。
4. 在“**恢复计划**”页中，选择该计划，并单击“**非计划的故障转移**”。
5. 在“**确认故障转移**”中，确认故障转移方向（到 Azure），并选择要故障转移到的恢复点。
6. 等待故障转移作业完成，验证故障转移是否按预期工作，以及复制的虚拟机是否在 Azure 中成功启动。

## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>步骤 11：故障回复 Azure 中已故障转移的计算机
[详细了解](site-recovery-failback-azure-to-vmware-classic-legacy.md)如何将 Azure 中运行的已故障转移的计算机恢复到本地环境。

## <a name="manage-your-process-servers"></a>管理进程服务器
进程服务器将复制数据发送到 Azure 中的主目标服务器，并发现已添加到 vCenter 服务器的新 VMware 虚拟机。 在以下情况下，可能要更改部署中的进程服务器：

* 如果当前进程服务器已关闭
* 如果恢复点目标 (RPO) 提升到组织无法接受的程度。

如果需要，可以将部分或所有本地 VMware 虚拟机和物理服务器的复制转移到另一台进程服务器。 例如：

* **故障** - 如果进程服务器发生故障或不可用，可以将受保护的计算机复制转移到另一台进程服务器。 源计算机和副本计算机的元数据将转移到新的进程服务器并且会重新同步。 新的进程服务器会自动连接到 vCenter 服务器以执行自动发现。 可以在 Site Recovery 仪表板上监视进程服务器的状态。
* **负载平衡以调整 RPO** - 若要改善负载平衡，你可以在 Site Recovery 门户中选择其他进程服务器，然后将一台或多台计算机的复制转移到该服务器，以手动进行负载平衡。 在此情况下，选择的源和副本计算机的元数据将转移到新的进程服务器。 原始进程服务器仍然连接到 vCenter 服务器。

### <a name="monitor-the-process-server"></a>监视进程服务器
如果进程服务器处于关键状态，Site Recovery 仪表板上会显示状态警告。 可以单击状态以打开“事件”选项卡，并向下钻取到“作业”选项卡上的特定作业。

### <a name="modify-the-process-server-used-for-replication"></a>修改用于复制的进程服务器
1. 依次打开“**服务器**” > “**配置服务器**”>“配置服务器”>“**服务器详细信息**”。
2. 在要修改的服务器旁边，依次单击“**进程服务器**” > “**更改进程服务器**”。

    ![更改进程服务器 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)
3. 在“**更改进程服务器**” > “**目标进程服务器**”中选择要使用的新服务器，并选择要复制到新服务器的虚拟机。 单击服务器名称旁边的信息图标，以查看可用空间和已用内存的详细信息。 随后会显示将每个所选虚拟机复制到新进程服务器所需的平均空间，以帮助你做出负载决策。

    ![更改进程服务器 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)
4. 单击复选标记以开始复制到新的进程服务器。 请注意，如果从某个重要的进程服务器中删除所有虚拟机，仪表板中应该不再会显示关键警告。

## <a name="third-party-software-notices-and-information"></a>第三方软件通知和信息
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

完整文件可以在 [ Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=529428)上找到。 Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

