---
title: 使用 Azure Site Recovery 执行 Azure 到 Azure 的灾难恢复体系结构
description: 概述了使用 Azure Site Recovery 服务为 Azure VM 设置 Azure 区域之间的灾难恢复时使用的体系结构。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: raynew
ms.openlocfilehash: a9468f437a89a85f28b6ce869b948ca2a4aff7bf
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983323"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Azure 到 Azure 的灾难恢复体系结构


本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务为 Azure 虚拟机 (VM) 部署灾难恢复时所用的体系结构、组件和过程。 使用灾难恢复设置，Azure VM 可以持续复制到不同的目标区域。 如果发生服务中断，可将 VM 故障转移到次要区域，然后在次要区域中对其进行访问。 一切恢复正常后，可以执行故障回复，继续在主要位置操作。



## <a name="architectural-components"></a>体系结构组件

下表汇总了 Azure VM 灾难恢复所涉及的组件。

**组件** | **要求**
--- | ---
**源区域中的 VM** | [受支持源区域](azure-to-azure-support-matrix.md#region-support)中的一个或多个 Azure VM。<br/><br/> VM 可以运行任何[受支持的操作系统](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)。
**源 VM 存储** | 可以管理 Azure VM；它们还可以包含分散在不同存储帐户之间的非托管磁盘。<br/><br/>[了解](azure-to-azure-support-matrix.md#replicated-machines---storage)支持的 Azure 存储。
**源 VM 网络** | VM 可以位于源区域中虚拟网络 (VNet) 上的一个或多个子网内。 [详细了解](azure-to-azure-support-matrix.md#replicated-machines---networking)网络要求。
**缓存存储帐户** | 源网络中需要一个缓存存储帐户。 在复制期间，VM 更改将存储在缓存中，然后再发送到目标存储。  缓存存储帐户必须是标准存储帐户。<br/><br/> 使用缓存可确保尽量减少对 VM 上运行的生产应用程序造成的影响。<br/><br/> [详细了解](azure-to-azure-support-matrix.md#cache-storage)缓存存储要求。 
**目标资源** | 在复制期间以及发生故障转移时将使用目标资源。 Site Recovery 默认可以设置目标资源，你也可以自行创建/自定义目标资源。<br/><br/> 在目标区域中，请检查是否能够创建 VM，以及你的订阅是否有足够的资源用于支持目标区域中所需的 VM 大小。 

![源和目标复制](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

## <a name="target-resources"></a>目标资源

为 VM 启用复制时，Site Recovery 将提供用于自动创建目标资源的选项。 

**目标资源** | **默认设置**
--- | ---
**目标订阅** | 与源订阅相同。
目标资源组  | VM 在故障转移后所属的资源组。<br/><br/> 该组可以位于除源区域以外的其他任何 Azure 区域。<br/><br/> Site Recovery 将在目标区域中创建一个带有“asr”后缀的新资源组。<br/><br/>
**目标 VNet** | 复制的 VM 在故障转移后所处的虚拟网络 (VNet)。 创建源虚拟网络与目标虚拟网络之间的网络映射，反之亦然。<br/><br/> Site Recovery 将创建带有“asr”后缀的新 VNet 和子网。
**目标存储帐户** |  如果 VM 不使用托管磁盘，则会将数据复制到此存储帐户。<br/><br/> Site Recovery 将在目标区域中创建新的存储帐户，以镜像源存储帐户。
**副本托管磁盘** | 如果 VM 使用托管磁盘，则会将数据复制到此副本托管磁盘。<br/><br/> Site Recovery 将在存储区域中创建副本托管磁盘用于镜像源。
目标可用性集  |  复制的 VM 在故障转移后所处的可用性集。<br/><br/> 对于源位置中某个可用性集内的 VM，Site Recovery 将在目标区域中创建一个带有“asr”后缀的可用性集。 如果存在某个可用性集，则会使用该可用性集，而不会新建。
**目标可用性区域** | 如果目标区域支持可用性区域，Site Recovery 将分配源区域中所用的相同区域编号。

### <a name="managing-target-resources"></a>管理目标资源

可按如下所述管理目标资源：

- 启用复制时可以修改目标设置。
- 已开始复制后可以修改目标设置。 请注意，目标区域 VM 的默认 SKU 与源 VM 的 SKU （或与源 VM SKU 比较的下一个最佳可用 SKU）相同。 类似于其他资源，例如目标资源组、目标名称等，还可以在复制过程中更新目标区域 VM SKU。 无法更新的资源是可用性类型（单实例、集或区域）。 若要更改此设置，需要禁用复制、修改设置，然后重新启用复制。 


## <a name="replication-policy"></a>复制策略 

启用 Azure VM 复制时，Site Recovery 默认会使用下表中汇总的默认设置创建新的复制策略。

**策略设置** | **详细信息** | **默认**
--- | --- | ---
**恢复点保留期** | 指定 Site Recovery 保留恢复点的时间长短 | 24 小时
**应用一致性快照频率** | Site Recovery 创建应用一致性快照的频率。 | 每 4 小时

### <a name="managing-replication-policies"></a>管理复制策略

可按如下所述管理和修改默认的复制策略设置：
- 启用复制时可以修改设置。
- 随时可以创建复制策略，并在启用复制时应用该策略。

### <a name="multi-vm-consistency"></a>多 VM 一致性

如果希望 VM 一同复制，并在故障转移时获得共享的崩溃一致性和应用一致性恢复点，可将这些 VM 集中到一个复制组中。 多 VM 一致性会影响工作负荷的性能。仅当 VM 运行的工作负荷需要在所有计算机之间保持一致时，才应该对这些 VM 使用此功能。 



## <a name="snapshots-and-recovery-points"></a>快照和恢复点

恢复点是基于在特定时间点生成的 VM 磁盘快照创建的。 故障转移 VM 时，可以使用恢复点来还原目标位置中的 VM。

故障转移时，我们通常想要确保 VM 在不发生任何数据损坏或丢失的情况下启动，并且 VM 数据可在操作系统以及 VM 上运行的应用中保持一致。 这取决于创建的快照类型。

Site Recovery 按如下所述创建快照：

1. 默认情况下，Site Recovery 创建崩溃一致性数据快照；如果指定了频率，则创建应用一致性快照。
2. 恢复点是基于快照创建的，根据复制策略中的保留期设置进行存储。

### <a name="consistency"></a>一致性

下表解释了不同的一致性类型。

### <a name="crash-consistent"></a>崩溃一致性

**说明** | **详细信息** | 建议 
--- | --- | ---
崩溃一致性快照捕获创建快照时磁盘上的数据。 它不包括内存中的任何数据。<br/><br/> 崩溃一致性快照包含在 VM 发生崩溃或者在创建快照的那一刻从服务器上拔下电源线时，磁盘上的等量数据。<br/><br/> 崩溃一致性不能保证操作系统或 VM 上的应用中的数据一致性。 | 默认情况下，Site Recovery 每隔五分钟创建崩溃一致性恢复点。 此设置不可修改。<br/><br/>  | 目前，大多数应用都可以从崩溃一致性恢复点正常恢复。<br/><br/> 对于操作系统以及 DHCP 服务器和打印服务器等应用而言，崩溃一致性恢复点通常已足够。

### <a name="app-consistent"></a>应用一致性

**说明** | **详细信息** | 建议 
--- | --- | ---
应用一致性恢复点是基于应用一致性快照创建的。<br/><br/> 应用一致性快照包含崩溃一致性快照中的所有信息，此外加上内存中的数据，以及正在进行的事务中的数据。 | 应用一致性快照使用卷影复制服务 (VSS)：<br/><br/>   1) 启动快照时，VSS 会在卷上执行写入时复制 (COW) 操作。<br/><br/>   2) 执行 COW 之前，VSS 会告知计算机上的每个应用它需要将内存中的数据刷新到磁盘。<br/><br/>   3) 然后，VSS 允许备份/灾难恢复应用（在本例中为 Site Recovery）读取快照数据并继续处理。 | 应用一致性快照是按指定的频率创建的。 此频率始终应小于为保留恢复点设置的频率。 例如，如果使用默认设置 24 小时保留恢复点，则应将频率设置为小于 24 小时。<br/><br/>应用一致性快照比崩溃一致性快照更复杂，且完成时间更长。<br/><br/> 应用一致性快照会影响已启用复制的 VM 上运行的应用的性能。 

## <a name="replication-process"></a>复制过程

为 Azure VM 启用复制时，会发生以下情况：

1. 自动在 VM 上安装 Site Recovery 移动服务扩展。
2. 该扩展将 VM 注册到 Site Recovery。
3. 开始 VM 的持续复制。  磁盘写入内容立即传输到源位置中的缓存存储帐户。
4. Site Recovery 处理缓存中的数据，并将其发送到目标存储帐户或副本托管磁盘。
5. 处理数据后，每隔五分钟生成崩溃一致性恢复点。 根据复制策略中指定的设置生成应用一致性恢复点。

![启用复制过程，步骤 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

**复制过程**

## <a name="connectivity-requirements"></a>连接要求

 复制的 Azure VM 需要出站连接。 Site Recovery 不必与 VM 建立入站连接。 

### <a name="outbound-connectivity-urls"></a>出站连接 (URL)

如果使用 URL 控制 VM 的出站访问，请允许这些 URL。

| **URL** | **详细信息** |
| ------- | ----------- |
| \* .blob.core.windows.net | 允许将数据从 VM 写入源区域中的缓存存储帐户。 |
| login.microsoftonline.com | 向 Site Recovery 服务 URL 提供授权和身份验证。 |
| *.hypervrecoverymanager.windowsazure.com | 允许 VM 与 Site Recovery 服务进行通信。 |
| *.servicebus.windows.net | 允许 VM 写入 Site Recovery 监视和诊断数据。 |
| *.vault.azure.net | 允许访问，以便通过门户为支持 ADE 的虚拟机启用复制
| *.automation.ext.azure.com | 允许通过门户为复制项启用移动代理自动升级

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 地址范围的出站连接

若要使用 IP 地址控制 VM 的出站连接，请允许这些地址。
请注意，可以在[网络白皮书](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)中找到网络连接要求的详细信息 

#### <a name="source-region-rules"></a>源区域规则

**规则** |  **详细信息** | **服务标记**
--- | --- | --- 
允许 HTTPS 出站通信：端口 443 | 允许对应于源区域中存储帐户的范围 | 储存.\<区域名称>
允许 HTTPS 出站通信：端口 443 | 允许对应于 Azure Active Directory (Azure AD) 的范围  | AzureActiveDirectory
允许 HTTPS 出站通信：端口 443 | 允许与目标区域中的事件中心对应的范围。 | EventsHub.\<区域名称>
允许 HTTPS 出站通信：端口 443 | 允许与 Azure Site Recovery 相对应的范围  | AzureSiteRecovery
允许 HTTPS 出站通信：端口 443 | 允许与 Azure Key Vault 对应的范围（仅在通过门户为支持 ADE 的虚拟机启用复制时才需要这样做） | AzureKeyVault
允许 HTTPS 出站通信：端口 443 | 允许与 Azure 自动化控制器对应的范围（仅当通过门户为复制的项启用移动代理自动升级时才需要） | GuestAndHybridManagement

#### <a name="target-region-rules"></a>目标区域规则

**规则** |  **详细信息** | **服务标记**
--- | --- | --- 
允许 HTTPS 出站通信：端口 443 | 允许与目标区域中的存储帐户相对应的范围 | 储存.\<区域名称>
允许 HTTPS 出站通信：端口 443 | 允许对应于 Azure AD 的范围  | AzureActiveDirectory
允许 HTTPS 出站通信：端口 443 | 允许与源区域中的事件中心对应的范围。 | EventsHub.\<区域名称>
允许 HTTPS 出站通信：端口 443 | 允许与 Azure Site Recovery 相对应的范围  | AzureSiteRecovery
允许 HTTPS 出站通信：端口 443 | 允许与 Azure Key Vault 对应的范围（仅在通过门户为支持 ADE 的虚拟机启用复制时才需要这样做） | AzureKeyVault
允许 HTTPS 出站通信：端口 443 | 允许与 Azure 自动化控制器对应的范围（仅当通过门户为复制的项启用移动代理自动升级时才需要） | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>使用 NSG 规则控制访问

如果使用 [NSG 规则](https://docs.microsoft.com/azure/virtual-network/security-overview)通过筛选传入和传出 Azure 网络/子网的网络流量来控制 VM 连接，请注意以下要求：

- 源 Azure 区域的 NSG 规则应允许复制流量进行出站访问。
- 我们建议先在测试环境中创建规则，然后在生产环境中实施这些规则。
- 使用[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，而不要允许单个 IP 地址。
    - 服务标记表示集合在一起的一组 IP 地址前缀，可以最大程度地降低安全规则创建过程的复杂性。
    - Microsoft 会不断地自动更新服务标记。 
 
详细了解 Site Recovery 的[出站连接](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)，以及如何[使用 NSG 控制连接](concepts-network-security-group-with-site-recovery.md)。


### <a name="connectivity-for-multi-vm-consistency"></a>多 VM 一致性的连接

如果启用了多 VM 一致性，则复制组中的计算机将通过端口 20004 相互通信。
- 确保防火墙设备没有阻止 VM 之间通过端口 20004 进行的内部通信。
- 如果想要 Linux VM 成为复制组的一部分，请确保按照特定 Linux 版本的指南手动打开端口 20004 上的出站流量。




## <a name="failover-process"></a>故障转移过程

如果启动故障转移，系统会在目标资源组、目标虚拟网络、目标子网和目标可用性集中创建 VM。 可在故障转移过程中使用任意恢复点。

![故障转移过程](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>后续步骤

将 Azure VM [快速复制](azure-to-azure-quickstart.md)到次要区域。
