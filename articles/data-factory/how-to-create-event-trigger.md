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
ms.date: 07/23/2018
ms.author: douglasl
ms.openlocfilehash: 53ea7425f0497eca7c95ddefeaa09aa40259672b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216253"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>如何运行管道的触发器来响应事件

本文介绍了可以在数据工厂管道中创建的基于事件的触发器。

事件驱动的体系结构 (EDA) 是一种常见数据集成模式，其中涉及到事件的生成、检测、消耗和响应。 数据集成方案通常要求数据工厂客户基于事件触发管道。 数据工厂现在已与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)集成，后者可以根据事件触发管道。

有关此功能的 10 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 本文中所介绍的集成依赖于 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)。 请确保订阅已注册事件网格资源提供程序。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/resource-manager-supported-services.md#portal)。

## <a name="data-factory-ui"></a>数据工厂 UI

### <a name="create-a-new-event-trigger"></a>创建新的事件触发器

Azure 存储帐户中文件的到达或删除就是一个典型的事件。 你可以在数据工厂管道中创建一个用来响应此事件的触发器。

> [!NOTE]
> 此集成仅支持版本 2 存储帐户（常规用途）。

![创建新的事件触发器](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>配置事件触发器

使用“Blob 路径开头为”和“Blob路径结尾为”属性，你可以指定要为其接收事件的容器、文件夹和 Blob 的名称。 可以为“Blob 路径开头为”和“Blob路径结尾为”属性使用各种模式，如本文中后面的示例所示。 至少需要这些属性中的一个。

![配置事件触发器](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>选择事件触发器类型

只要文件到达存储位置并且创建了相应的 blob，此事件就会触发并运行数据工厂管道。 可以在数据工厂管道中创建触发器来响应 blob 创建事件、blob 删除事件或同时响应两者。

![选择触发器类型作为事件](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>将触发器属性映射至管道参数

当事件触发器为特定 blob 触发时，事件会将该 blob 的文件夹路径和文件名捕获至属性 `@triggerBody().folderPath` 和 `@triggerBody().fileName` 中。 若要在管道中使用这些属性的值，必须将这些属性映射至管道参数。 将这些属性映射至参数后，可以通过管道中的 `@pipeline.parameters.parameterName` 表达式访问由触发器捕获的值。

![将属性映射至管道参数](media/how-to-create-event-trigger/event-based-trigger-image4.png)

例如，在前面的屏幕截图中。 如果在存储帐户中创建了以 `.csv` 结尾的 blob 路径，则会触发该触发器。 所以，无论在存储帐户的任何位置创建了扩展名为 `.csv` 的 blob，`folderPath` 和 `fileName` 属性都会捕获这个新建的 blob 的位置。 例如，`@triggerBody().folderPath` 的值类似于 `/containername/foldername/nestedfoldername`，`@triggerBody().fileName` 的值类似于 `filename.csv`。 该示例将这些值映射至管道参数 `sourceFolder` 和 `sourceFile`。 它们可以分别作为 `@pipeline.parameters.sourceFolder` 和 `@pipeline.parameters.sourceFile` 用于整个管道。

## <a name="json-schema"></a>JSON 架构

下表概述了与基于事件的触发器相关的架构元素：

| **JSON 元素** | **说明** | 类型 | **允许的值** | **必需** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | 存储帐户的 Azure 资源管理器资源 ID。 | String | Azure 资源管理器 ID | 是 |
| **events** | 导致此触发器触发的事件的类型。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是，任意组合。 |
| **blobPathBeginsWith** | Blob 路径必须使用为要触发的触发器提供的模式开头。 例如，“/records/blobs/december/”将仅针对 records 容器下的 december 文件夹中的 blob 使触发器触发。 | String   | | 必须至少提供这些属性之一：blobPathBeginsWith、blobPathEndsWith。 |
| **blobPathEndsWith** | Blob 路径必须使用为要触发的触发器提供的模式结尾。 例如，“december/boxes.csv”将仅针对 december 文件夹中名为 boxes 的 blob 使触发器触发。 | String   | | 必须至少提供这些属性之一：blobPathBeginsWith、blobPathEndsWith。 |

## <a name="examples-of-event-based-triggers"></a>基于事件的触发器的示例

本部分提供了基于事件的触发器的设置示例。

-   **Blob 路径开头为**('/containername/') – 接收该容器中任何 blob 的事件。
-   **Blob 路径开头为**('/containername/blobs/foldername') - 接收 containername 容器和 foldername 文件夹中任何 blob 的事件。
-   **Blob 路径开头为**('/containername/blobs/foldername/file.txt') - 接收 containername 容器下 foldername 文件夹中名为 file.txt 的 blob 的事件。
-   **Blob 路径结尾为**('file.txt') – 接收任何路径中名为 file.txt 的 blob 的事件。
-   **Blob 路径结尾为**('/containername/blobs/file.txt') - 接收 containername 容器下名为 file.txt 的 blob 的事件。
-   **Blob 路径结尾为**('foldername/file.txt') – 接收任何容器下的 foldername 文件夹中名为 file.txt 的 blob 的事件。

> [!NOTE]
> 每当指定容器和文件夹、容器和文件或容器、文件夹和文件时，都必须包含路径的 `/blobs/` 段。

## <a name="next-steps"></a>后续步骤
有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#triggers)。
