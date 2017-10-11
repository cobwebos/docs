---
title: "Azure 导入/导出作业的诊断和错误恢复 | Microsoft Docs"
description: "了解如何为 Microsoft Azure 导入/导出服务作业启用详细日志记录。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 0068aae9d6780aa41a070db0eb191d0d5a165d21
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Azure 导入/导出作业的诊断和错误恢复
对于每个已处理的驱动器，Azure 导入/导出服务会在关联的存储帐户中创建错误日志。 也可以通过在调用[放置作业](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate)或[更新作业属性](/rest/api/storageimportexport/jobs#Jobs_Update)操作时将 `LogLevel` 属性设置为 `Verbose` 来启用详细日志记录。

 默认情况下，日志将写入一个名为 `waimportexport` 的容器中。 可以在调用“`Put Job`”或“`Update Job Properties`”操作时设置“`DiagnosticsPath`”属性来指定其他名称。 使用以下命名约定将日志存储为块 Blob：`waies/jobname_driveid_timestamp_logtype.xml`。

 可以调用[获取作业](/rest/api/storageimportexport/jobs#Jobs_Get)操作来检索作业的日志 URI。 每个驱动器的详细日志 URI 在 `VerboseLogUri` 属性中返回，错误日志的 URI 在 `ErrorLogUri` 属性中返回。

可以使用日志记录数据来识别以下问题。

## <a name="drive-errors"></a>驱动器错误

以下各项被归类为驱动器错误：

-   访问或读取清单文件时出错

-   BitLocker 密钥不正确

-   驱动器读/写错误

## <a name="blob-errors"></a>Blob 错误

以下各项被归类为 blob 错误：

-   Blob 或者名称不正确或有冲突

-   缺少文件

-   找不到 Blob

-   文件已截断（磁盘上的文件小于清单中指定的文件大小）

-   文件内容已损坏（对于导入作业，可以根据 MD5 校验和不匹配情况来检测这种问题）

-   Blob 元数据和属性文件已损坏（可以使用 MD5 校验和不匹配情况来检测这种问题）

-   Blob 属性和/或元数据文件的架构错误

可能存在以下情况：导入或导出作业的某些部分未成功完成，但整体作业仍已完成。 在此情况下，可以通过网络上传或下载数据的缺失部分，也可以创建新作业来传输数据。 若要了解如何通过网络修复数据，请参阅 [Azure 导入/导出工具参考](storage-import-export-tool-how-to-v1.md)。

## <a name="next-steps"></a>后续步骤

* [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)
