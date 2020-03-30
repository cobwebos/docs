---
title: 迁移到 Azure 文件共享
description: 了解迁移到 Azure 文件共享并查找迁移指南。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 903ce52120fce7c23c6a3754498b81fc6fc2430f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247310"
---
# <a name="migrate-to-azure-file-shares"></a>迁移到 Azure 文件共享

本文介绍迁移到 Azure 文件共享的基本方面。

除了迁移基础知识外，本文还包含现有个性化迁移指南的列表。 这些迁移指南可帮助您将文件移动到 Azure 文件共享中，并按数据当前所在的位置以及计划迁移到哪些部署模型（仅限云或混合部署）进行组织。

## <a name="migration-basics"></a>迁移基础知识

Azure 中有多种不同类型的云存储可用。 将文件迁移到 Azure 的一个基本方面是确定哪种 Azure 存储选项适合您的数据。

Azure 文件共享非常适合通用文件数据。 确实，您使用本地 SMB 或 NFS 共享的任何内容。 使用[Azure 文件同步](storage-sync-files-planning.md)，可以选择在本地多个 Windows 服务器上缓存多个 Azure 文件共享的内容。

如果应用程序当前在本地服务器上运行，则根据应用程序的不同，在 Azure 文件共享中存储文件可能适合您。 可以提升应用程序以在 Azure 中运行，并使用 Azure 文件共享作为共享存储。 您还可以考虑此方案[的 Azure 磁盘](../../virtual-machines/windows/managed-disks-overview.md)。 对于不依赖于 SMB 或计算机本地访问其数据或共享访问的云诞生应用程序，对象存储（如[Azure Blob）](../blobs/storage-blobs-overview.md)通常是最佳选择。

任何迁移的键是在将文件从当前存储位置迁移到 Azure 时捕获所有适用的文件保真度。 选择正确的 Azure 存储的帮助也是 Azure 存储选项支持多少保真度以及方案所需的保真度方面。 通用文件数据传统上取决于文件元数据。 应用程序数据可能不是。 文件有两个基本组件：

- **数据流**：文件的数据流存储文件内容。
- **文件元数据**： 文件元数据具有多个子组件：
   * 文件属性：例如，只读。
   * 文件权限：称为*NTFS 权限*或*文件和文件夹 ACL。*
   * 时间戳：最值得注意的是*创建和**上次修改*的时间戳。
   * 替代数据流：用于存储大量非标准属性的空间。

因此，在迁移中，文件保真度可以定义为在源上存储所有适用文件信息的能力、使用迁移工具传输它们的能力以及将它们存储在迁移的目标存储上的能力。

