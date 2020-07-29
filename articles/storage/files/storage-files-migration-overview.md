---
title: 迁移到 Azure 文件共享
description: 了解到 Azure 文件共享的迁移并查找迁移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 512688345c03ea9d5da0e4933cd6a794eaaf597b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660761"
---
# <a name="migrate-to-azure-file-shares"></a>迁移到 Azure 文件共享

本文介绍迁移到 Azure 文件共享的基本环节。

本文包含迁移基础知识和迁移指南表。 这些指南可帮助你将文件移到 Azure 文件共享。 指南根据你的数据所在的位置以及你要迁移到的部署模型（仅限云或混合）进行组织。

## <a name="migration-basics"></a>迁移基础知识

Azure 有多种可用的云存储类型。 文件迁移到 Azure 的一个重要方面是确定哪个 Azure 存储选项适合你的数据。

[Azure 文件共享](storage-files-introduction.md)适合一般用途的文件数据。 此数据包括你为使用本地 SMB 或 NFS 共享的任何内容。 通过[Azure 文件同步](storage-sync-files-planning.md)，你可以在本地运行 Windows Server 的服务器上缓存多个 Azure 文件共享的内容。

对于当前在本地服务器上运行的应用程序，在 Azure 文件共享中存储文件可能是一个不错的选择。 你可以将应用移到 Azure，并使用 Azure 文件共享作为共享存储。 还可以考虑将[Azure 磁盘](../../virtual-machines/windows/managed-disks-overview.md)用于此方案。

某些云应用不依赖于 SMB 或计算机本地数据访问或共享访问。 对于这些应用程序，像[Azure blob](../blobs/storage-blobs-overview.md)这样的对象存储通常是最佳选择。

任何迁移中的关键是在将文件从其当前存储位置移到 Azure 时捕获所有适用的文件保真。 Azure 存储选项支持的保真度和方案所需的保真度还有助于你选择正确的 Azure 存储。 一般用途的文件数据通常依赖于文件元数据。 应用数据可能不是。

下面是文件的两个基本组件：

- **数据流：文件**的数据流存储文件内容。
- **文件元数据**：文件元数据具有以下子组件：
   * 文件属性，如只读
   * 文件权限，可称为*NTFS 权限*或*文件和文件夹 acl*
   * 时间戳，最值得注意的是创建和上次修改的时间戳
   * 备用数据流，它是用于存储大量非标准属性的空间

可以将迁移中的文件保真度定义为：

- 将所有适用的文件信息存储在源上。
- 通过迁移工具传输文件。
- 将文件存储在迁移的目标存储中。

