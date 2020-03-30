---
title: 在 Azure SAP HANA（大型实例）上进行 HANA 备份和还原 | Microsoft Docs
description: 如何在 Azure SAP HANA（大型实例）上执行 HANA 备份和还原
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430073"
---
# <a name="backup-and-restore"></a>备份和还原

>[!IMPORTANT]
>本文不能替代 SAP HANA 管理文档或 SAP 说明。 我们希望您在 SAP HANA 管理和操作方面有扎实的了解和专业知识，尤其是在备份、恢复、高可用性和灾难恢复方面。 在本文中，将显示 SAP HANA 工作室的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

重要的是使用自己的 HANA 版本或发布，在相应的环境中执行步骤和过程。 本文中描述的某些过程被简化，以便更好的一般理解。 它们不应用作最终操作手册的详细步骤。 如果要为配置创建操作手册，请测试和练习流程，并记录与您的特定配置相关的流程。 

操作数据库最重要的一个方面是保护它们免受灾难性事件的影响。 这些事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。

备份数据库，并能够将其还原到任何时间点（例如，在某人删除关键数据之前），可以还原到尽可能接近中断前的状态。

必须执行两种类型的备份才能实现还原功能：

- 数据库备份：完整备份、增量备份或差异备份
- 事务日志备份

除了在应用程序级别执行完整数据库备份以外，还可以使用存储快照执行备份。 存储快照不会替换事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 存储快照可以通过快速提供数据库的滚动映像来加快恢复。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- **自制 (DIY)。** 确保磁盘空间足够后，请使用以下磁盘备份方法之一执行完整的数据库并记录备份。 您可以直接备份到附加到 HANA 大型实例单元的卷，也可以备份到在 Azure 虚拟机 （VM） 中设置的 NFS 共享。 在后一种情况中，客户在 Azure 中设置 Linux VM、将 Azure 存储附加到 VM 并通过该 VM 中配置的 NFS 服务器共享此存储。 如果针对直接附加到 HANA 大型实例单元的卷执行备份，请将备份复制到 Azure 存储帐户。 在设置导出基于 Azure 存储的 NFS 共享的 Azure VM 后执行此操作。 您还可以使用 Azure 备份保管库或 Azure 冷存储。 

   另一个选项是使用第三方数据保护工具在备份复制到 Azure 存储帐户后存储备份。 对于出于合规性和审核目的需要存储更长时间的数据，可能还需要 DIY 备份选项。 在所有情况下，备份均复制到通过 VM 和 Azure 存储表示的 NFS 共享中。

- **基础结构备份和还原功能。** 您还可以使用 Azure（大型实例）上 SAP HANA 的基础基础结构提供的备份和恢复功能。 此选项满足备份和快速还原的需要。 本部分的余下内容介绍 HANA 大型实例提供的备份和还原功能。 本节还介绍备份和恢复与 HANA 大型实例提供的灾难恢复功能的关系。

> [!NOTE]
>   HANA 大型实例的基础基础结构使用的快照技术依赖于 SAP HANA 快照。 此时，SAP HANA 快照与 SAP HANA 多租户数据库容器的多个租户不配合使用。 如果只部署了一个租户，SAP HANA 快照可以正常工作，您可以使用此方法。

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>在 Azure（大型实例）上使用 SAP HANA 的存储快照

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 当通过 /hana/数据和 /hana/shared（包括 /usr/sap）卷触发快照时，快照技术在运行存储快照之前启动 SAP HANA 快照。 在恢复存储快照后，此 SAP HANA 快照是最终日志还原的设置点。 要成功使用 HANA 快照，需要一个活动的 HANA 实例。 在 HSR 方案中，当前无法执行 HANA 快照的辅助节点不支持存储快照。
- 存储快照成功运行后，SAP HANA 快照将被删除。
- 事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来创建快照。 在这种情况下，您不需要运行 HANA 快照。
- 如果必须将数据库还原到特定时间点，则对于生产中断，请请求 Azure 上的 Microsoft Azure 支持或 AZURE 上的 SAP HANA 还原到特定存储快照。 一个例子就是按计划将沙盒系统还原到其原始状态。
- 存储快照中包含的 SAP HANA 快照是应用在拍摄存储快照后运行和存储的事务日志备份的偏移点。
- 创建这些事务日志备份是为了将数据库还原到某个时间点。

