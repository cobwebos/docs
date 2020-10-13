---
title: Azure 存储资源管理器 blob 版本控制指南 |Microsoft Docs
description: Azure 存储资源管理器的 Blob 版本控制指南
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: d318983cec1365b71d14731395e71528621659d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051896"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure 存储资源管理器 blob 版本控制指南

Microsoft Azure 存储资源管理器提供对 blob 版本的轻松访问和管理。 本指南将帮助你了解 blob 版本控制在存储资源管理器中的工作原理。 在继续之前，建议你阅读有关 [blob 版本控制](https://docs.microsoft.com/azure/storage/blobs/versioning-overview)的详细信息。

## <a name="terminology"></a>术语

本部分提供一些定义，以帮助了解其在本文中的用法。

- 软删除：备用的自动数据保护功能。 可在 [此处](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview)详细了解软删除。
- 活动 blob：创建处于活动状态的 blob 或 blob 版本。 只能对处于活动状态的 blob 或 blob 版本执行操作。
- 软删除 blob：标记为软删除的 blob 或 blob 版本。 只保留软删除的 blob 的保留期。
- Blob 版本：已启用 blob 版本控制创建的 blob。 每个 blob 版本都与一个版本 ID 相关联。
- 当前版本：标记为当前版本的 blob 版本。
- 以前的版本：不是当前版本的 blob 版本。
- 非版本 blob：已禁用 blob 版本控制创建的 blob。 非版本 blob 没有版本 ID。

## <a name="view-blob-versions"></a>查看 blob 版本

存储资源管理器支持四种查看 blob 的不同视图。

| 查看 | 活动非版本 blob | 软删除的非版本 blob | Blob 版本 |
| ---- | :----------: | :-----------: | :------------------: |
| 活动 blob | 是 | 否 | 仅当前版本 |
| 活动 blob 和软删除 blob | 是 | 是 | 仅当前版本 |
| 没有当前版本的活动 blob 和 blob | 是 | 否 | 当前版本或最新活动版本 |
| 没有当前版本的所有 blob 和 blob | 是 | 是 | 当前版本或最新版本 |

### <a name="active-blobs"></a>活动 blob

在此视图中，存储资源管理器显示：

- 活动非版本 blob
- 当前版本

### <a name="active-blobs-and-soft-deleted-blobs"></a>活动 blob 和软删除 blob

在此视图中，存储资源管理器显示：

- 活动非版本 blob
- 软删除的非版本 blob
- 当前版本。

### <a name="active-blobs-and-blobs-without-current-version"></a>没有当前版本的活动 blob 和 blob

在此视图中，存储资源管理器显示：

- 活动非版本 blob
- 当前版本
- 最新的活动早期版本。 

对于没有当前版本但具有活动先前版本的 blob，存储资源管理器会将其最新的活动早期版本显示为该 blob 的表示形式。

### <a name="all-blobs-and-blobs-without-current-version"></a>没有当前版本的所有 blob 和 blob

在此视图中，存储资源管理器显示：

- 活动非版本 blob
- 软删除的非版本 blob
- 当前版本
- 最新版本。 

对于没有当前版本的 blob，存储资源管理器显示其最新版本作为该 blob 的表示形式。

> [!Note]
> 由于服务限制，在列出 blob 版本时，存储资源管理器需要一些额外的处理才能获取虚拟目录的层次结构视图。 列出以下视图中的 blob 需要更长的时间：
> 
> - 没有当前版本的活动 blob 和 blob
> - 没有当前版本的所有 blob 和 blob

## <a name="manage-blob-versions"></a>管理 blob 版本

### <a name="view-versions-of-a-blob"></a>查看 blob 的版本

存储资源管理器提供了 " **管理版本** " 命令来查看 blob 的所有版本。 若要查看 blob 的版本，请选择要查看其版本的 blob，然后从工具栏或上下文菜单中选择 " **管理历史记录" " &rarr; 管理版本** "。

### <a name="download-blob-versions"></a>下载 blob 版本

若要下载一个或多个 blob 版本，请选择要下载的 blob 版本，并从工具栏或上下文菜单中选择 " **下载** "。

如果要下载 blob 的多个版本，则下载的文件的文件名称开头将包含其版本 Id。

### <a name="delete-blob-versions"></a>删除 blob 版本

若要删除一个或多个 blob 版本，请选择要删除的 blob 版本，并从工具栏或上下文菜单中选择 " **删除** "。

Blob 版本服从软删除策略。 如果启用软删除，则会软删除 blob 版本。 一种特殊情况是删除当前版本。 删除当前版本会自动使其成为活动的以前版本。

### <a name="promote-blob-version"></a>升级 blob 版本

可以通过将以前版本升级为当前版本来还原 blob 的内容。 选择要升级的 blob 版本，并从工具栏或上下文菜单中选择 " **升级版本** "。

非版本 blob 将被升级的 blob 版本覆盖。 确认操作之前，请确保不再需要该数据或自行备份数据。 当前版本会自动成为以前的版本，因此存储资源管理器不会提示进行确认。

### <a name="undelete-blob-version"></a>删除 blob 版本

Blob 版本不能单独删除。 它们必须同时被删除。 若要撤消删除 blob 的所有 blob 版本，请选择其中一个 blob 版本，并选择从工具栏或上下文菜单中选择 " **撤消删除** "。

### <a name="change-access-tier-of-blob-versions"></a>更改 blob 版本的访问层

每个 blob 版本都有其自己的访问层。 若要更改 blob 版本的访问层，请选择要更改访问层的 blob 版本，并从上下文菜单中选择 " **更改访问层 ...** "。

## <a name="see-also"></a>另请参阅

* [Blob 版本控制](https://docs.microsoft.com/azure/storage/blobs/versioning-overview)
* [blob 的软删除](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview)
