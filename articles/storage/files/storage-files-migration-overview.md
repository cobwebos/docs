---
title: 迁移到 Azure 文件共享
description: 了解迁移到 Azure 文件共享并查找迁移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d6141d48d67dd44c348961c6e09acf4e2531a61e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685988"
---
# <a name="migrate-to-azure-file-shares"></a>迁移到 Azure 文件共享

本文介绍迁移到 Azure 文件共享的基本方面。

本文包含迁移基础知识和迁移指南表。 这些指南可帮助您将文件移动到 Azure 文件共享中。 指南根据数据所在的位置以及要移动到的部署模型（仅限云或混合部署）进行组织。

## <a name="migration-basics"></a>迁移基础知识

Azure 具有多种可用类型的云存储。 文件迁移到 Azure 的一个基本方面是确定哪种 Azure 存储选项适合您的数据。

[Azure 文件共享](storage-files-introduction.md)适用于通用文件数据。 此数据包括您使用本地 SMB 或 NFS 共享的任何内容。 使用[Azure 文件同步](storage-sync-files-planning.md)，可以在本地运行 Windows 服务器的服务器上缓存多个 Azure 文件共享的内容。

对于当前在本地服务器上运行的应用，将文件存储在 Azure 文件共享中可能是一个不错的选择。 您可以将应用移动到 Azure，并使用 Azure 文件共享作为共享存储。 您还可以考虑此方案[的 Azure 磁盘](../../virtual-machines/windows/managed-disks-overview.md)。

某些云应用不依赖于 SMB 或计算机本地数据访问或共享访问。 对于这些应用，像[Azure Blob](../blobs/storage-blobs-overview.md)这样的对象存储通常是最佳选择。

任何迁移的键是在将文件从当前存储位置移动到 Azure 时捕获所有适用的文件保真度。 Azure 存储选项支持的保真度以及方案所需的多少也可以帮助您选择正确的 Azure 存储。 通用文件数据传统上取决于文件元数据。 应用数据可能不是。

下面是文件的两个基本组件：

- **数据流**：文件的数据流存储文件内容。
- **文件元数据**：文件元数据具有以下子组件：
   * 文件属性（如只读）
   * 文件权限，可以称为*NTFS 权限*或*文件和文件夹 ACL*
   * 时间戳，最显著的是创建和上次修改的时间戳
   * 替代数据流，用于存储大量非标准属性的空间

迁移中的文件保真度可以定义为：

- 将所有适用的文件信息存储在源上。
- 使用迁移工具传输文件。
- 将文件存储在迁移的目标存储中。

