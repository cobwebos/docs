---
title: 取消和删除 Azure 导入/导出作业 | Microsoft Docs
description: 了解如何取消和删除 Microsoft Azure 导入/导出服务的作业。
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: e11cf974a5f33020c889844dd34f51612e13036e
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521007"
---
# <a name="canceling-and-deleting-azure-importexport-jobs"></a>取消和删除 Azure 导入/导出作业

 若要在作业处于 `Packaging` 状态之前请求取消该作业，请调用[更新作业属性](/rest/api/storageimportexport/jobs#Jobs_Update)操作并将 `CancelRequested` 元素设置为 `true`。 系统会尽最大努力取消该作业。 如果驱动器正在传输数据，则即使在请求取消后，数据也仍可能继续传输。

 已取消的作业将转为 `Completed` 状态并保留 90 天，届时它将被删除。

 若要删除某个作业，请在传送该作业之前（即，在该作业处于 `Creating` 状态时）调用[删除作业](/rest/api/storageimportexport/jobs#Jobs_Delete)操作。 也可以在作业处于 `Completed` 状态时将其删除。 删除某个作业后，不再能够通过 REST API 或 Azure 门户访问其信息和状态。

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]

## <a name="next-steps"></a>后续步骤

* [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)
