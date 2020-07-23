---
title: 在本地读取和更新可靠集合备份
description: 使用 Azure 中的备份资源管理器 Service Fabric 读取和更新本地可靠集合备份。
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: 1da70c37c8a6ed93e7abe1b5d329e808c592e43a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86034822"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>使用备份资源管理器读取和更新可靠的集合备份

如果数据在 Service Fabric 可靠集合中损坏，Azure Service Fabric 备份资源管理器可帮助进行数据更正。 在应用程序中引入的任何 bug 或在活动群集中发生的任何错误项可能会损坏数据的当前状态。

利用备份资源管理器的帮助，你可以执行以下任务：
-   查询集合的元数据。
-   查看加载的备份集合中的当前状态及其条目。
-   列出自上一个检查点以来执行的事务。
-   通过在集合中添加、更新或删除项来更新集合。
-   使用更新的状态进行全新备份。

> [!NOTE]
> Service Fabric 备份资源管理器目前只支持在备份中查看和编辑可靠集合。
>

## <a name="access-the-backup"></a>访问备份

可以通过以下任意方式使用 Service Fabric 备份资源管理器来查看或更新备份中的可靠集合：
-   **二进制**：使用 NuGet 包查看和更改可靠集合。
-   **Http/REST**：使用基于 HTTP 的 REST 服务器来查看和更改可靠集合。
-   **bkpctl**：使用 Service Fabric 备份资源管理器命令行接口（CLI）来查看和更改可靠的集合备份。

备份资源管理器具有适用于高级用户的 c # 库。 您可以直接在应用程序中使用库来处理可靠的集合，这与客户在其现有有状态服务中使用状态管理器的方式类似。 对于基本用户和基本用例，资源管理器还具有公开用于检查备份的 Api 的独立 REST 服务器。 Bkpctl CLI 工具适用于 REST Api，基于 Python。 可以使用 CLI 工具来读取和更新备份，并通过命令行执行新的备份。

有关详细信息，请参阅[Service Fabric 备份资源管理器](https://github.com/microsoft/service-fabric-backup-explorer)GitHub 存储库。 存储库包含源和发布信息以及设置说明。

你还可以在本地生成存储库并处理备份。
 
备份资源管理器的 NuGet （ServiceFabric. ReliableCollectionBackup）将在[nuget.org](https://www.nuget.org/)上提供。 

## <a name="next-steps"></a>后续步骤

* 详细了解[Azure Service Fabric 有状态服务中的可靠集合](service-fabric-reliable-services-reliable-collections.md)。
* 查看[Service Fabric 最佳实践](service-fabric-best-practices-overview.md)。
