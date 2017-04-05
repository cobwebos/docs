---
title: "使用 Azure 导入/导出服务 REST API | Microsoft Docs"
description: "了解可在何处查找使用 Azure 导入/导出服务 REST API 的资源，包括操作说明和参考材料。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e4f5ca289f4bd87574e448d37a1154b222f221f5
ms.lasthandoff: 03/30/2017


---
# <a name="using-the-azure-importexport-service-rest-api"></a>使用 Azure 导入/导出服务 REST API

Microsoft Azure 导入/导出服务公开一个 REST API 来实现对导入/导出作业的编程控制。 可通过 [Azure 门户](https://portal.azure.com/)执行的所有导入/导出操作也都可以使用 REST API 来完成。 此外，可以使用 REST API 来执行某些精确的操作，例如查询作业完成百分比，而这些操作目前在经典门户中是无法完成的。

有关导入/导出服务的概述以及演示如何使用经典门户创建和管理导入和导出作业的教程，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](storage-import-export-service.md)。

## <a name="service-endpoints"></a>服务终结点

Azure 导入/导出服务是 Azure Resource Manager 的资源提供程序，它在以下 HTTPS 终结点上提供一组 REST API 用于管理导入/导出作业：

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>版本控制

对导入/导出服务发出的请求必须指定 `api-version` 参数并将其值设置为 `2016-11-01`。

## <a name="importexport-service-operations"></a>导入/导出服务操作

[创建导入作业](storage-import-export-creating-an-import-job.md)

[创建导出作业](storage-import-export-creating-an-export-job.md)

[检索作业的状态信息](storage-import-export-retrieving-state-info-for-a-job.md)

[枚举作业](storage-import-export-enumerating-jobs.md)

[取消和删除作业](storage-import-export-cancelling-and-deleting-jobs.md)

[备份驱动器清单](storage-import-export-backing-up-drive-manifests.md)

[导入/导出作业的诊断和错误恢复](storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>后续步骤

* [存储导入/导出 REST](/rest/api/storageimportexport)

