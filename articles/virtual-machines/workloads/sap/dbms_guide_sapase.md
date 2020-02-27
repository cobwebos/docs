---
title: 适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署 | Microsoft Docs
description: 适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 007e8d87c670376ad334c1c4e58fd93995930b78
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616239"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署

本文档介绍在 Azure IaaS 中部署 SAP 时要考虑的多个不同领域。 在阅读本文档之前，应已经阅读了[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)文档以及 [Azure 文档上的 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)中的其他指南。 本文档介绍在 Linux 和 Windows 操作系统上运行的 SAP ASE。 Azure 支持的最低版本为 SAP ASE 16.0 修补程序级别2。  建议部署最新版本的 SAP 和最新的修补程序级别。  建议使用最小的 SAP ASE 16.3 修补程序级别7。  最新版本的 SAP 可以在[目标 ASE 16.0 发行计划和 CR List 信息](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)中找到。

有关 SAP 应用程序或安装媒体位置的发布支持的其他信息，请参阅以下位置的 SAP 产品可用性矩阵：

- [SAP 支持说明 #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP 支持说明 #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP 支持说明 #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP 支持说明 #1973241](https://launchpad.support.sap.com/#/notes/1973241)

注释：在 SAP 世界内外的所有文档中，产品的名称被称为 Sybase ASE 或 SAP ASE，或在某些情况下均称为。 为了保持一致，我们在本文档中使用名称**SAP ASE** 。

## <a name="operating-system-support"></a>操作系统支持
SAP 产品可用性矩阵包含每个 SAP 应用程序支持的操作系统和 SAP 内核组合。  Linux 分发 SUSE 2.x、SUSE 4.6 和 Red Hat 7. x 完全受支持。  不支持作为 SAP ASE 的操作系统 Oracle Linux。  建议使用可用的最新 Linux 版本。 Windows 客户应该使用 Windows Server 2016 或 Windows Server 2019 版本。  更早版本的 Windows （如 Windows 2012）在技术上受支持，但我们始终建议使用最新的 Windows 版本。


## <a name="specifics-to-sap-ase-on-windows"></a>有关 Windows 上的 SAP ASE 的具体信息
自从有了 Microsoft Azure，可以将现有 SAP ASE 应用程序迁移到 Azure 虚拟机。 借助 Azure 虚拟机中的 SAP ASE，可以轻松地将这些应用程序迁移到 Microsoft Azure，从而减少部署、管理和维护企业级应用程序的总拥有成本。 借助 Azure 虚拟机中的 SAP ASE，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。

Microsoft Azure 提供了许多不同的虚拟机类型，可运行最小的 SAP 系统和布局，也可运行包含成千上万用户的大型 SAP 系统和布局。 [Sap 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)中提供了不同 sap 认证的 VM SKU 的 SAP 大小调整 sap 数量。

适用于 windows 的[SAP Ase 安装指南中提供](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)了用于在 windows 上安装 sap ase 的文档

"锁定内存中的页" 是一种将阻止 SAP ASE 数据库缓冲区分页的设置。 此设置适用于具有大量内存的大型繁忙系统。 有关详细信息，请联系 BC-SYB。 


## <a name="linux-operating-system-specific-settings"></a>Linux 操作系统特定设置
在 Linux Vm 上，使用配置文件 SAP 运行 `saptune`： ASE Linux 大页面应在默认情况下启用，并且可以通过命令进行验证  

`cat /proc/meminfo` 

页面大小通常为 2048 KB。 有关详细信息，请参阅文章[在 Linux 上的大型页面](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>针对 SAP ASE 部署的 VM 和磁盘结构的建议

Sap[支持说明](https://launchpad.support.sap.com/#/notes/1928533)中列出的任何 vm 类型都支持 sap ASE For Sap NetWeaver 应用程序 #1928533 用于中型 SAP ASE 数据库服务器的典型 vm 类型包括 Esv3。  大型多 tb 数据库可以利用 M 系列 VM 类型。 可以通过启用 M 系列写入加速器来改善 SAP ASE 事务日志磁盘写入性能。 由于 SAP ASE 执行日志写入的方式，应小心地测试写入加速器。  查看[SAP 支持说明 #2816580](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)并考虑运行性能测试。  
写入加速器仅适用于事务日志磁盘。 磁盘级别缓存应设置为 "无"。 如果 Azure 写入加速器与其他 DBMS 相比，没有显示类似的改进，别惊讶。 根据 SAP ASE 写入事务日志的方式，Azure 写入加速器不会有很小的加速。
建议为数据设备和日志设备使用不同的磁盘。  系统数据库 sybsecurity 和 `saptools` 不需要专用磁盘，并可放置在包含 SAP 数据库数据和日志设备的磁盘上 

![SAP ASE 的存储配置](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>文件系统，条带大小 & IO 均衡 
除非另行配置，否则 SAP ASE 会按顺序将数据写入磁盘存储设备。 这意味着，具有四个设备的空 SAP ASE 数据库只会将数据写入第一个设备。  当第一个设备满时，其他磁盘设备将仅写入到中。  每个 SAP ASE 设备的读取和写入 IO 量可能不同。 若要平衡所有可用 Azure 磁盘上的磁盘 IO，需要使用 Windows 存储空间或 Linux LVM2。 在 Linux 上，建议使用 XFS 文件系统格式化磁盘。 应该使用性能测试来测试 LVM 条带大小。 128 KB 条带大小是一个很好的起点。 在 Windows 上，应测试 NTFS 分配单元大小（澳大利亚）。 64 KB 可用作起始值。 

建议按照在 SAP 自适应服务器企业和[sap 支持说明 #1815695](https://launchpad.support.sap.com/#/notes/1815695)[中配置自动数据库空间扩展](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)一文中所述配置自动数据库扩展。 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Azure 虚拟机、磁盘和文件系统配置中的示例 SAP ASE 
下面的模板显示适用于 Linux 和 Windows 的示例配置。 确认虚拟机和磁盘配置之前，请确保单个 VM 的网络和存储带宽配额足以满足业务要求。 同时请记住，不同的 Azure VM 类型具有不同的可附加到 VM 的最大磁盘数。 例如，E4s_v3 VM 的存储 IO 吞吐量限制为 48 MB/秒。 如果数据库备份活动所需的存储吞吐量超过 48 MB/秒，则更大的 VM 类型具有更大的存储带宽吞吐量是不可避免的。 配置 Azure 存储时，还需要记住，尤其是在[Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance)中，每 GB 容量的吞吐量和 IOPS 都发生变化。 有关详细信息，请参阅本主题中的[如何在 Azure 中使用哪些磁盘类型？](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types)。 特定 Azure VM 类型的配额记录在 "[内存优化" 虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/sizes-memory)和链接到该虚拟机的文章中。 

> [!NOTE]
>  如果 DBMS 系统从本地移至 Azure，则建议在 VM 上执行监视，并评估 CPU、内存、IOPS 和存储吞吐量。 将观测到的最大值与上面提到的文章中记录的 VM 配额限制进行比较

下面给出的示例用于说明目的，可以根据单个需要进行修改。 由于 SAP ASE 的设计，数据设备的数量与其他数据库不一样重要。 本文档中详细说明的数据设备数只是一个指南。 

数据库大小介于 50 GB – 250 GB （如 SAP 解决方案管理器）的小型 SAP ASE DB 服务器的配置示例可能如下所示：

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | E4s_v3 （4 vCPU/32 GB RAM） | E4s_v3 （4 vCPU/32 GB RAM） | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高版本 | 16.3 PL 7 或更高版本 | --- |
| 数据设备数 | 4 | 4 | ---|
| 日志设备数 | 1 | 1 | --- |
| temp 设备数 | 1 | 1 | SAP BW 工作负荷的详细信息 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要工作负荷测试 | 需要工作负荷测试 | --- |
| 数据磁盘的数量和类型 | 高级存储： 2 x P10 （RAID0） | 高级存储： 2 x P10 （RAID0）| 缓存 = 只读 |
| 日志磁盘的数量和类型 | 高级存储： 1 x P20  | 高级存储： 1 x P20 | 缓存 = 无 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设单个实例 |
| 备份设备数 | 4 | 4| --- |
| 备份磁盘的数量和类型 | 1 | 1 | --- |


例如，如果数据库大小介于 250 GB – 750 GB 之间，则中型 SAP ASE DB 服务器的配置示例可能如下所示：

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | E16s_v3 （16 vCPU/128 GB RAM） | E16s_v3 （16 vCPU/128 GB RAM） | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高版本 | 16.3 PL 7 或更高版本 | --- |
| 数据设备数 | 8 | 8 | ---|
| 日志设备数 | 1 | 1 | --- |
| temp 设备数 | 1 | 1 | SAP BW 工作负荷的详细信息 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要工作负荷测试 | 需要工作负荷测试 | --- |
| 数据磁盘的数量和类型 | 高级存储： 4 x P20 （RAID0） | 高级存储： 4 x P20 （RAID0）| 缓存 = 只读 |
| 日志磁盘的数量和类型 | 高级存储： 1 x P20  | 高级存储： 1 x P20 | 缓存 = 无 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设单个实例 |
| 备份设备数 | 4 | 4| --- |
| 备份磁盘的数量和类型 | 1 | 1 | --- |

数据库大小介于 750 GB – 2000 GB （如大型 SAP Business Suite system）的小型 SAP ASE DB 服务器的配置示例可能如下所示：

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | E64s_v3 （64 vCPU/432 GB RAM） | E64s_v3 （64 vCPU/432 GB RAM） | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高版本 | 16.3 PL 7 或更高版本 | --- |
| 数据设备数 | 16 | 16 | ---|
| 日志设备数 | 1 | 1 | --- |
| temp 设备数 | 1 | 1 | SAP BW 工作负荷的详细信息 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要工作负荷测试 | 需要工作负荷测试 | --- |
| 数据磁盘的数量和类型 | 高级存储： 4 x P30 （RAID0） | 高级存储： 4 x P30 （RAID0）| 缓存 = 只读 |
| 日志磁盘的数量和类型 | 高级存储： 1 x P20  | 高级存储： 1 x P20 | 缓存 = 无 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设单个实例 |
| 备份设备数 | 4 | 4| --- |
| 备份磁盘的数量和类型 | 1 | 1 | --- |


数据库大小为 2 TB + 的小型 SAP ASE DB 服务器的配置示例，如更大的全局使用的 SAP 业务套件系统，如下所示

| 配置 | Windows | Linux | 注释 |
| --- | --- | --- | --- |
| VM 类型 | M 系列（1.0 到 4.0 TB RAM）  | M 系列（1.0 到 4.0 TB RAM） | --- |
| 加速网络 | 启用 | 启用 | ---|
| SAP ASE 版本 | 16.3 PL 7 或更高版本 | 16.3 PL 7 或更高版本 | --- |
| 数据设备数 | 32 | 32 | ---|
| 日志设备数 | 1 | 1 | --- |
| temp 设备数 | 1 | 1 | SAP BW 工作负荷的详细信息 |
| 操作系统 | Windows Server 2019 | SUSE 12 SP4/15 SP1 或 RHEL 7。6 | --- |
| 磁盘聚合 | 存储空间 | LVM2 | --- |
| 文件系统 | NTFS | XFS |
| 格式块大小 | 需要工作负荷测试 | 需要工作负荷测试 | --- |
| 数据磁盘的数量和类型 | 高级存储： 4 + x P30 （RAID0） | 高级存储： 4 + x P30 （RAID0）| 缓存 = 只读，考虑 Azure Ultra 磁盘 |
| 日志磁盘的数量和类型 | 高级存储： 1 x P20  | 高级存储： 1 x P20 | 缓存 = 无，考虑 Azure Ultra 磁盘 |
| ASE MaxMemory 参数 | 90% 的物理 RAM | 90% 的物理 RAM | 假设单个实例 |
| 备份设备数 | 16 | 16 | --- |
| 备份磁盘的数量和类型 | 4 | 4 | 使用 LVM2/存储空间 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Azure 上的 SAP ASE 的备份 & 还原注意事项
增加数据和备份设备的数量会提高备份和还原的性能。 建议将托管 SAP ASE 备份设备的 Azure 磁盘划分为显示在前面所示的表中。 应小心平衡备份设备和磁盘的数量，并确保备份吞吐量不应超过全部 VM 吞吐量配额的 40%-50%。 建议使用 SAP 备份压缩作为默认值。 可在以下文章中找到更多详细信息：

- [SAP 支持说明 #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP 支持说明 #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP 支持说明 #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

不要使用驱动器 D:\或/temp 空间作为数据库或日志转储目标。

### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 i/o 带宽可能变成限制因素的配置中，可减少 IOPS 的度量值有助于扩展可在 Azure 等 IaaS 方案中运行的工作负荷。 因此，我们建议确保先使用 SAP ASE 压缩，然后再将现有 SAP 数据库上传到 Azure。

之所以建议先应用库压缩，再上传到 Azure，原因有以下几点：

* 减少要上传到 Azure 的数据量
* 假设可以在本地使用功能更强大的硬件（具有更多 CPU、更高 I/O 带宽或更少 I/O 延迟），则执行压缩的持续时间较短
* 较小的数据库大小可以降低磁盘分配的成本

数据和 LOB 压缩可以在 Azure 虚拟机托管的 VM 中运行，如同在本地运行一样。 有关如何检查是否已在现有 SAP ASE 数据库中使用压缩的更多详细信息，请查看[SAP 支持说明 1750510](https://launchpad.support.sap.com/#/notes/1750510)。 有关 SAP ASE 数据库压缩的更多详细信息，[请参阅 sap 支持说明 #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure 上的 SAP ASE 的高可用性 
HADR 用户指南详细介绍了2个节点 SAP ASE "始终打开" 解决方案的设置和配置。  此外，还支持第三个灾难恢复节点。 SAP ASE 支持许多高可用配置，包括共享磁盘和本地操作系统群集（浮动 IP）。 Azure 上唯一受支持的配置是使用不带浮动 IP 的故障管理器。  浮动 IP 地址方法在 Azure 上不起作用。  SAP 内核是 "HA 感知" 应用程序，并了解主要和辅助 SAP ASE 服务器。 SAP ASE 和 Azure 之间没有密切的集成，不使用 Azure 内部负载均衡器。 因此，应遵循[SAP ASE HADR 用户指南](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)中的标准 sap ase 文档 

> [!NOTE]
> Azure 上唯一受支持的配置是使用不带浮动 IP 的故障管理器。  浮动 IP 地址方法在 Azure 上不起作用。 

### <a name="third-node-for-disaster-recovery"></a>用于灾难恢复的第三个节点
除了使用 SAP ASE Always On 来实现本地高可用性，你可能需要将配置扩展到另一个 Azure 区域中的异步复制的节点。 可在[此处](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)找到此类方案的文档。

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE 数据库加密 & SSL 
SAP 软件预配管理器（SWPM）提供一个选项，用于在安装期间对数据库进行加密。  如果要使用加密，则建议使用 SAP 完全数据库加密。  请参阅中记录的详细信息：

- [SAP 支持说明 #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP 支持说明 #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP 支持说明 #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP 支持说明 #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> 如果 SAP ASE 数据库已加密，则备份转储压缩将不起作用。 另请参阅[SAP 支持说明 #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure 上的 SAP ASE 部署清单
 
- 部署 SAP ASE 16.3 PL7 或更高版本
- 更新为 FaultManager 和 Saphostagent 以及的最新版本和修补程序
- 在可用的最新认证 OS （如 Windows 2019、Suse 15.1 或 Redhat 7.6 或更高版本）上部署
- 使用 SAP 认证的 Vm –建议使用高内存 Azure VM Sku，如 Es_v3 或适用于 x-大型系统 M 系列 VM Sku
- 将 VM 的磁盘 IOPS 和总 VM 聚合吞吐量配额与磁盘设计相匹配。  部署足够数量的磁盘
- 使用具有正确条带大小和文件系统的 Windows 存储空间或 Linux LVM2 聚合磁盘
- 为数据、日志、临时和备份目的创建足够数量的设备
- 考虑对 x 大系统使用 UltraDisk 
- 在 Linux 操作系统上运行 `saptune` SAP-ASE 
- 通过数据库加密保护数据库–手动存储密钥 Azure Key Vault 
- 完成[Azure 上的 SAP 清单](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist) 
- 配置日志备份和完整备份 
- 测试 HA/DR、备份和还原，并 & 批量测试 
- 确认自动数据库扩展正在工作 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>使用 DBACockpit 监视数据库实例
对于使用 SAP ASE 作为数据库平台的 SAP 系统，可以将 DBACockpit 作为事务 DBACockpit 中的嵌入式浏览器窗口或作为 Webdynpro 来访问。 但是，只在 DBACockpit 的 Webdynpro 实现中提供了用于监视和管理数据库的全部功能。

与本地系统一样，需执行数个步骤，才能启用 DBACockpit 的 Webdynpro 实现所使用的所有 SAP NetWeaver 功能。 按照[SAP 支持说明 #1245200](https://launchpad.support.sap.com/#/notes/1245200) ，启用 webdynpro 并生成所需的应用。 按照上述说明中的说明进行操作时，还将配置 Internet 通信管理器（`ICM`）以及用于 http 和 https 连接的端口。 http 的默认设置如下所示：

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

事务 DBACockpit 中生成的链接如下所示：

> https：\//\<fullyqualifiedhostname >： 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http：\//\<fullyqualifiedhostname >： 8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

需要确保 ICM 使用完全限定的主机名，并且此名称可在打开 DBACockpit 的计算机上解析，具体取决于如何将托管 SAP 系统的 Azure 虚拟机连接到 AD 和 DNS。 请参阅[SAP 支持说明 #773830](https://launchpad.support.sap.com/#/notes/773830)以了解 ICM 如何根据配置文件参数确定完全限定的主机名，并在必要时显式设置参数 ICM/host_name_full。

如果在仅限云的方案中部署 VM，而在本地与 Azure 之间没有跨界连接，则需要定义一个公共 IP 地址和一个 `domainlabel`。 VM 的公共 DNS 名称格式如下所示：

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

有关 DNS 名称的更多详细信息，请参阅 [此处] [azurerm-virtual-machines-azurerm-versus-azuresm]。

将 SAP 配置文件参数 icm/host_name_full 设置为 Azure VM 的 DNS 名称，其链接可能如下所示：

> https：\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http：\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

在此情况下，需要确保：

* 在 Azure 门户中，针对用来与 ICM 通信的 TCP/IP 端口，向网络安全组添加入站规则
* 针对用来与 ICM 通信的 TCP/IP 端口，向 Windows 防火墙配置添加入站规则

针对自动导入的所有可用修正，建议定期应用适用于 SAP 版本的修正集合 SAP 说明：

* [SAP 支持说明 #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP 支持说明 #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP 支持说明 #1882376](https://launchpad.support.sap.com/#/notes/1882376)

可以在以下 SAP 说明中找到有关 SAP ASE 的 DBA Cockpit 的更多详细信息：

* [SAP 支持说明 #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP 支持说明 #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP 支持说明 #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP 支持说明 #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP 支持说明 #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP 支持说明 #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP 支持说明 #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP 支持说明 #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>适用于 SAP ASE 的有用链接、说明 & 白皮书
" [SYBASE ASE 16.3 PL7](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US)的起始页" 文档提供了以下各文档的链接：

- SAP ASE 学习旅程-管理 & 监视
- SAP ASE 学习旅程-安装 & 升级

很有用。 另一个有用的文档是 sap[应用程序，适用于 Sap 自适应服务器企业迁移和运行时的最佳做法](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)。

其他有用的 SAP 支持说明包括：

- [SAP 支持说明 #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP 支持说明 #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP 支持说明 #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP 支持说明 #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP 支持说明 #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP 支持说明 #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP 支持说明 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP 支持说明 #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP 支持说明 #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP 支持说明 #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP 支持说明 #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP 支持说明 #1588316](https://launchpad.support.sap.com/#/notes/158831) 

其他信息发布于 

- [SAP 自适应服务器企业版上的 SAP 应用程序](https://community.sap.com/topics/applications-on-ase)
- [Sybase 信息中心](http://infocenter.sybase.com/help/index.jsp) 

每月新闻稿通过[SAP 支持说明发布 #2381575](https://launchpad.support.sap.com/#/notes/2381575) 

[具有第三个 DR 节点设置的 Sybase ASE Always on](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199) 

## <a name="next-steps"></a>后续步骤
查看[Azure 上的 SAP 工作负荷一文：规划和部署清单](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)

