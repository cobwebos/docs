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
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780228"
---
若要读取存档存储中的数据，必须先将 Blob 的层更改为热层或冷层。 此过程称为解除冻结，可能需要数小时才能完成。 建议将较大的 blob 大小用于最佳的解除冻结性能。 同时解冻多个小型 Blob 可能导致该时间延长。 目前有两个解除冻结优先级：高（预览）和标准版本，可通过[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)或[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob)操作上的可选*x 解除冻结*属性进行设置。

* **标准优先级**：解除冻结请求将按接收顺序进行处理，最长可能需要15小时。
* **高优先级（预览）** ：解除冻结请求将优先于标准请求，并可能在1小时内完成。 高优先级的时间可能超过1小时，具体取决于 blob 大小和当前需求。 高优先级请求保证优先于标准优先级请求。

> [!NOTE]
> 标准优先级是用于存档的默认解除冻结选项。 高优先级是更快的选项，此选项的成本高于标准优先级解除冻结，通常保留用于紧急数据还原情况。

解除冻结请求启动后，将无法取消。 在解除冻结期间，可以通过查看“存档状态”Blob 属性来确认该层是否已更改。 状态显示为“rehydrate-pending-to-hot”或“rehydrate-pending-to-cool”，具体取决于目标层。 完成后，“存档状态”属性会被删除，“访问层”Blob 属性会反映出新层是热层还是冷层。
