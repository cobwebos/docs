---
title: "为 Azure 导入/导出创建导入作业 | Microsoft Docs"
description: "了解如何为 Microsoft Azure 导入/导出服务创建导入作业。"
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: 
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: d373d2a0e601f2796719fc5efb8761f276ab24d9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>为 Azure 导入/导出服务创建导入作业

使用 REST API 为 Microsoft Azure 导入/导出服务创建导入作业的过程包括以下步骤：

-   使用 Azure 导入/导出工具准备驱动器。

-   获取驱动器的寄送目的地位置。

-   创建导入作业。

-   通过支持的运输服务将驱动器寄送到 Microsoft。

-   使用寄送详细信息更新导入作业。

 有关导入/导出服务的概述以及演示如何使用 [Azure 门户](https://portal.azure.com/)创建和管理导入和导出作业的教程，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](storage-import-export-service.md)。

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>使用 Azure 导入/导出工具准备驱动器

无论是通过门户还是 REST API 创建作业，为导入作业准备驱动器的步骤都是相同的。

下面是驱动器准备工作的简要概述。 有关完整说明，请参阅 [Azure 导入/导出工具参考](storage-import-export-tool-how-to-v1.md)。 可从[此处](http://go.microsoft.com/fwlink/?LinkID=301900)下载 Azure 导入/导出工具。

准备驱动器的过程包括以下步骤：

-   确定要导入的数据。

-   在 Microsoft Azure 存储中确定目标 Blob。

-   使用 Azure 导入/导出工具将数据复制到一个或多个硬盘驱动器。

 Azure 导入/导出工具还会在准备每个驱动器时为其生成清单文件。 清单文件中包含：

-   所有要上传的文件的枚举，以及从这些文件到 Blob 的映射。

-   每个文件的段校验和。

-   有关要与每个 Blob 关联的元数据和属性的信息。

-   当要上传的 Blob 名称与容器中现有 Blob 的名称相同时所要执行的操作列表。 可能的选项包括：a) 使用文件覆盖 Blob，b) 保留现有 Blob 并跳过上传文件，c) 将后缀追加到名称，使之不与其他文件冲突。

## <a name="obtaining-your-shipping-location"></a>获取寄送位置

在创建导入作业之前，需要通过调用[列出位置](/rest/api/storageimportexport/listlocations)操作获取寄送位置名称和地址。 `List Locations` 返回位置及其邮寄地址的列表。 可以从返回的列表中选择一个位置，并将硬盘驱动器寄送到该地址。 也可以使用“`Get Location`”操作直接获取特定位置的寄送地址。

 遵循以下步骤获取寄送位置：

-   指定存储帐户位置的名称。 可在 Azure 门户中存储帐户的“仪表板”上的“位置”字段下找到该值，或者使用服务管理 API 操作[获取存储帐户属性](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties)来查询该值。

-   通过调用“`Get Location`”操作来检索可用于处理此存储帐户的位置。

-   如果位置的 `AlternateLocations` 属性包含位置本身，则可以使用此位置。 否则，请使用某个备用位置再次调用“`Get Location`”操作。 原始位置可能会出于维护目的而暂时关闭。

## <a name="creating-the-import-job"></a>创建导入作业
若要创建导入作业，请调用[放置作业](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate)操作。 需要提供以下信息：

-   作业的名称。

-   存储帐户名称。

-   在上一步骤中获取的寄送位置名称。

-   作业类型（导入）。

-   应在导入作业完成后将驱动器寄送到的回邮地址。

-   作业中的驱动器列表。 对于每个驱动器，必须包含在驱动器准备步骤中获取的以下信息：

    -   驱动器 ID

    -   BitLocker 密钥

    -   硬盘驱动器上的清单文件相对路径

    -   Base16 编码的清单文件 MD5 哈希

## <a name="shipping-your-drives"></a>寄送驱动器
必须将驱动器寄送到在上一步骤中获取的地址，并向导入/导出服务提供包裹的跟踪号。

> [!NOTE]
>  必须通过支持的、可提供包裹跟踪号的承运人寄送驱动器。

## <a name="updating-the-import-job-with-your-shipping-information"></a>使用寄送信息更新导入作业
获取跟踪号后，请调用[更新作业属性](/api/storageimportexport/jobs#Jobs_Update)操作更新承运人名称、作业跟踪号以及运送回邮的承运人帐号。 可以选择性地指定驱动器数量和寄送日期。

## <a name="next-steps"></a>后续步骤

* [使用导入/导出服务 REST API](storage-import-export-using-the-rest-api.md)
