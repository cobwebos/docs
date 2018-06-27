---
title: 在 Azure 数据工厂中创建基于事件的触发器 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中创建运行管道的触发器来响应事件。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 457983021034d83e0eed05bd91eae1ac30c046da
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296874"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>如何运行管道的触发器来响应事件

本文介绍了可以在数据工厂管道中创建的基于事件的触发器。

事件驱动的体系结构 (EDA) 是一种常见数据集成模式，其中涉及到事件的生成、检测、消耗和响应。 数据集成方案通常要求数据工厂客户基于事件触发管道。

## <a name="data-factory-ui"></a>数据工厂 UI

### <a name="create-a-new-event-trigger"></a>创建新的事件触发器

Azure 存储帐户中文件的到达或删除就是一个典型的事件。 你可以在数据工厂管道中创建一个用来响应此事件的触发器。

![创建新的事件触发器](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>选择事件触发器类型

只要文件到达存储位置并且创建了相应的 blob，此事件就会触发并运行数据工厂管道。 可以在数据工厂管道中创建触发器来响应 blob 创建事件、blob 删除事件或同时响应两者。

![选择触发器类型作为事件](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>配置事件触发器

使用“Blob 路径开头为”和“Blob路径结尾为”属性，你可以指定要为其接收事件的容器、文件夹和 Blob 的名称。 可以为“Blob 路径开头为”和“Blob路径结尾为”属性使用各种模式，如本文中后面的示例所示。 至少需要这些属性中的一个。

![配置事件触发器](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>JSON 架构

下表概述了与基于事件的触发器相关的架构元素：

| **JSON 元素** | **说明** | **类型** | **允许的值** | **必需** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | 存储帐户的 Azure 资源管理器资源 ID。 | 字符串 | Azure 资源管理器 ID | 是 |
| **events** | 导致此触发器触发的事件的类型。 | 数组    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是，任意组合。 |
| **blobPathBeginsWith** | Blob 路径必须使用为要触发的触发器提供的模式开头。 例如，“/records/blobs/december/”将仅针对 records 容器下的 december 文件夹中的 blob 使触发器触发。 | 字符串   | | 必须至少提供这些属性之一：blobPathBeginsWith、blobPathEndsWith。 |
| **blobPathEndsWith** | Blob 路径必须使用为要触发的触发器提供的模式结尾。 例如，“december/boxes.csv”将仅针对 december 文件夹中名为 boxes 的 blob 使触发器触发。 | 字符串   | | 必须至少提供这些属性之一：blobPathBeginsWith、blobPathEndsWith。 |

## <a name="examples-of-event-based-triggers"></a>基于事件的触发器的示例

本部分提供了基于事件的触发器的设置示例。

-   **Blob 路径开头为**('/containername/') – 接收该容器中任何 blob 的事件。
-   **Blob 路径开头为**('/containername/foldername') – 接收 containername 容器和 foldername 文件夹中任何 blob 的事件。
-   **Blob 路径开头为**('/containername/foldername/file.txt') – 接收 containername 容器下 foldername 文件夹中名为 file.txt 的 blob 的事件。
-   **Blob 路径结尾为**('file.txt') – 接收任何路径中名为 file.txt 的 blob 的事件。
-   **Blob 路径结尾为**('/containername/file.txt') – 接收 containername 容器下名为 file.txt 的 blob 的事件。
-   **Blob 路径结尾为**('foldername/file.txt') – 接收任何容器下的 foldername 文件夹中名为 file.txt 的 blob 的事件。

## <a name="next-steps"></a>后续步骤
有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#triggers)。