您可以执行针对三类卷的存储快照：

- /hana/数据和 /hana/共享的组合快照，包括 /usr/sap。 此快照需要创建 SAP HANA 快照，为存储快照进行准备工作。 从存储的角度来看，SAP HANA 快照可确保数据库处于一致状态。 对于还原过程，这是要设置的点。
- 基于 /hana/logbackups 创建单独的快照。
- 操作系统分区。

要获取最新的快照脚本和文档，请参阅[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 当您从[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)下载快照脚本包时，您将获得三个文件。 其中一个文件记录在 PDF 中，用于提供的功能。 下载工具集后，请按照"获取快照工具"中的说明进行操作。

## <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照占用分配给 HANA 大型实例单元的存储空间。 请考虑计划存储快照的以下方面以及要保留的存储快照数。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 拍摄时的特定存储快照占用的存储空间很少。
- 随着数据内容的变化和 SAP HANA 数据文件中的内容在存储卷上的变化，快照需要存储原始块内容和数据更改。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 执行这些卷的快照会消耗卷空间。 你需要：

- 确定何时计划存储快照。
- 监视存储卷的空间消耗。 
- 管理存储的快照数。 

将大量数据导入 HANA 数据库或对其执行其他重大更改时，可以禁用存储快照。 


以下各节提供了执行这些快照的信息，并包括一般建议：

- 尽管硬件可以维持每个卷 255 个快照，但您希望保持在远低于此数字。 建议为 250 或更少。
- 执行存储快照之前，请监视并跟踪可用空间。
- 根据可用空间减少存储快照数量。 可以减少保留的快照数量，或者扩展卷。 可以 1 TB 为单位订购附加存储。
- 在使用 SAP 平台迁移工具 (R3load) 将数据移入 SAP HANA，或者从备份还原 SAP HANA 数据库或执行其他活动期间，可以禁用 /hana/data 卷上的存储快照。 
- 在 SAP HANA 表进行大型重组期间，尽可能避免存储快照。
- 存储快照是利用 Azure 上的 SAP HANA（大型实例）灾难恢复功能的先决条件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服务存储快照的先决条件

要确保快照脚本成功运行，请确保 Perl 安装在 HANA 大型实例服务器上的 Linux 操作系统上。 Perl 预安装在您的 HANA 大型实例单元上。 若要检查 Perl 版本，请使用以下命令：

`perl -v`

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>设置存储快照

要使用 HANA 大型实例设置存储快照，请按照以下步骤操作。
1. 确保在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。
1. 修改 /etc/ssh/ssh\_config，添加代码行 _MACs hmac-sha1_。
1. 在主节点上为运行的每个 SAP HANA 实例创建 SAP HANA 备份用户帐户（如果适用）。
1. 在所有 SAP HANA 大型实例服务器上安装 SAP HANA HDB 客户端。
1. 在每个区域的第一台 SAP HANA 大型实例服务器上，创建一个公钥用于访问对快照创建操作进行控制的底层存储基础结构。
1. 将脚本和配置文件从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 复制到 SAP HANA 安装中的 **hdbsql** 位置。
1. 根据需要修改*HANABackupDetails.txt*文件，以符合相应的客户规格。

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 获取最新的快照脚本和文档。 有关前面列出的步骤，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 方案的注意事项
如果在一个 HANA 大型实例单元上运行具有多个 SAP HANA 实例的[MCOD 方案](https://launchpad.support.sap.com/#/notes/1681092)，则为每个 SAP HANA 实例预配了单独的存储卷。 有关 MDC 和其他注意事项的详细信息，请参阅在 Azure 上的 SAP [HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"要记住的重要事项"。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步骤 1：安装 SAP HANA HDB 客户端

在 Azure（大型实例）上安装在 SAP HANA 上的 Linux 操作系统包括运行 SAP HANA 存储快照以用于备份和灾难恢复所必需的文件夹和脚本。 检查[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)中的更多最新版本。 

在安装 SAP HANA 时，您有责任在 HANA 大型实例单元上安装 SAP HANA HDB 客户端。

### <a name="step-2-change-the-etcsshssh_config"></a>步骤 2：更改 /etc/ssh/ssh\_config

此步骤在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"启用与存储的通信"中进行了介绍。


### <a name="step-3-create-a-public-key"></a>步骤 3：创建公钥

要启用对 HANA 大型实例租户的存储快照接口的访问，请通过公钥建立登录过程。 

在租户中的 Azure（大型实例）服务器上的第一个 SAP HANA 上，创建公钥以访问存储基础结构。 使用公钥时，无需密码即可登录到存储快照接口。 您也不需要使用公钥维护密码凭据。 

要生成公钥，请参阅在 Azure 上的 SAP [HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"启用与存储的通信"。


### <a name="step-4-create-an-sap-hana-user-account"></a>步骤 4：创建 SAP HANA 用户帐户

要开始创建 SAP HANA 快照，请在 SAP HANA 中创建存储快照脚本可以使用的用户帐户。 在 SAP HANA Studio 中创建用于此目的的 SAP HANA 用户帐户。 用户必须在 SYSTEMDB 下创建，*而不是*在 MDC 的 SID 数据库下创建。 在单个容器环境中，用户是在租户数据库中创建的。 此帐户必须具有**备份管理员**和**目录读取**权限。 

要设置和使用用户帐户，请参阅[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"启用与 SAP HANA 的通信"。


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步骤 5：为 SAP HANA 用户帐户授权

在此步骤中，您可以授权您创建的 SAP HANA 用户帐户，以便脚本在运行时无需提交密码。 SAP HANA`hdbuserstore`命令支持创建 SAP HANA 用户密钥。 密钥存储在一个或多个 SAP HANA 节点上。 用户可以使用用户密钥从脚本流程内部访问 SAP HANA，而无需管理密码。 本文稍后会介绍脚本过程。

>[!IMPORTANT]
>使用快照命令运行的用户上下文运行这些配置命令。 否则，快照命令将无法正常工作。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步骤 6：获取快照脚本、配置快照及测试配置和连接

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) 下载最新版本的脚本。 脚本的安装方式随着脚本版本 4.1 而改变。 有关详细信息，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"启用与 SAP HANA 的通信"。

有关命令的确切顺序，请参阅[在 Azure 上为 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"轻松安装快照工具（默认）"。 我们建议使用默认安装。 

要从版本 3.x 升级到 4.1，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"升级现有安装"。 要卸载 4.1 工具集，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"卸载快照工具"。

不要忘记在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中运行"快照工具的完整设置"中描述的步骤。

安装时的不同脚本和文件的目的在"这些快照工具是什么？ 在[Azure 上的 SAP HANA 的 Microsoft 快照工具中](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

在配置快照工具之前，请确保也正确配置了 HANA 备份位置和设置。 有关详细信息，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"SAP HANA 配置"。

快照工具集的配置在[Azure 上的 SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)的 Microsoft 快照工具中的"配置文件 - HANABackupCustomerdetails.txt"中进行了描述。

#### <a name="test-connectivity-with-sap-hana"></a>使用 SAP HANA 测试连接

将所有配置数据填入 *HANABackupCustomerDetails.txt* 文件后，需要检查 HANA 实例数据的相关配置是否正确。 使用脚本独立于 SAP HANA 纵向扩展或横向扩展配置的脚本 `testHANAConnection`。

有关详细信息，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"检查与 SAP HANA - testHANA 连接的连接"。

#### <a name="test-storage-connectivity"></a>测试存储连接

下一个测试步骤是根据您放入*HANABackupCustomerDetails.txt*配置文件中的数据检查与存储的连接。 然后运行测试快照。 在运行该`azure_hana_backup`命令之前，必须运行此测试。 有关此测试的命令序列，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"检查与存储 - 测试存储快照连接的连接"。

成功登录到存储虚拟机接口后，脚本会继续执行阶段 2 并创建测试快照。 此处显示了 SAP HANA 的三节点横向扩展配置的输出。

如果测试快照使用脚本成功运行，则可以计划实际存储快照。 如果不成功，请先调查问题，然后再前进。 测试快照应保留至第一批实际快照完成为止。


### <a name="step-7-perform-snapshots"></a>步骤 7：执行快照

完成准备步骤后，可以开始配置和安排实际存储快照。 要计划的脚本会使用 SAP HANA 纵向扩展和横向扩展配置。 备份脚本的定期和常规执行应使用 cron 实用工具来计划。 

有关确切的命令语法和功能，请参阅[在 Azure 上为 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"执行快照备份 - azure_hana_backup"。 

当脚本`azure_hana_backup`运行时，它会在以下三个阶段创建存储快照：

1. 它运行 SAP HANA 快照。
1. 它运行存储快照。
1. 它删除在存储快照运行之前创建的 SAP HANA 快照。

要运行脚本，请从复制该脚本的 HDB 可执行文件夹调用它。 

保留期使用运行脚本时作为参数提交的快照数进行管理。 存储快照涵盖的时间量是执行期间的函数，以及脚本运行时作为参数提交的快照数的函数。 

如果保留的快照数超过脚本调用中指定为参数的快照数，则在新快照运行之前将删除同一标签的最旧存储快照。 作为最后一个调用参数指定的数字是可用于控制保留的快照数的数字。 使用此数字，您还可以间接控制用于快照的磁盘空间。 


## <a name="snapshot-strategies"></a>快照策略
不同类型快照的频率取决于是否使用 HANA 大型实例灾难恢复功能。 此功能依赖于存储快照，这可能需要实施某些与存储快照频率和执行时间长短相关的特殊建议。 

以下注意事项和建议假设不** 使用 HANA 大型实例提供的灾难恢复功能。 假设用户使用存储快照来获取备份并提供过去 30 天的时点恢复。 鉴于快照和空间的数量限制，请考虑以下要求：

- 时间点恢复的恢复时间。
- 使用的空间。
- 潜在灾难恢复的恢复点和恢复时间目标。
- 针对磁盘最终执行的 HANA 完整数据库备份。 每当针对磁盘执行完整数据库备份或执行 **backint** 接口时，存储快照的执行会失败。 如果计划在存储快照上运行完整数据库备份，请确保在此期间禁用存储快照的执行。
- 每个卷的快照数，限制为 250。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

如果不使用 HANA 大型实例的灾难恢复功能，快照周期将不太频繁。 在这种情况下，在 12 小时或 24 小时期间对 /hana/数据和 /hana/shared 执行组合快照，其中包括 /usr/sap。 将快照保留一个月。 日志备份卷的快照也是如此。 针对日志备份卷执行 SAP HANA 事务日志备份会在 5 分钟到 15 分钟的时间内进行。

最好是使用 cron 执行计划的存储快照。 对所有备份和灾难恢复需求使用相同的脚本。 修改脚本输入，使之与请求的不同备份时间匹配。 这些快照都以不同的方式在 cron 中计划，具体取决于它们的执行时间。 它可以每小时、每 12 小时、每天或每周一次。 

下面的示例在 /etc/crontab 中显示 cron 计划：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
在前面的示例中，每小时组合的快照涵盖包含 /hana/数据和 /hana/shared/SID 的卷，其中包括 /usr/sap 的位置。 使用此类型的快照快速实现过去两天内的时点恢复。 这些卷上还有每日快照。 因此，按每小时快照和每日快照进行四周的覆盖，您有两天的覆盖范围。 事务日志备份卷也每天备份。 这些备份将保留四周。 

正如您在 crontab 的第三行中看到的，HANA 事务日志的备份计划每 5 分钟运行一次。 运行存储快照的不同 cron 作业的开始时间是交错的。 这样，快照不会在特定时间点同时运行。 

在下面的示例中，您将执行一个组合快照，该快照涵盖了包含 /hana/数据和 /hana/shared/SID 的卷，其中包括每小时的位置/ usr/sap。 这些快照会保留两天。 事务日志备份卷的快照在 5 分钟的基础上运行，并保留 4 小时。 与以前一样，HANA 事务日志文件的备份计划每 5 分钟运行一次。 

启动事务日志备份后，执行事务日志备份卷的快照时会存在 2 分钟的延迟。 在正常情况下，SAP HANA 事务日志备份将在这 2 分钟内完成。 与之前一样，包含启动 LUN 的卷通过存储快照每天备份一次，且会保留四周。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下图说明了上一个示例的序列。 引导 LUN 被排除。

![备份和快照之间的关系](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 对 /hana/log 卷执行常规写入，将提交的更改记录到数据库。 SAP HANA 将保存点定期写入 /hana/data 卷。 如在 crontab 中指定，SAP HANA 事务日志备份每 5 分钟运行一次。 

您还看到，由于在 /hana/数据和 /hana/shared/SID 卷上触发组合存储快照，SAP HANA 快照每小时运行一次。 HANA 快照成功后，组合存储快照将运行。 按照 crontab 中的指示，/hana/logbackup 卷上的存储快照在 HANA 事务日志备份后大约 2 分钟运行一次。

> 

>[!IMPORTANT]
> 仅当与 SAP HANA 事务日志备份配合执行存储快照时，才能针对 SAP HANA 备份使用存储快照。 这些事务日志备份需要能够涵盖执行存储快照的间隔时间段。 

如果向用户承诺时间点恢复目标为 30 天，则需要：

- 在极端情况下，通过 /hana/数据和 /hana/shared/SID 访问 30 天的旧存储快照。 
- 创建保留时长长于任何两个组合存储快照间隔时间的连续事务日志备份。 因此事务日志备份卷快照的最长保留时长需为 30 天。 如果将事务日志备份复制到位于 Azure 存储上的另一个 NFS 共享，则情况并非如此。 在这种情况下，可以从该 NFS 共享拉取旧事务日志备份。

要受益于存储快照和事务日志备份的最终存储复制，请更改 SAP HANA 将事务日志备份写入的位置。 可在 HANA Studio 中进行此更改。 

尽管 SAP HANA 会自动备份完整的日志段，但指定日志备份间隔是确定性的。 当您使用灾难恢复选项时尤其如此，因为您通常希望运行具有确定性周期的日志备份。 在以下情况下，15 分钟设置为日志备份间隔。

![在 SAP HANA Studio 中计划 SAP HANA 备份日志](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您还可以选择比每 15 分钟更频繁的备份。 较高的频率设置通常与 HANA 大型实例的灾难恢复功能结合使用。 某些客户每 5 分钟执行一次事务日志备份。

如果从未备份过数据库，则最后一步是执行基于文件的数据库备份，以创建必须在备份目录中存在的单个备份项。 否则，SAP HANA 无法启动指定的日志备份。

![创建基于文件的备份以创建单个备份项](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


首次成功运行存储快照后，请删除步骤 6 中运行的测试快照。 有关详细信息，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"删除测试快照 - 删除测试存储快照"。 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>监视磁盘卷上的快照数量和大小

在特定的存储卷上，可以监视快照的数量以及这些快照的存储消耗量。 `ls` 命令不会显示快照目录或文件。 Linux OS`du`命令显示有关这些存储快照的详细信息，因为它们存储在相同的卷上。 将该命令与以下选项一起使用：

- `du –sh .snapshot`：此选项提供快照目录中所有快照的总数。
- `du –sh --max-depth=1`：此选项列出 **.snapshot** 文件夹中保存的所有快照，以及每个快照的大小。
- `du –hc`：此选项提供所有快照占用的总大小。

使用这些命令可确保拍摄和存储的快照不会占用卷上的所有存储。

>[!NOTE]
>引导 LUN 的快照与前面的命令不可见。

### <a name="get-details-of-snapshots"></a>获取快照的详细信息
要获取有关快照的更多详细信息，请使用 脚本`azure_hana_snapshot_details`。 如果灾难恢复位置中有一个活动服务器，则可以在任一位置运行此脚本。 此脚本根据每个包含快照的卷提供以下细分输出： 
   * 一个卷中全部快照的大小
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）

有关命令和输出的语法，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"列表快照 - azure_hana_snapshot_details"。 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>减少服务器上的快照数

如前所述，可以减少存储的某些快照标签的数量。 用于启动快照的命令的最后两个参数是要保留的快照的标签与数量。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在前面的示例中，快照标签是**每日哈纳**。 要保留此标签的快照数为**28**。 对磁盘空间消耗量做出响应时，可能想要减少存储的快照数。 例如，将快照数减少到 15 的一种简单方法是运行最后一个参数设置为**15**的脚本：

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果使用此设置运行脚本，则包含新存储快照的快照数为 15。 会保存最新的 15 个快照，同时删除 15 个较旧的快照。

 >[!NOTE]
 > 仅当快照超过一小时时，此脚本才会减少快照数。 脚本不会删除不到一小时的快照。 这些限制与提供的可选灾难恢复功能相关。

如果不再希望在语法示例中维护具有每日**哈纳**备份前缀的一组快照，则运行**0**作为保留编号的脚本。 然后删除与该标签匹配的所有快照。 删除所有快照可能会影响 HANA 大型实例灾难恢复功能的功能。

第二种删除特定快照的方法是使用脚本 `azure_hana_snapshot_delete`。 此脚本旨在使用在 HANA Studio 中找到的 HANA 备份 ID 或通过快照名称本身删除快照或一组快照。 目前，备份 ID 仅绑定到为**hana**快照类型创建的快照。 类型**日志**和**引导**的快照备份不执行 SAP HANA 快照，因此找不到这些快照的备份 ID。 如果输入快照名称，将在不同卷上查找所有与输入的快照名称匹配的快照。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

有关该脚本的详细信息，请参阅[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"删除快照 - azure_hana_snapshot_delete"。

将脚本运行为用户**根**。

>[!IMPORTANT]
>如果仅在计划删除的快照上存在数据，则在删除快照后，该数据将永久丢失。


## <a name="file-level-restore-from-a-storage-snapshot"></a>从存储快照实现文件级还原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
对于 **hana** 和 **logs** 快照类型，可以直接在 **.snapshot** 目录中的卷上访问快照。 每个快照都有一个子目录。 将每个文件以快照点时的状态从该子目录复制到实际的目录结构中。 

在当前版本的脚本中，*没有*为快照还原提供还原脚本作为自助服务。 快照还原可以在故障转移期间作为灾难恢复站点的自助服务灾难恢复脚本的一部分执行。 要从现有可用快照还原所需的快照，必须通过打开服务请求与 Microsoft 操作团队联系。

>[!NOTE]
>单个文件还原适用于独立于 HANA 大型实例单元类型的引导 LUN 快照。 **.snapshot**目录未在引导 LUN 中公开。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>恢复到最近的 HANA 快照

在生产中断方案中，可以从存储快照中恢复的过程可以作为 Microsoft Azure 支持的客户事件启动。 如果在生产系统中删除了数据，则这是一个高度紧迫的问题，检索数据的唯一方法是还原生产数据库。

在不同的情况下，时间点恢复可能不太紧急，因此可以提前几天规划。 您可以使用 Azure 上的 SAP HANA 计划此恢复，而不是升起高优先级标志。 例如，您可能计划通过应用新的增强包来升级 SAP 软件。 然后又需要还原到升级增强包之前的快照状态。

发送请求之前，需要做好准备。 然后，Azure 团队的 SAP HANA 可以处理请求并提供还原的卷。 接下来，需要基于快照还原 HANA 数据库。

有关使用新工具集还原快照的可能性，请参阅[Azure 上的 SAP HANA 手册恢复指南中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"如何还原快照"。

要准备请求，请按照以下步骤操作。

1. 确定要还原的快照。 除非另有说明，否则只能还原 hana/data 卷。 

1. 关闭 HANA 实例。

   ![关闭 HANA 实例](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 卸载每个 HANA 数据库节点上的数据卷。 如果仍将数据卷载入操作系统，快照还原会失败。

   ![卸载每个 HANA 数据库节点上的数据卷](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 打开 Azure 支持请求，并包含有关恢复特定快照的说明：

   - 在还原期间：Azure 服务上的 SAP HANA 可能会要求您参加电话会议，以协调、验证和确认是否还原了正确的存储快照。 

   - 恢复后：Azure 服务上的 SAP HANA 在还原存储快照时通知您。

1. 完成还原过程后，请重新装载所有数据卷。

   ![重新装载所有数据卷](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



另一种可能性，例如，从存储快照恢复的 SAP HANA 数据文件，在 Azure 上的 SAP HANA 手册中的步骤 7[中记录了从存储快照](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中恢复的数据文件。

要从快照备份进行还原，请参阅[从存储快照在 Azure 上使用 SAP HANA 的手动恢复指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 

>[!Note]
>如果您的快照已由 Microsoft 操作还原，则无需执行步骤 7。


### <a name="recover-to-another-point-in-time"></a>恢复到另一个时间点
要还原到特定时间点，请参阅[从存储快照在 Azure 上的 SAP HANA 的手动恢复指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"将数据库恢复到以下时间点"。 


## <a name="next-steps"></a>后续步骤
- 请参阅[灾难恢复原则和准备](hana-concept-preparation.md)。
