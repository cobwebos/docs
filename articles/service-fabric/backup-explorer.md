---
title: 在本地读取和更新可靠集合备份
description: 使用 Azure Service Fabric 中的备份资源管理器来读取和更新本地可靠集合备份。
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: 1da70c37c8a6ed93e7abe1b5d329e808c592e43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86034822"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>通过使用备份资源管理器读取和更新可靠集合备份

如果数据在 Service Fabric 可靠集合中发生损坏，Azure Service Fabric 备份资源管理器可帮助进行数据更正。 应用程序中引入的任何 bug 或活动群集中出现的任何错误条目可能会损坏数据的当前状态。

通过备份资源管理器的帮助，可以执行以下任务：
-   查询集合的元数据。
-   查看加载的备份集合中的当前状态及其条目。
-   列出自上一个检查点以来已执行的事务。
-   通过在集合中添加、更新或删除条目来更新集合。
-   通过使用更新的状态执行全新备份。

> [!NOTE]
> Service Fabric 备份资源管理器目前只支持查看和编辑备份中的可靠集合。
>

## <a name="access-the-backup"></a>访问备份

可以通过以下任意方式使用 Service Fabric 备份资源管理器来查看或更新备份中的可靠集合：
-   二进制：使用 NuGet 包查看和更改可靠集合。
-   HTTP/REST：使用基于 HTTP 的 REST 服务器来查看和更改可靠集合。
-   bkpctl：使用 Service Fabric 备份资源管理器命令行接口 (CLI) 来查看和更改可靠集合备份。

备份资源管理器具有适用于高级用户的 C# 库。 可以直接在应用程序中使用该库来处理可靠集合，方式类似于客户在其现有的有状态服务中使用状态管理器。 对于基础用户以及在基础用例中，该资源管理器还有一个独立 REST 服务器，此服务器公开 API 以检查备份。 bkpctl CLI 工具基于 REST API 而工作，并且是基于 Python 实现的。 可以使用该 CLI 工具来读取和更新备份，并通过命令行执行新备份。

有关详细信息，请参阅 [Service Fabric 备份资源管理器](https://github.com/microsoft/service-fabric-backup-explorer) GitHub 存储库。 该存储库包含源和发布信息以及设置说明。

你也可以在本地生成该存储库并处理备份。
 
[nuget.org](https://www.nuget.org/) 上将会提供适用于备份资源管理器的 NuGet (Microsoft.ServiceFabric.ReliableCollectionBackup.Parser)。 

## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure Service Fabric 有状态服务中的可靠集合](service-fabric-reliable-services-reliable-collections.md)。
* 查看 [Service Fabric 最佳做法](service-fabric-best-practices-overview.md)。