若要确保迁移顺利进行，请根据[需要确定最佳的复制工具](#migration-toolbox)，并将存储目标与源相匹配。

考虑到上述信息，可以看到 Azure 中的常规用途文件的目标存储是[azure 文件共享](storage-files-introduction.md)。

与 Azure blob 中的对象存储不同，Azure 文件共享可本机存储文件元数据。 Azure 文件共享还会保留文件和文件夹层次结构、属性和权限。 NTFS 权限可以存储在文件和文件夹上，因为它们位于本地。

Active Directory 的用户（其本地域控制器）可以以本机方式访问 Azure 文件共享。 因此，Azure Active Directory 域服务（Azure AD DS）的用户可以。 每个都使用其当前标识，基于共享权限和文件和文件夹 Acl 获取访问权限。 此行为类似于连接到本地文件共享的用户。

备用数据流是文件保真的主要方面，当前无法存储在 Azure 文件共享中的文件上。 当使用 Azure 文件同步时，它将保留在本地。

详细了解[Azure AD 身份验证](storage-files-identity-auth-active-directory-enable.md)和 Azure 文件共享[Azure AD DS 身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="migration-guides"></a>迁移指南

下表列出了详细的迁移指南。

如何使用表：

1. 找到当前存储文件的源系统的行。

1. 选择下列目标之一：

   - 使用 Azure 文件同步在本地缓存 Azure 文件共享内容的混合部署
   - 云中的 Azure 文件共享

   选择与你的选择相匹配的目标列。

1. 在源和目标的交集内，表单元列出可用的迁移方案。 选择一个直接链接到详细的迁移指南。

没有链接的方案还没有已发布的迁移指南。 偶尔检查此表中是否有更新。 新指南可用时将发布。

| 源 | 目标： </br>混合部署 | 目标： </br>仅限云的部署 |
|:---|:--|:--|
| | 工具组合：| 工具组合： |
| Windows Server 2012 R2 及更高版本 | <ul><li>[Azure 文件同步](storage-sync-files-deployment-guide.md)</li><li>[Azure 文件同步和 Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>Azure 文件同步和存储迁移服务</li></ul> | <ul><li>Azure 文件同步</li><li>Azure 文件同步和 Data Box</li><li>Azure 文件同步和存储迁移服务</li><li>RoboCopy</li></ul> |
| Windows Server 2012 和更低版本 | <ul><li>Azure 文件同步和 Data Box</li><li>Azure 文件同步和存储迁移服务</li></ul> | <ul><li>Azure 文件同步和存储迁移服务</li><li>RoboCopy</li></ul> |
| 网络连接存储（NAS） | <ul><li>[Azure 文件同步和 RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux 或 Samba | <ul><li>[Azure 文件同步和 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple 云设备8100或 StorSimple 云设备8600 | <ul><li>[Azure 文件同步和 StorSimple 云设备8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 云设备1200 | <ul><li>[Azure 文件同步](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>迁移工具箱

### <a name="file-copy-tools"></a>文件复制工具

Microsoft 和其他工具提供了多个文件复制工具。 若要为迁移方案选择合适的工具，您必须考虑以下基本问题：

* 该工具是否支持文件复制的源和目标位置？

* 该工具是否支持源和目标存储位置之间的网络路径或可用协议（如 REST、SMB 或 NFS）？

* 该工具是否保留源位置和目标位置支持的必要文件保真度？

    在某些情况下，目标存储不支持与源相同的保真度。 如果目标存储满足你的需求，则该工具必须仅与目标的文件保真功能匹配。

* 该工具是否具有使其适合你的迁移策略的功能？

    例如，考虑该工具是否允许您最大程度地减少停机时间。
    
    当某个工具支持将源镜像到目标的选项时，通常可以在同一源和目标上多次运行该工具，而源仍可访问。

    首次运行该工具时，它会复制大量数据。 此初始运行可能会持续一段时间。 它通常比您为业务流程使数据源脱机所需的时间要长。

    通过将源镜像到目标（如使用**robocopy/MIR**），可以在同一源和目标上再次运行该工具。 运行速度要快得多，因为它只需要传输在上一次运行之后发生的源更改。 通过这种方式重新运行复制工具可显著减少停机时间。

下表对 Microsoft 工具及其当前适用于 Azure 文件共享的适用性进行分类：

| 建议 | 工具 | 支持 Azure 文件共享 | 保存文件保真 |
| :-: | :-- | :---- | :---- |
|![是，建议使用](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 。 可以将 Azure 文件共享装载为网络驱动器。 | 完全保真。 * |
|![是，建议使用](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 文件同步 | 本机集成到 Azure 文件共享中。 | 完全保真。 * |
|![是，建议使用](media/storage-files-migration-overview/circle-green-checkmark.png)| 存储迁移服务 | 间接支持。 在 SMS 目标服务器上，可以将 Azure 文件共享装载为网络驱动器。 | 完全保真。 * |
|![是，建议使用](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy 版本10.4 或更高版本| 。 | 完全保真。 * |
|![不完全推荐](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | 。 | 不复制元数据。 [Data Box 可以与 Azure 文件同步一起使用](storage-sync-offline-data-transfer.md)。 |
|![不完全推荐](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure 存储资源管理器，版本1.14 | 。 | 不复制 Acl。 支持时间戳。  |
|![不推荐](media/storage-files-migration-overview/circle-red-x.png)| Azure 数据工厂 | 。 | 不复制元数据。 |
|||||

*\*完全保真：满足或超过 Azure 文件共享功能。*

### <a name="migration-helper-tools"></a>迁移帮助程序工具

本部分介绍可帮助你规划和运行迁移的工具。

#### <a name="robocopy-from-microsoft-corporation"></a>Microsoft Corporation 的 RoboCopy

RoboCopy 是最适用于文件迁移的工具之一。 它作为 Windows 的一部分提供。 主[RoboCopy 文档](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)是此工具的众多选项的有用资源。

#### <a name="treesize-from-jam-software-gmbh"></a>Database 从卡纸软件 GmbH

Azure 文件同步主要与项目（文件和文件夹）的数量进行缩放，而不是用总存储量来缩放。 Database 工具允许你确定 Windows Server 卷上的项目数。

您可以使用该工具在[Azure 文件同步部署](storage-sync-files-deployment-guide.md)之前创建透视。 在部署后，还可以使用云分层。 在这种情况下，你将看到项数以及哪些目录最多使用服务器缓存。

该工具的测试版本是版本4.4.1。 它与云分层文件兼容。 在正常操作过程中，该工具不会导致重新调用分层文件。

## <a name="next-steps"></a>后续步骤

1. 创建要部署 Azure 文件共享（仅限云或混合）的计划。
1. 查看可用迁移指南列表，查找与 Azure 文件共享的源和部署匹配的详细指南。

下面是有关本文中所述的 Azure 文件技术的详细信息：

* [Azure 文件共享概述](storage-files-introduction.md)
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [Azure 文件同步：云分层](storage-sync-cloud-tiering.md)
