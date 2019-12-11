---
title: 使用 Azure 导入/导出服务 REST API | Microsoft Docs
description: 了解可在何处查找使用 Azure 导入/导出服务 REST API 的资源，包括操作说明和参考材料。
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978860"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>使用 Azure 导入/导出服务 REST API

Microsoft Azure 导入/导出服务公开一个 REST API 来实现对导入/导出作业的编程控制。 可通过 [Azure 门户](https://portal.azure.com/)执行的所有导入/导出操作也都可以使用 REST API 来完成。 此外，可以使用 REST API 来执行某些精细的操作，例如查询作业完成百分比，该操作目前在 Azure 门户中未提供。

有关导入/导出服务的概述以及演示如何使用门户创建和管理导入和导出作业的教程，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](../storage-import-export-service.md)。

## <a name="service-endpoints"></a>服务终结点

Azure 导入/导出服务是 Azure 资源管理器的资源提供程序，它在以下 HTTPS 终结点上提供一组 REST API 用于管理导入/导出作业：

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>版本控制

对导入/导出服务发出的请求必须指定 `api-version` 参数并将其值设置为 `2016-11-01`。

## <a name="importexport-service-operations"></a>导入/导出服务操作

[创建导入作业](../storage-import-export-creating-an-import-job.md)

[创建导出作业](../storage-import-export-creating-an-export-job.md)

[检索作业的状态信息](storage-import-export-retrieving-state-info-for-a-job.md)

[枚举作业](../storage-import-export-enumerating-jobs.md)

[取消和删除作业](storage-import-export-cancelling-and-deleting-jobs.md)

[备份驱动器清单](../storage-import-export-backing-up-drive-manifests.md)

[导入/导出作业的诊断和错误恢复](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>后续步骤

* [存储导入/导出 REST](/rest/api/storageimportexport)