为确保迁移尽可能顺利进行，请确定[适合您需求的最佳复制工具](#migration-toolbox)，并将存储目标与源相匹配。

考虑到前面的信息，Azure 中通用文件的目标存储是什么[：Azure 文件共享](storage-files-introduction.md)。 与 Azure Blob 中的对象存储相比，文件元数据可以本机存储在 Azure 文件共享中的文件中。

Azure 文件共享还保留文件和文件夹层次结构。 此外：
* NTFS 权限可以存储在文件和文件夹中，因为它们是本地的。
* AD 用户（或 Azure AD DS 用户）可以本机访问 Azure 文件共享。 
    他们使用其当前标识，并根据共享权限以及文件和文件夹 ACL 获取访问权限。 当用户连接到本地文件共享时，行为与不同。
*  替代数据流是文件保真度的主要方面，当前无法存储在 Azure 文件共享中的文件中。
   涉及 Azure 文件同步时，它将保存在本地。

* [了解有关 Azure 文件共享的 AD 身份验证详细信息](storage-files-identity-auth-active-directory-enable.md)
* [了解有关 Azure 文件共享的 Azure 活动目录域服务 （AAD DS） 身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="migration-guides"></a>迁移指南

下表列出了详细的迁移指南。

通过：
1. 查找文件当前存储的源系统的行。
2. 确定是针对混合部署，其中是否使用 Azure 文件同步在本地缓存一个或多个 Azure 文件共享的内容，或者是否希望直接在云中使用 Azure 文件共享。 选择反映您的决策的目标列。
3. 在源和目标交集中，表单元格列出了可用的迁移方案。 选择其中一个可直接链接到详细的迁移指南。

没有链接的方案尚未发布迁移指南。 偶尔检查此表以获取更新。 新指南将在可用时发布。

| 源 | 目标： </br>混合部署 | 目标： </br>仅限云的部署 |
|:---|:--|:--|
| | 工具组合：| 工具组合： |
| Windows Server 2012 R2 和更高版本 | <ul><li>[Azure 文件同步](storage-sync-files-deployment-guide.md)</li><li>[Azure 文件同步 + 数据框](storage-sync-offline-data-transfer.md)</li><li>存储迁移服务 + Azure 文件同步</li></ul> | <ul><li>Azure 文件同步</li><li>Azure 文件同步 + 数据框</li><li>存储迁移服务 + Azure 文件同步</li><li>RoboCopy</li></ul> |
| Windows 服务器 2012 及旧版 | <ul><li>Azure 文件同步 + 数据框</li><li>存储迁移服务 + Azure 文件同步</li></ul> | <ul><li>存储迁移服务 + Azure 文件同步</li><li>RoboCopy</li></ul> |
| 网络附加存储 （NAS） | <ul><li>[Azure 文件同步 + RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux / 桑巴 | <ul><li>[RoboCopy = Azure 文件同步](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| 斯托简单 8100 / 8600 | <ul><li>[Azure 文件同步 = 8020 虚拟设备](storage-files-migration-storsimple-8000.md)</li></ul> | |
| 斯托简单 1200 | <ul><li>[Azure 文件同步](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>迁移工具箱

### <a name="file-copy-tools"></a>文件复制工具

有几个微软和非微软文件复制工具可用。 为了为您的迁移方案选择正确的文件复制工具，必须考虑三个基本问题：

* 复制工具是否支持给定文件副本的源和目标位置？ 
    * 它是否支持您的网络路径和/或可用协议（例如 REST/SMB/NFS）以及源和目标存储位置？
* 复制工具是否保留源/目标位置支持的必要文件保真度？ 在某些情况下，目标存储不支持与源相同的保真度。 您已经决定目标存储足以满足您的需要，因此复制工具只需要匹配目标文件保真度功能。
* 复制工具是否具有使其适合我的迁移策略的功能？ 
    * 例如，考虑它是否有允许您最小化停机时间的选项。 一个很好的问题是：我可以在同一个上运行此副本多次，由用户主动访问的位置吗？ 如果是这样，您可以显著减少停机时间。 将此与仅可在源停止更改时启动副本的情况进行比较，以确保完整副本。

下表对 Microsoft 工具及其当前是否适合 Azure 文件共享进行分类：

| 建议 | 工具 | 支持 Azure 文件共享 | 保留文件保真度 |
| :-: | :-- | :---- | :---- |
|![是，建议。](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | 支持。 Azure 文件共享可以装载为网络驱动器。 | 全保真* |
|![是，建议。](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure 文件同步 | 本机集成到 Azure 文件共享中。 | 全保真* |
|![是，建议。](media/storage-files-migration-overview/circle-green-checkmark.png)| 存储迁移服务 (SMS) | 间接支持。 Azure 文件共享可以装载为 SMS 目标服务器上的网络驱动器。 | 全保真* |
|![不完全推荐。](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure 数据箱 | 支持。 | 不复制元数据。 [可与 Azure 文件同步结合使用](storage-sync-offline-data-transfer.md)。 |
|![建议不要使用。](media/storage-files-migration-overview/circle-red-x.png)| AzCopy | 支持。 | 不复制元数据。 |
|![建议不要使用。](media/storage-files-migration-overview/circle-red-x.png)| Azure 存储资源管理器 | 支持。 | 不复制元数据。 |
|![建议不要使用。](media/storage-files-migration-overview/circle-red-x.png)| Azure 数据工厂 | 支持。 | 不复制元数据。 |
|||||

*\*完全保真度：满足或超过 Azure 文件共享功能。*

### <a name="migration-helper-tools"></a>迁移帮助工具

本节列出了帮助规划和执行迁移的工具。

* **RoboCopy，来自微软公司**

    文件迁移的最适用的复制工具之一，是微软 Windows 的一部分。 由于此工具中的许多选项，[主要 RoboCopy 文档](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)是一个有用的来源。

* **树的大小，来自JAM软件有限公司**

    Azure 文件同步主要随项目数（文件和文件夹）进行扩展，而与 TiB 总量一样， 则较少。 该工具可用于确定 Windows Server 卷上的文件和文件夹数。 此外，它可用于在[Azure 文件同步部署](storage-sync-files-deployment-guide.md)之前创建透视图 - 之后，当云分层参与，您不仅希望看到项目数，还希望看到服务器缓存使用最多的目录。
    此工具（经过测试的版本 4.4.1）与云分层文件兼容。 在正常操作期间，它不会导致分层文件的调用。


## <a name="next-steps"></a>后续步骤

1. 创建计划，为之部署 Azure 文件共享（仅限云或混合共享）而努力。
2. 查看可用迁移指南的列表，查找与 Azure 文件共享的源和部署相匹配的详细指南。

本文中提及的 Azure 文件技术提供了更多信息：

* [Azure 文件共享概述](storage-files-introduction.md)
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
* [Azure 文件同步：云分层](storage-sync-cloud-tiering.md)
