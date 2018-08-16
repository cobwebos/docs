---
title: Microsoft Azure StorSimple 虚拟阵列概述 | Microsoft 文档
description: 介绍 StorSimple 虚拟阵列，它是一个集成式存储解决方案，用于管理本地虚拟阵列与 Microsoft Azure 云存储之间的存储任务。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2018
ms.author: alkohli
ms.openlocfilehash: 78ed53e5e2f5d04943e6c32ddfedf037cb9e1f73
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480948"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>StorSimple 虚拟阵列简介

## <a name="overview"></a>概述

Microsoft Azure StorSimple 虚拟阵列是一个集成式存储解决方案，用于管理虚拟机监控程序中运行的本地虚拟阵列与 Microsoft Azure 云存储之间的存储任务。 虚拟阵列是一种经济高效、易于管理的文件服务器或 iSCSI 服务器解决方案，可以消除与企业存储和数据保护相关的很多问题和开支。 Virtual Array 特别适用于存储不常访问的存档数据。

本文概要介绍了 Virtual Array - 下面的部分其他资源：

* 有关最佳实践，请参阅 [StorSimple 虚拟阵列最佳实践](storsimple-ova-best-practices.md)。
* 有关 StorSimple 8000 系列设备的概述，请转到 [StorSimple 8000 系列：混合云解决方案](storsimple-overview.md)。
* 有关 StorSimple 5000/7000 系列设备的信息，请转到 [StorSimple 联机帮助](http://onlinehelp.storsimple.com/)。

虚拟阵列支持 iSCSI 或服务器消息块 (SMB) 协议。 它在现有虚拟机监控程序基础结构上运行，并提供到云、云备份、快速还原、项目级恢复和灾难恢复功能的分层。

下表总结了 StorSimple 虚拟阵列的重要功能。

| 功能 | StorSimple 虚拟阵列 |
| --- | --- |
| 安装要求 |使用虚拟化基础结构（HYPER-V 或 VMware） |
| 可用性 |单节点 |
| 总容量（包括云） |每个虚拟阵列最多 64 TB 可用容量 |
| 本地容量 |每个虚拟阵列 390 GB 到 6.4 TB 可用容量（需要预配 500 GB 到 8 TB 的磁盘空间） |
| 本机协议 |iSCSI 或 SMB |
| 恢复时间目标 (RTO) |iSCSI：无论大小，小于 2 分钟 |
| 恢复点目标 (RPO) |每日备份和按需备份 |
| 存储分层 |使用热映射确定哪些数据应向内或向外分层 |
| 支持 |供应商支持的虚拟化基础结构 |
| 性能 |因底层基础结构而异 |
| 数据移动性 |可还原到相同设备或执行项目级恢复（文件服务器） |
| 存储层 |本地虚拟机监控程序存储和云 |
| 共享大小 |分层：最多 20 TB；本地固定：最多 2 TB |
| 卷大小 |分层：500 GB 到 5 TB；本地固定：50 GB 到 200 GB <br> 分层卷的本地预留空间不超过 200 GB。 |
| 快照 |崩溃一致 |
| 项目级恢复 |是；用户可从共享还原 |

## <a name="why-use-storsimple"></a>为什么使用 StorSimple？

StorSimple 在几分钟内将用户和服务器连接到 Azure 存储，无需应用程序修改。

下表介绍了 StorSimple 虚拟阵列解决方案提供的某些主要优势。

| 功能 | 优势 |
| --- | --- |
| 透明集成 |虚拟阵列支持 iSCSI 或 SMB 协议。 本地层和云层之间的数据移动是无缝的，并且对用户透明。 |
| 降低存储成本 |借助 StorSimple，预配足够的本地存储来满足对最常使用的热门数据的当前需求。 随着存储需求的增长，StorSimple 将冷数据分层到经济高效的云存储中。 在发送到云之前对数据进行重复数据删除和压缩，以进一步降低存储要求和开支。 |
| 简化存储管理 |StorSimple 提供云中的集中式管理，使用 StorSimple 设备管理器管理多台设备。 |
| 改进灾难恢复和合规性 |StorSimple 通过立即还原元数据和按需还原数据促进更快的灾难恢复。 这意味着正常运行能够得以继续，而很少发生中断。 |
| 数据移动性 |可出于恢复和迁移目的从其他站点访问分层到云的数据。 请注意，只能将数据还原到原始虚拟阵列。 但是，使用灾难恢复功能可将整个虚拟阵列还原到另一个虚拟阵列。 |

## <a name="storsimple-workload-summary"></a>StorSimple 工作负荷摘要

下面以表格形式列出了受支持的 StorSimple 工作负荷的摘要。

|场景     |工作负载     |支持      |限制               |
|-------------|-------------|---------------|---------------------------|
|远程办公室/分支机构 (ROBO)  |文件共享     |是      |请参阅[文件服务器的最大限制](storsimple-ova-limits.md)。<br></br>请参阅[受支持的 SMB 版本的系统要求](storsimple-ova-system-requirements.md)。| 所有版本     |
|云存档  |存档文件共享     |是      |请参阅[文件服务器的最大限制](storsimple-ova-limits.md)。<br></br>请参阅[受支持的 SMB 版本的系统要求](storsimple-ova-system-requirements.md)。| 所有版本     |

StorSimple Virtual Array 最适用于不常访问的数据。 虽然 Virtual Array 拥有可提高性能的本地缓存，但用户应假定设备服务文件位于最低存储层（云）。 每个 Virtual Array 可以大约 100 Mbps 的速度写入和读取 Azure 存储。 该链接在发送到设备的所有请求之间共享，可能会限制性能（如下图所示）。

![云存档](./media/storsimple-ova-overview/cloud-archiving.png)

多名并发用户访问 Virtual Array 时均共享到 Azure 的连接，这导致性能降低。 无法保证每位用户的性能，且设备在接收请求时单独处理各个请求。

StorSimple Virtual Array 不适用于需要高可用性的工作负载。 Virtual Array 是单节点设备，会在安装软件更新时出现停机。 管理员应每年计划 3 到 4 次 30 分钟的维护时段。

## <a name="workflows"></a>工作流

StorSimple 虚拟阵列尤其适合以下工作流：

* [基于云的存储管理](#cloud-based-storage-management)
* [与位置无关的备份](#location-independent-backup)
* [数据保护和灾难恢复](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>基于云的存储管理
可使用在 Azure 门户中运行的 StorSimple Device Manager 服务管理存储在多台设备上和多个位置的数据。 这对分布式分支方案尤其有用。 请注意，必须创建 StorSimple Device Manager 服务的单独实例来管理虚拟阵列和物理 StorSimple 设备。 另请注意，虚拟阵列现在使用新的 Azure 门户（而不是 Azure 经典门户）。

![基于云的存储管理](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>与位置无关的备份
借助虚拟阵列，云快照提供卷或共享的与位置无关的时间点副本。 云快照默认处于启用状态，并且无法禁用。 所有卷和共享通过单个每日备份策略同时备份，并且可在必要时创建其他临时备份。

### <a name="data-protection-and-disaster-recovery"></a>数据保护和灾难恢复
虚拟阵列支持以下数据保护和灾难恢复方案：

* **卷或共享存储** - 使用还原作为恢复卷或共享的新工作流。 使用此方法恢复整个卷或共享。
* **项目级恢复** - 共享允许对最近备份的简化访问。 可从云中提供的特殊 *.backup* 文件夹轻松恢复单个文件。 此还原功能是用户驱动的，不需要任何管理干预。
* **灾难恢复** - 使用故障转移功能可将所有卷或共享恢复到新的虚拟阵列。 创建新的虚拟阵列并向 StorSimple Device Manager 服务注册它，然后故障转移原始虚拟阵列。 然后，新的虚拟阵列会采用预配的资源。

## <a name="storsimple-virtual-array-components"></a>StorSimple 虚拟阵列组件

虚拟阵列包括以下组件：

* [Virtual Array](#virtual-array) - 一种混合云存储设备，基于在虚拟化环境或虚拟机监控程序中预配的虚拟机。
* [StorSimple 设备管理器服务](#storsimple-device-manager-service) - Azure 门户的扩展，可用于通过能从不同地理位置访问的单个 Web 界面管理一台或多台 StorSimple 设备。 可使用 StorSimple Device Manager 服务创建和管理服务、查看和管理设备和警报以及管理卷、共享和现有快照。
* [本地 Web 用户界面](#local-web-user-interface) - 基于 Web 的 UI，可用于配置设备，使其连接到本地网络，再向 StorSimple 设备管理器服务注册该设备。 
* [命令行接口](#command-line-interface) - Windows PowerShell 接口，可用于在 Virtual Array 上启动支持会话。
  以下各节更详细地介绍了其中的每个组件，并解释该解决方案如何排列数据、分配存储并促进存储管理和数据保护。

### <a name="virtual-array"></a>虚拟阵列

虚拟阵列是单节点存储解决方案，可提供主存储、管理与云存储的通信并帮助确保存储在设备上的所有数据的安全性和机密性。

虚拟阵列在可供下载的单个模型中可用。 虚拟阵列在设备上具有 6.4 TB 的最大容量（基础存储要求为 8 TB），包括云储存的最大容量为 64 TB。

虚拟阵列具有以下功能：

* 它经济高效。 它利用现有虚拟化基础结构，并且可部署在现有 Hyper-V 或 VMware 虚拟机监控程序上。
* 它驻留在数据中心中，并且可配置为 iSCSI 服务器或文件服务器。
* 它与云集成。
* 备份存储在云中，这可以促进更快的灾难恢复并简化项目级恢复 (ILR)。
* 可将更新应用到虚拟阵列，就像将它们应用到物理设备一样。

> [!NOTE]
> 虚拟阵列无法扩展。 因此，在创建虚拟阵列时必须预配足够的存储。

### <a name="storsimple-device-manager-service"></a>StorSimple Device Manager 服务

Microsoft Azure StorSimple 提供一个基于 Web 的用户界面（StorSimple Device Manager 服务），可用于集中管理 StorSimple 存储。 可以使用 StorSimple Device Manager 服务执行以下任务：

* 从单个服务管理多个 StorSimple 虚拟阵列。
* 配置和管理 StorSimple 虚拟阵列的安全设置。 （云中的加密依赖于 Microsoft Azure API。）
* 配置存储帐户凭据和属性。
* 配置和管理卷或共享。
* 备份和还原卷或共享上的数据。
* 监视性能。
* 查看系统设置并确定可能的问题。

使用 StorSimple Device Manager 服务执行虚拟阵列的日常管理。

有关详细信息，请转到[使用 StorSimple Device Manager 服务管理 StorSimple 设备](storsimple-virtual-array-manager-service-administration.md)。

### <a name="local-web-user-interface"></a>本地 Web 用户界面

虚拟阵列包含基于 Web 的 UI，用于向 StorSimple Device Manager 服务一次性配置和注册设备。 可使用它关闭并重新启动虚拟阵列、运行诊断测试、更新软件、更改设备管理员密码、查看系统日志并联系 Microsoft 支持部门以提交服务请求。

有关使用基于 Web 的 UI 的信息，请转到[使用基于 Web 的 UI 管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md)。

### <a name="command-line-interface"></a>命令行接口

所包含的 Windows PowerShell 接口允许启动与 Microsoft 支持部门的会话，以便他们可以帮助排查和解决可能在虚拟阵列上遇到的问题。

## <a name="storage-management-technologies"></a>存储管理技术

除了虚拟阵列和其他组件，StorSimple 解决方案还使用以下软件技术提供对重要数据的快速访问、降低存储消耗并保护存储在虚拟阵列上的数据：

* [自动存储分层](#automatic-storage-tiering) 
* [本地固定的共享和卷](#locally-pinned-shares-and-volumes)
* [分层或备份到云的数据的重复数据删除和压缩](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [计划和按需备份](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>自动存储分层
虚拟阵列使用新的分层机制管理虚拟阵列和云上存储的数据。 只有两个层：本地虚拟阵列和 Azure 云存储。 StorSimple 虚拟阵列根据热映射将数据自动排列为层，热映射跟踪当前使用情况、年限和与其他数据的关系。 使用最频繁（最热）的数据存储在本地，不太频繁使用的数据和非活动数据则自动迁移到云中。 （所有备份都存储在云中。）StorSimple 会随着使用模式的变化调整和重新排列数据及存储分配。 例如，随着时间的推移，一些信息可能不太频繁使用。 随着数据的使用频率逐渐减少，将其向外分层到云。 如果同一数据再次变得频繁使用，将其向内分层到存储阵列。

特定分层共享或卷的数据保证拥有自己的本地层空间（大约为该共享或卷的总预配空间的 10%）。 尽管这降低了该共享或卷所在的虚拟阵列上的可用存储，但它确保了单个共享或卷的分层不会受到其他共享或卷的分层需求的影响。 因此一个共享或卷上的非常繁忙的工作负荷无法将所有其他工作负荷强制施加到云。

为 iSCSI 创建的分层卷拥有 200 GB 的最大本地预留空间（无论卷大小如何）。

![自动存储分层](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> 可将卷指定为本地固定，在此情况下数据保留在虚拟阵列上，并且永远不分层到云。 有关详细信息，请转到[本地固定共享和卷](#locally-pinned-shares-and-volumes)。


### <a name="locally-pinned-shares-and-volumes"></a>本地固定的共享和卷

可将相应的共享和卷创建为本地固定。 此功能确保关键应用程序所需的数据保留在虚拟阵列中，并且永远不分层到云。 本地固定的共享和卷具有以下功能：

* 它们不受云延迟或连接问题影响。
* 它们仍受益于 StorSimple 云备份和灾难恢复功能。

可将本地固定共享或卷还原为分层，或者将分层共享或卷还原为本地固定。 

有关本地固定卷的详细信息，请转到[使用 StorSimple Device Manager 服务管理卷](storsimple-virtual-array-manage-volumes.md)。

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>分层或备份到云的数据的重复数据删除和压缩

StorSimple 使用删除重复和数据压缩功能进一步减少云中的存储需求。 删除重复通过消除存储数据集中的冗余来减少总体存储的数据量。 当信息更改时，StorSimple 忽略未更改的数据并且仅捕获更改。 此外，StorSimple 还标识并删除重复信息，从而减少存储的数据量。

> [!NOTE]
> 存储在虚拟阵列上的数据不进行重复数据删除或压缩。 所有重复数据删除和压缩都发生在将数据发送到云之前。

### <a name="scheduled-and-on-demand-backups"></a>计划和按需备份

StorSimple 数据保护功能支持创建按需备份。 此外，默认备份计划确保每天备份数据。 备份采用增量快照的形式创建，这些快照存储在云中。 快照仅记录最后一次备份以来的更改，可快速创建和还原。 在灾难恢复方案中，这些快照可能非常重要，因为它们替换辅助存储系统（如磁带备份），并且允许将数据还原到数据中心或其他站点上（如有必要）。

## <a name="managing-personal-information"></a>管理个人信息

虚拟系列的 StorSimple 设备管理器收集两个密钥实例中的个人信息：
 - 提醒用户设置，可在其中配置用户电子邮件地址。 此信息可以由管理员清除。 
 - 可访问驻留在共享上的数据的用户。 会显示可访问共享数据的用户列表，并可供导出。 删除共享时也会删除此列表。

有关详细信息，请[在信任中心查看 Microsoft 隐私策略](https://www.microsoft.com/trustcenter)。

## <a name="next-steps"></a>后续步骤

了解如何[准备虚拟阵列门户](storsimple-virtual-array-deploy1-portal-prep.md)。