为确保迁移顺利进行，请确定[适合您需求的最佳复制工具](#migration-toolbox)，并将存储目标与源相匹配。

考虑到前面的信息，您可以看到 Azure 中通用文件的目标存储是[Azure 文件共享](storage-files-introduction.md)。

与 Azure Blob 中的对象存储不同，Azure 文件共享可以本机存储文件元数据。 Azure 文件共享还保留文件和文件夹层次结构、属性和权限。 NTFS 权限可以存储在文件和文件夹上，因为它们是本地的。

Active Directory 的用户可以本机访问 Azure 文件共享（其本地域控制器）。 Azure 活动目录域服务 （Azure AD DS） 的用户也是如此。 每个使用其当前标识根据共享权限以及文件和文件夹 ACL 访问。 此行为类似于连接到本地文件共享的用户。

替代数据流是文件保真度的主要方面，当前无法存储在 Azure 文件共享中的文件中。 使用 Azure 文件同步时，它将保存在本地。

详细了解 Azure 文件共享的[Azure AD 身份验证](storage-files-identity-auth-active-directory-enable.md)和 Azure AD [DS 身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)。

## <a name="migration-guides"></a>迁移指南

下表列出了详细的迁移指南。

如何使用表：

1. 查找文件当前存储的源系统的行。

1. 选择以下目标之一：

   - 使用 Azure 文件同步在本地缓存 Azure 文件共享内容的混合部署
   - 云中的 Azure 文件共享

   选择与您的选择相匹配的目标列。

1. 在源和目标交集中，表单元格列出了可用的迁移方案。 选择一个可直接链接到详细迁移指南。

没有链接的方案还没有发布的迁移指南。 偶尔检查此表以获取更新。 新指南将在可用时发布。

| 源 | 目标： </br>混合部署 | 目标： </br>仅限云的部署 |
|:---|:--|:--|
| | 工具组合：| 工具组合： |
| Windows 服务器 2012 R2 及更高版本 | <ul><li>[Azure 文件同步](storage-sync-files-deployment-guide.md)</li><li>[Azure 文件同步和 Azure 数据框](storage-sync-offline-data-transfer.md)</li><li>Azure 文件同步和存储迁移服务</li></ul> | <ul><li>Azure 文件同步</li><li>Azure 文件同步和数据框</li><li>Azure 文件同步和存储迁移服务</li><li>RoboCopy</li></ul> |
| Windows Server 2012 和更低版本 | <ul><li>Azure 文件同步和数据框</li><li>Azure 文件同步和存储迁移服务</li></ul> | <ul><li>Azure 文件同步和存储迁移服务</li><li>RoboCopy</li></ul> |
| 网络连接存储 （NAS） | <ul><li>[Azure 文件同步和 RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux 或桑巴 | <ul><li>[Azure 文件同步和 RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| 微软 Azure StorSimple 云设备 8100 或 StorSimple 云设备 8600 | <ul><li>[Azure 文件同步和 StorSimple 云设备 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple 云设备 1200 | <ul><li>[Azure 文件同步](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>迁移工具箱

### <a name="file-copy-tools"></a>文件复制工具

微软和其他公司提供了多种文件复制工具。 要为您的迁移方案选择正确的工具，必须考虑以下基本问题：

* 该工具是否支持文件副本的源位置和目标位置？

* 该工具是否支持源和目标存储位置之间的网络路径或可用协议（如 REST、SMB 或 NFS）？

* 该工具是否保留源和目标位置支持的必要文件保真度？

    在某些情况下，目标存储不支持与源相同的保真度。 如果目标存储足以满足您的需要，则该工具必须仅匹配目标的文件保真功能。

* 该工具是否具有使其适合您的迁移策略的功能？

    例如，请考虑该工具是否允许您将停机时间降至最低。
    
    当工具支持将源镜像到目标的选项时，通常可以在源保持可访问时在同一源和目标上运行多次。

    首次运行该工具时，它会复制大部分数据。 此初始运行可能会持续一段时间。 对于业务流程，使数据源脱机的时间通常比您希望的要长。

    通过将源镜像到目标（如**使用 robocopy /MIR），** 可以在同一源和目标上再次运行该工具。 运行速度要快得多，因为它只需要传输上次运行后发生的源更改。 以这种方式重新运行复制工具可以显著减少停机时间。

下表对 Microsoft 工具及其当前是否适合 Azure 文件共享进行分类：

| 建议 | 工具 | 支持 Azure 文件共享 | 保存文件保真度 |
| :-: | :-- | :---- | :---- |
|![是，建议](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 支持。 Azure 文件共享可以装载为网络驱动器。 | 完全保真度。 |
|![是，建议](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 文件同步 | 本机集成到 Azure 文件共享中。 | 完全保真度。 |
|![是，建议](media/storage-files-migration-overview/circle-green-checkmark.png)| 存储迁移服务 | 间接支持。 Azure 文件共享可以装载为 SMS 目标服务器上的网络驱动器。 | 完全保真度。 |
|![不完全推荐](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Data Box | 支持。 | 不复制元数据。 [数据框可与 Azure 文件同步一起使用](storage-sync-offline-data-transfer.md)。 |
|![不建议](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | 支持。 | 不复制元数据。 |
|![不建议](media/storage-files-migration-overview/circle-red-x.png)| Azure 存储资源管理器 | 支持。 | 不复制元数据。 |
|![不建议](media/storage-files-migration-overview/circle-red-x.png)| Azure 数据工厂 | 支持。 | 不复制元数据。 |
|||||

*\*完全保真度：满足或超过 Azure 文件共享功能。*

### <a name="migration-helper-tools"></a>迁移帮助工具

本节介绍可帮助您规划和运行迁移的工具。

#### <a name="robocopy-from-microsoft-corporation"></a>微软公司的RoboCopy

RoboCopy 是最适合文件迁移的工具之一。 它是 Windows 的一部分。 主要[RoboCopy 文档](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)是此工具的许多选项的有用资源。

#### <a name="treesize-from-jam-software-gmbh"></a>来自 JAM 软件有限公司的树大小

Azure 文件同步主要随项目数（文件和文件夹）数进行缩放，而不是使用总存储量进行缩放。 树大小工具允许您确定 Windows 服务器卷上的项目数。

可以使用 该工具在[Azure 文件同步部署](storage-sync-files-deployment-guide.md)之前创建透视图。 您还可以在部署后参与云分层时使用它。 在这种情况下，您将看到项目数以及服务器缓存最多的目录。

该工具的测试版本为版本 4.4.1。 它与云分层文件兼容。 该工具在正常操作期间不会导致分层文件的调用。

## <a name="next-steps"></a>后续步骤

1. 创建需要部署 Azure 文件共享（仅限云或混合）的计划。
1. 查看可用迁移指南的列表，查找与 Azure 文件共享的源和部署相匹配的详细指南。

以下是本文中提到的有关 Azure 文件技术的详细信息：

* [Azure 文件共享概述](storage-files-introduction.md)
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [Azure 文件同步：云分层](storage-sync-cloud-tiering.md)
