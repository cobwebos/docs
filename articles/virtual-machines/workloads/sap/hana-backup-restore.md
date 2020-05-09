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
ms.openlocfilehash: 9cb2647cc5ac2dc60f5ae7327ee39ed2ff625193
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901377"
---
# <a name="backup-and-restore"></a>备份和还原

>[!IMPORTANT]
>本文不是 SAP HANA 管理文档或 SAP 说明的替代。 我们期望您对 SAP HANA 管理和操作的专业技能有深刻的了解，尤其是在备份、还原、高可用性和灾难恢复方面。 本文显示 SAP HANA Studio 中的屏幕截图。 SAP 管理工具屏幕的内容、结构和性质以及工具本身可能会因不同的 SAP HANA 发行版本而有所差异。

重要的是使用自己的 HANA 版本或发布，在相应的环境中执行步骤和过程。 本文中所述的某些过程经过简化，可以更好地了解。 它们不会用作最终操作手册的详细步骤。 如果要为配置创建操作手册，请测试和执行进程，并记录与特定配置相关的过程。 

操作数据库最重要的一个方面是保护它们免受灾难性事件的作用。 这些事件可能由多种因素导致，包括自然灾难、简单的人为失误，等等。

备份数据库时，可以将数据库还原到任何时间点（例如，在某人删除关键数据之前），这样就可以还原到尽可能接近中断之前的状态。

要实现还原功能，必须执行两种类型的备份：

- 数据库备份：完整备份、增量备份或差异备份
- 事务日志备份

除了在应用程序级别执行完整数据库备份以外，还可以使用存储快照执行备份。 存储快照不会替代事务日志备份。 事务日志备份对于将数据库还原到某个时间点或从已提交的事务中清空日志而言十分重要。 存储快照可以通过快速提供数据库的前滚映像加快恢复速度。 

Azure 上的 SAP HANA（大型实例）提供两个备份和还原选项：

- **自制 (DIY)。** 确保有足够的磁盘空间后，可以使用以下磁盘备份方法之一执行完整的数据库和日志备份。 你可以直接备份到附加到 HANA 大型实例单元的卷，或者备份到 Azure 虚拟机（VM）中设置的 NFS 共享。 在后一种情况中，客户在 Azure 中设置 Linux VM、将 Azure 存储附加到 VM 并通过该 VM 中配置的 NFS 服务器共享此存储。 如果对直接附加到 HANA 大型实例单元的卷执行备份，请将备份复制到 Azure 存储帐户。 设置用于导出基于 Azure 存储的 NFS 共享的 Azure VM 后，请执行此操作。 你还可以使用 Azure 备份保管库或 Azure 冷存储。 

   另一种选择是在将备份复制到 Azure 存储帐户后，使用第三方数据保护工具来存储它们。 对于出于合规性和审核目的而需要长期存储的数据，也可能需要 DIY 备份选项。 在所有情况下，备份均复制到通过 VM 和 Azure 存储表示的 NFS 共享中。

- **基础结构备份和还原功能。** 你还可以使用 Azure 上 SAP HANA 基础结构（大型实例）提供的备份和还原功能。 此选项满足备份和快速还原的需要。 本部分的余下内容介绍 HANA 大型实例提供的备份和还原功能。 本部分还介绍了备份和还原所需的与 HANA 大型实例提供的灾难恢复功能之间的关系。

> [!NOTE]
>   HANA 大型实例的底层基础结构使用的快照技术依赖于 SAP HANA 快照。 此时，SAP HANA 快照不能与 SAP HANA 多租户数据库容器的多个租户配合工作。 如果只部署一个租户，SAP HANA 快照即可工作，你可以使用此方法。

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>在 Azure 上使用 SAP HANA 的存储快照（大型实例）

Azure 上的 SAP HANA（大型实例）的底层存储基础结构支持卷的存储快照。 支持备份和还原卷，不过需要注意以下事项：

