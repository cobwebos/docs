---
title: include 文件
description: include 文件
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780228"
---
若要读取存档存储中的数据，必须先将 Blob 的层更改为热层或冷层。 此过程称为解冻，可能需要多个小时才能完成。 建议使用较大的 Blob 大小，以优化解冻性能。 同时解冻多个小型 Blob 可能导致该时间延长。 目前有两种解冻优先级：高（预览版功能）和“标准”，可以在[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 操作中通过可选的 *x-ms-rehydrate-priority* 属性进行设置。

* **标准优先级**：补液请求按收到的顺序处理，可能需要长达 15 小时。
* **高优先级（预览）：** 补液请求将优先于标准请求，并可能在 1 小时内完成。 高优先级请求可能需要花费 1 小时以上，具体取决于 Blob 大小和当前需求。 保证高优先级请求优先于标准优先级请求。

> [!NOTE]
> 标准优先级是存档的默认解冻选项。 高优先级是更快的选项，其费用高于标准优先级解冻，通常保留用于紧急数据还原。

发起解冻请求后，将无法取消。 在补液期间，您可以检查*存档状态*blob 属性以确认层是否已更改。 状态显示为“rehydrate-pending-to-hot”或“rehydrate-pending-to-cool”，具体取决于目标层。 完成后，“存档状态”属性会被删除，“访问层”Blob 属性会反映出新层是热层还是冷层。**
