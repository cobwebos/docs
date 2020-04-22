---
title: include 文件
description: include 文件
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: fc5f4d2c10cac23600025a72fedf7fe2cec5a34e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684088"
---
若要读取存档存储中的数据，必须先将 Blob 的层更改为热层或冷层。 此过程称为解冻，可能需要多个小时才能完成。 建议使用较大的 Blob 大小，以优化解冻性能。 同时解冻多个小型 Blob 可能导致该时间延长。 目前有两个重新水合优先级，即"高"和"标准"，可通过[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或复制[Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作上的可选*x-ms-rehydrate 优先*属性进行设置。

* **标准优先级**：解冻请求将按其接收顺序处理，最长可能需要 15 个小时。
* **高优先级**：补液请求优先于标准请求，并可能在 1 小时内完成。 高优先级请求可能需要花费 1 小时以上，具体取决于 Blob 大小和当前需求。 保证高优先级请求优先于标准优先级请求。

> [!NOTE]
> 标准优先级是存档的默认解冻选项。 高优先级是更快的选项，其费用高于标准优先级解冻，通常保留用于紧急数据还原。

发起解冻请求后，将无法取消。 在补液过程中 *，x-ms-access 层*blob 属性将继续作为存档显示，直到将补液完成到联机层。 要确认补液状态和进度，可以调用[Get Blob 属性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)来检查 x ms*存档状态*和*x-ms-rehyd-rehyd 优先*blob 属性。 存档状态可以读取"补水待定到热"或"补水待定到冷却"，具体取决于补水目标层。 补水优先级将指示"高"或"标准"的速度。 完成后，将删除存档状态和补水优先属性，并且访问层 blob 属性将更新以反映所选的热层或冷层。
