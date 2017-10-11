---
title: "备份 Azure 导入/导出驱动器清单 | Microsoft Docs"
description: "了解如何自动备份 Microsoft Azure 导入/导出服务的驱动器清单。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>为 Azure 导入/导出作业备份驱动器清单

通过在[放置作业](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate)或[更新作业属性](/rest/api/storageimportexport/jobs#Jobs_Update) REST API 操作中将 `BackupDriveManifest` 属性设置为 `true`，可以自动将驱动器清单备份到 blob。 默认情况下，不会备份驱动器清单。 驱动器清单备份以块 Blob 的形式存储在与作业关联的存储帐户中的某个容器内。 该容器的名称默认为 `waimportexport`，但可以在调用“`Put Job`”或“`Update Job Properties`”操作时在“`DiagnosticsPath`”属性中指定其他名称。 备份清单 Blob 按以下格式命名：`waies/jobname_driveid_timestamp_manifest.xml`。

 可以调用[获取作业](/rest/api/storageimportexport/jobs#Jobs_Get)操作来为作业检索备份驱动器清单的 URI。 Blob URI 会在每个驱动器的 `ManifestUri` 属性中返回。

## <a name="next-steps"></a>后续步骤

* [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)