- 它不是创建完整的数据库备份，而是频繁创建存储卷的快照。
- 当通过/hana/data 和/hana/shared （包括/usr/sap）触发快照时，快照技术会在运行存储快照之前启动 SAP HANA 快照。 在恢复存储快照后，此 SAP HANA 快照是最终日志还原的设置点。 若要成功使用 HANA 快照，需要一个活动 HANA 实例。 在 HSR 方案中，不支持在无法执行 HANA 快照的当前辅助节点上存储快照。
- 成功运行存储快照之后，会删除 SAP HANA 快照。
- 事务日志备份需频繁创建，并存储在 /hana/logbackups 卷或 Azure 中。 可以单独触发包含事务日志备份的 /hana/logbackups 卷来创建快照。 在这种情况下，不需要运行 HANA 快照。
- 如果必须将数据库还原到某个特定的时间点，则在发生生产中断时，请求 Microsoft Azure 支持或 SAP HANA 在 Azure 上还原到特定的存储快照。 一个例子就是按计划将沙盒系统还原到其原始状态。
- 存储快照中包含的 SAP HANA 快照是一个偏移点，用于应用在拍摄存储快照之后运行并存储的事务日志备份。
- 创建这些事务日志备份是为了将数据库还原到某个时间点。

可以执行面向三个卷类的存储快照：

- /Hana/data 和/hana/shared 的组合快照，其中包括/usr/sap 此快照需要创建 SAP HANA 快照，为存储快照进行准备工作。 SAP HANA 快照确保从存储角度来看，数据库处于一致的状态。 对于还原过程，这是设置的点。
- 基于 /hana/logbackups 创建单独的快照。
- 操作系统分区。

若要获取最新的快照脚本和文档，请参阅[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 从[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)下载快照脚本包时，会获得三个文件。 其中一项文件在 PDF 中记录为提供的功能。 下载工具集后，请按照 "获取快照工具" 中的说明进行操作。

## <a name="storage-snapshot-considerations"></a>存储快照注意事项

>[!NOTE]
>存储快照使用分配给 HANA 大型实例单元的存储空间。 请考虑计划存储快照的以下方面以及要保留的存储快照数。 

Azure 上的 SAP HANA（大型实例）的存储快照的特定机制包括：

- 使用存储空间时，特定的存储快照位于时间点。
- 随着数据内容的更改和存储卷上 SAP HANA 数据文件中的内容更改，快照需要存储原始块内容并且数据发生更改。
- 因此，存储快照的大小会不断增长。 快照保留的时间越长，存储快照就会变得越大。
- 在存储快照的生存期内对 SAP HANA 数据库卷所做的更改越多，存储快照消耗的空间也就越大。

Azure 上的 SAP HANA（大型实例）为 SAP HANA 数据卷和日志卷使用固定的卷大小。 执行这些卷的快照会消耗卷空间。 你需要：

- 确定何时计划存储快照。
- 监视存储卷的空间消耗。 
- 管理存储的快照数。 

将大量数据导入 HANA 数据库或对其执行其他重大更改时，可以禁用存储快照。 


以下各节提供了有关执行这些快照的信息，并包括常规建议：

- 尽管硬件可以保留每个卷的255快照，但你希望保持在此数字以下。 建议为250或更低。
- 执行存储快照之前，请监视并跟踪可用空间。
- 根据可用空间减少存储快照数量。 可以减少保留的快照数量，或者扩展卷。 可以 1 TB 为单位订购附加存储。
- 在使用 SAP 平台迁移工具 (R3load) 将数据移入 SAP HANA，或者从备份还原 SAP HANA 数据库或执行其他活动期间，可以禁用 /hana/data 卷上的存储快照。 
- 在较大的 SAP HANA 表的重组期间，应尽可能避免存储快照。
- 存储快照是利用 Azure 上的 SAP HANA（大型实例）灾难恢复功能的先决条件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服务存储快照的先决条件

若要确保快照脚本成功运行，请确保在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。 Perl 预安装在 HANA 大型实例单元上。 若要检查 Perl 版本，请使用以下命令：

`perl -v`

![运行此命令复制公钥](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>设置存储快照

若要设置 HANA 大型实例的存储快照，请执行以下步骤。
1. 确保在 HANA 大型实例服务器上的 Linux 操作系统中安装 Perl。
1. 修改 /etc/ssh/ssh\_config，添加代码行 _MACs hmac-sha1_。
1. 在主节点上为运行的每个 SAP HANA 实例创建 SAP HANA 备份用户帐户（如果适用）。
1. 在所有 SAP HANA 大型实例服务器上安装 SAP HANA HDB 客户端。
1. 在每个区域的第一台 SAP HANA 大型实例服务器上，创建一个公钥用于访问对快照创建操作进行控制的底层存储基础结构。
1. 将脚本和配置文件从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 复制到 SAP HANA 安装中的 **hdbsql** 位置。
1. 根据需要修改*hanabackupdetails.txt*文件，以获取适当的客户规范。

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 获取最新的快照脚本和文档。 对于前面列出的步骤，请参阅[适用于 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 方案的注意事项
如果对一个 HANA 大型实例单元运行具有多个 SAP HANA 实例的[MCOD 方案](https://launchpad.support.sap.com/#/notes/1681092)，则为每个 SAP HANA 实例预配单独的存储卷。 有关 MDC 和其他注意事项的详细信息，请参阅[适用于 Azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"要记住的重要事项"。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步骤 1：安装 SAP HANA HDB 客户端

在 Azure 上的 SAP HANA 上安装的 Linux 操作系统（大型实例）包含出于备份和灾难恢复目的运行 SAP HANA 存储快照所需的文件夹和脚本。 查看[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)中的最新版本。 

安装 SAP HANA 时，你有责任在 HANA 大型实例单元上安装 SAP HANA HDB 客户端。

### <a name="step-2-change-the-etcsshssh_config"></a>步骤 2：更改 /etc/ssh/ssh\_config

在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中，在 "通过存储启用通信" 中介绍了此步骤。


### <a name="step-3-create-a-public-key"></a>步骤 3：创建公钥

若要允许访问 HANA 大型实例租户的存储快照接口，请通过公钥建立登录过程。 

在租户中的第一个 SAP HANA Azure （大型实例）服务器上，创建一个公钥用于访问存储基础结构。 使用公钥时，无需密码即可登录到存储快照接口。 你也无需使用公钥维护密码凭据。 

若要生成公钥，请参阅[Azure 上用于 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"启用与存储的通信"。


### <a name="step-4-create-an-sap-hana-user-account"></a>步骤 4：创建 SAP HANA 用户帐户

若要开始创建 SAP HANA 快照，请在存储快照脚本可以使用的 SAP HANA 中创建用户帐户。 在 SAP HANA Studio 中创建用于此目的的 SAP HANA 用户帐户。 用户必须在 SYSTEMDB 下创建，而*不*是在适用于 MDC 的 SID 数据库下创建。 在单容器环境中，用户是在租户数据库中创建的。 此帐户必须具有 "**备份管理员**" 和 "**目录读取**" 权限。 

若要设置和使用用户帐户，请参阅[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "启用与 SAP HANA 的通信"。


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步骤 5：为 SAP HANA 用户帐户授权

在此步骤中，你将为你创建的 SAP HANA 用户帐户授权，以便脚本无需在运行时提交密码。 SAP HANA 命令`hdbuserstore`启用 SAP HANA 用户密钥的创建。 密钥存储在一个或多个 SAP HANA 节点上。 用户可以使用用户密钥从脚本流程内部访问 SAP HANA，而无需管理密码。 本文稍后会介绍脚本过程。

>[!IMPORTANT]
>在运行快照命令的用户上下文中运行这些配置命令。 否则，snapshot 命令将无法正常运行。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步骤 6：获取快照脚本、配置快照及测试配置和连接

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 下载最新版本的脚本。 脚本的安装方式随版本4.1 的脚本而更改。 有关详细信息，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"启用与 SAP HANA 的通信"。

有关命令的确切顺序，请参阅在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "轻松安装快照工具（默认）"。 建议使用默认安装。 

若要从版本3.x 升级到4.1，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"升级现有安装"。 若要卸载4.1 工具集，请参阅[Azure 上用于 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "卸载快照工具"。

请不要忘记运行在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "完成快照工具的安装" 中所述的步骤。

不同脚本和文件的安装方式在 "这些快照工具的用途是什么？" 中进行了介绍。 在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中。

在配置快照工具之前，请确保还正确配置了 HANA 备份位置和设置。 有关详细信息，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"SAP HANA 配置"。

[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "配置文件-hanabackupcustomerdetails.txt .txt" 中介绍了快照工具集的配置。

#### <a name="test-connectivity-with-sap-hana"></a>测试与 SAP HANA 的连接

将所有配置数据填入 *HANABackupCustomerDetails.txt* 文件后，需要检查 HANA 实例数据的相关配置是否正确。 使用脚本独立于 SAP HANA 纵向扩展或横向扩展配置的脚本 `testHANAConnection`。

有关详细信息，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "检查与 SAP HANA 的连接"。

#### <a name="test-storage-connectivity"></a>测试存储连接

下一测试步骤是基于放入*hanabackupcustomerdetails.txt*配置文件的数据检查与存储的连接。 然后运行测试快照。 在运行该`azure_hana_backup`命令之前，必须先运行此测试。 有关此测试的命令序列，请参阅[Azure 上用于 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "检查与存储的连接-testStorageSnapshotConnection"。

成功登录到存储虚拟机接口后，脚本会继续执行阶段 2 并创建测试快照。 此处显示了 SAP HANA 的三节点横向扩展配置的输出。

如果测试快照已成功运行脚本，则可以计划实际的存储快照。 如果未成功，请先调查问题，然后再继续。 测试快照应保留至第一批实际快照完成为止。


### <a name="step-7-perform-snapshots"></a>步骤 7：执行快照

完成准备步骤后，你可以开始配置和计划实际的存储快照。 要计划的脚本会使用 SAP HANA 纵向扩展和横向扩展配置。 备份脚本的定期和常规执行应使用 cron 实用工具来计划。 

有关确切的命令语法和功能，请参阅[azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "执行快照备份-azure_hana_backup"。 

脚本`azure_hana_backup`运行时，它会在以下三个阶段创建存储快照：

1. 它运行 SAP HANA 快照。
1. 它运行存储快照。
1. 它会删除在运行存储快照之前创建的 SAP HANA 快照。

若要运行该脚本，请从复制到的 HDB 可执行文件文件夹调用该脚本。 

在运行脚本时，将使用作为参数提交的快照数来管理保持期。 存储快照所涵盖的时间是执行期间的一个函数，以及在运行脚本时作为参数提交的快照数。 

如果保留的快照数超过了脚本调用中指定为参数的数目，则在运行新的快照之前，将删除相同标签的最早存储快照。 作为最后一个调用参数指定的数字是可用于控制保留的快照数的数字。 使用此数字，还可以间接控制用于快照的磁盘空间。 


## <a name="snapshot-strategies"></a>快照策略
不同类型快照的频率取决于是否使用 HANA 大型实例灾难恢复功能。 此功能依赖于存储快照，这可能需要实施某些与存储快照频率和执行时间长短相关的特殊建议。 

以下注意事项和建议假设不** 使用 HANA 大型实例提供的灾难恢复功能。 假设用户使用存储快照来获取备份并提供过去 30 天的时点恢复。 考虑到快照和空间的限制，请考虑以下要求：

- 时间点恢复的恢复时间。
- 使用的空间。
- 潜在灾难恢复的恢复点和恢复时间目标。
- 针对磁盘最终执行的 HANA 完整数据库备份。 每当针对磁盘执行完整数据库备份或执行 **backint** 接口时，存储快照的执行会失败。 如果计划在存储快照之上运行完整数据库备份，请确保在此期间已禁用存储快照的执行。
- 每个卷的快照数限制为250。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

如果不使用 HANA 大型实例的灾难恢复功能，快照周期将不太频繁。 在这种情况下，请在/hana/data 和/hana/shared 上执行合并的快照，其中包括/usr/sap，以12小时或24小时为周期。 将快照保留一个月。 对于日志备份卷的快照也是如此。 对日志备份卷执行 SAP HANA 事务日志备份的时间是5分钟到15分钟的时间段。

最好是使用 cron 执行计划的存储快照。 使用同一个脚本来满足所有备份和灾难恢复需求。 修改脚本输入，使之与请求的不同备份时间匹配。 这些快照在 cron 中按不同的执行时间进行了不同的计划。 可以是每小时、每12小时、每天或每周。 

以下示例显示了/etc/crontab 中的 cron 计划：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
在上面的示例中，每小时组合快照包含包含/hana/data 和/hana/shared/SID （包括/usr/sap）位置的卷。 使用此类型的快照快速实现过去两天内的时点恢复。 在这些卷上还有一个每日快照。 因此，你有两天的覆盖面，每小时拍摄快照，每日快照四周。 事务日志备份卷也每天备份一次。 这些备份将保留四周。 

正如在 crontab 第三行中看到的那样，HANA 事务日志的备份计划为每5分钟运行一次。 运行存储快照的不同 cron 作业的启动时间是错开的。 这样，便不会在某个时间点同时运行所有快照。 

在下面的示例中，你将执行一个合并快照，该快照涵盖包含/hana/data 和/hana/shared/SID 的卷，其中包含每小时的/usr/sap。 这些快照会保留两天。 事务日志备份卷的快照每5分钟运行一次，并保留四个小时。 与之前一样，HANA 事务日志文件的备份计划为每5分钟运行一次。 

启动事务日志备份后，执行事务日志备份卷的快照时会存在 2 分钟的延迟。 正常情况下，SAP HANA 事务日志备份在这两分钟内完成。 与之前一样，包含启动 LUN 的卷通过存储快照每天备份一次，且会保留四周。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下图说明了上一示例的序列。 排除启动 LUN。

![备份和快照之间的关系](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 对 /hana/log 卷执行常规写入，将提交的更改记录到数据库。 SAP HANA 将保存点定期写入 /hana/data 卷。 如 crontab 中所指定，SAP HANA 事务日志备份每5分钟运行一次。 

还会看到，SAP HANA 快照每小时运行一次，这是因为在/hana/data 和/hana/shared/SID 卷上触发了组合存储快照。 HANA 快照成功后，将运行组合存储快照。 如 crontab 中所述，/hana/logbackup 卷上的存储快照每5分钟运行一次，在 HANA 事务日志备份后大约2分钟运行一次。

> 

>[!IMPORTANT]
> 仅当与 SAP HANA 事务日志备份配合执行存储快照时，才能针对 SAP HANA 备份使用存储快照。 这些事务日志备份需要能够涵盖执行存储快照的间隔时间段。 

如果向用户承诺时间点恢复目标为 30 天，则需要：

- 在极端情况下，通过/hana/data 和/hana/shared/SID 访问已合并的存储快照。 
- 创建保留时长长于任何两个组合存储快照间隔时间的连续事务日志备份。 因此事务日志备份卷快照的最长保留时长需为 30 天。 如果将事务日志备份复制到位于 Azure 存储的另一个 NFS 共享，则不会出现这种情况。 在这种情况下，可以从该 NFS 共享拉取旧事务日志备份。

若要从存储快照和事务日志备份的最终存储复制中获益，请更改 SAP HANA 写入事务日志备份的位置。 可在 HANA Studio 中进行此更改。 

尽管 SAP HANA 自动备份完整日志段，但请指定要确定的日志备份间隔。 当你使用灾难恢复选项时尤其如此，因为你通常想要运行具有确定性时间段的日志备份。 在以下情况下，15分钟将设置为日志备份间隔。

![在 SAP HANA Studio 中计划 SAP HANA 备份日志](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

你还可以选择比每15分钟更频繁的备份。 较高的频率设置通常与 HANA 大型实例的灾难恢复功能结合使用。 某些客户每 5 分钟执行一次事务日志备份。

如果从未备份过数据库，则最后一步是执行基于文件的数据库备份，以创建必须在备份目录中存在的单个备份项。 否则，SAP HANA 无法启动指定的日志备份。

![创建基于文件的备份以创建单个备份项](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


第一次成功运行存储快照后，删除在步骤6中运行的测试快照。 有关详细信息，请参阅[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "删除测试快照-removeTestStorageSnapshot"。 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>监视磁盘卷上的快照数量和大小

在特定的存储卷上，可以监视快照的数量以及这些快照的存储消耗量。 `ls` 命令不会显示快照目录或文件。 Linux OS 命令`du`显示有关这些存储快照的详细信息，因为这些快照存储在相同的卷上。 使用带有以下选项的命令：

- `du –sh .snapshot`：此选项提供快照目录中所有快照的总数。
- `du –sh --max-depth=1`：此选项列出 **.snapshot** 文件夹中保存的所有快照，以及每个快照的大小。
- `du –hc`：此选项提供所有快照占用的总大小。

使用这些命令来确保所采用和存储的快照不会消耗卷上的所有存储。

>[!NOTE]
>启动 LUN 的快照在以前的命令中不可见。

### <a name="get-details-of-snapshots"></a>获取快照的详细信息
若要获取有关快照的详细信息，请`azure_hana_snapshot_details`使用脚本。 如果灾难恢复位置有活动服务器，则可以在任一位置运行此脚本。 此脚本根据每个包含快照的卷提供以下细分输出： 
   * 一个卷中全部快照的大小
   * 该卷中每个快照包含以下详细信息： 
      - 快照名称 
      - 创建时间 
      - 快照大小
      - 快照频率
      - 与该快照关联的 HANA 备份 ID（如果相关）

有关命令和输出的语法，请参阅[azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"列出快照-azure_hana_snapshot_details"。 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>减少服务器上的快照数

如前所述，可以减少存储的快照的数量。 用于启动快照的命令的最后两个参数是要保留的快照的标签与数量。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在上面的示例中，快照标签为 " **dailyhana**"。 带有此标签的快照数为**28**。 对磁盘空间消耗量做出响应时，可能想要减少存储的快照数。 例如，将快照数减少到15的一种简单方法是运行脚本，并将最后一个参数设置为**15**：

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果运行带有此设置的脚本，则快照数（包括新存储快照）为15。 会保存最新的 15 个快照，同时删除 15 个较旧的快照。

 >[!NOTE]
 > 仅当快照超过一小时的情况下，此脚本才能减少快照数。 该脚本不会删除不超过一小时的快照。 这些限制与提供的可选灾难恢复功能相关。

如果不再想要保留一组包含备份前缀**dailyhana**的快照，请在语法示例中使用**0**作为保留号来运行该脚本。 然后，将删除所有与该标签匹配的快照。 删除所有快照可能会影响 HANA 大型实例灾难恢复功能的功能。

第二种删除特定快照的方法是使用脚本 `azure_hana_snapshot_delete`。 此脚本旨在使用在 HANA Studio 中找到的 HANA 备份 ID 或通过快照名称本身删除快照或一组快照。 目前，备份 ID 仅与为**hana**快照类型创建的快照相关联。 "**日志**" 和 "**启动**" 类型的快照备份不会执行 SAP HANA 快照，因此，不会为这些快照找到备份 ID。 如果输入快照名称，将在不同卷上查找所有与输入的快照名称匹配的快照。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

有关该脚本的详细信息，请参阅[azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"删除快照 azure_hana_snapshot_delete"。

以用户**根**身份运行脚本。

>[!IMPORTANT]
>如果有数据仅存在于计划删除的快照上，则在删除快照后，该数据将永久丢失。


## <a name="file-level-restore-from-a-storage-snapshot"></a>从存储快照实现文件级还原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
对于 **hana** 和 **logs** 快照类型，可以直接在 **.snapshot** 目录中的卷上访问快照。 每个快照都有一个子目录。 将每个文件的快照中的每个文件的状态从该子目录复制到实际的目录结构中。 

在该脚本的当前版本中，*没有*为快照还原提供作为自助服务的还原脚本。 在故障转移期间，可以在灾难恢复站点上以自助服务灾难恢复脚本的形式执行快照还原。 若要从现有的可用快照还原所需的快照，必须通过打开服务请求联系 Microsoft 运营团队。

>[!NOTE]
>单个文件还原不适用于独立于 HANA 大型实例单元类型的启动 LUN 的快照。 **快照**目录未在启动 LUN 中公开。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>恢复到最近的 HANA 快照

在生产环境中，从存储快照恢复的过程可以作为客户事件启动 Microsoft Azure 支持。 如果在生产系统中删除了数据，则这是一项紧急的问题，而检索数据的唯一方法是还原生产数据库。

在不同的情况下，时间点恢复可能不太紧急，因此可以提前几天规划。 你可以使用 Azure 上的 SAP HANA 来规划此恢复，而不是提高高优先级的标志。 例如，你可能计划通过应用新的增强包来升级 SAP 软件。 然后又需要还原到升级增强包之前的快照状态。

发送请求之前，需要做好准备。 然后，Azure 团队上的 SAP HANA 可以处理请求并提供还原的卷。 接下来，需要基于快照还原 HANA 数据库。

有关使用新工具集还原快照的可能方法，请参阅在[Azure 中从存储快照手动恢复指南中 SAP HANA 的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"如何还原快照"。

若要为请求做好准备，请执行以下步骤。

1. 确定要还原的快照。 除非另有说明，否则只能还原 hana/data 卷。 

1. 关闭 HANA 实例。

   ![关闭 HANA 实例](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 卸载每个 HANA 数据库节点上的数据卷。 如果仍将数据卷载入操作系统，快照还原会失败。

   ![卸载每个 HANA 数据库节点上的数据卷](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 打开 Azure 支持请求，并提供有关还原特定快照的说明：

   - 在还原过程中： Azure 服务上的 SAP HANA 可能要求你参加会议呼叫来协调、验证和确认是否还原了正确的存储快照。 

   - 还原后： Azure 服务上的 SAP HANA 会在还原存储快照时通知你。

1. 完成还原过程后，请重新装载所有数据卷。

   ![重新装载所有数据卷](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



例如，从存储[快照中 SAP HANA Azure 手动恢复指南的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)第7步中介绍了从存储快照恢复的 SAP HANA 数据文件。

若要从快照备份还原，请参阅[从存储快照手动恢复 Azure 上的 SAP HANA 指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 

>[!Note]
>如果快照是由 Microsoft 操作还原的，则无需执行步骤7。


### <a name="recover-to-another-point-in-time"></a>恢复到另一个时间点
若要还原到特定的时间点，请参阅在[Azure 中从存储快照 SAP HANA 的手动恢复指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的 "将数据库恢复到以下时间点"。 


## <a name="next-steps"></a>后续步骤
- 请参阅[灾难恢复原则和准备](hana-concept-preparation.md)。
