---
title: 在 Azure 数据工厂中创建基于事件的触发器
description: 了解如何在 Azure 数据工厂中创建运行管道的触发器来响应事件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: 10f0079f47e5d2fd99b358fcc5cfb4c80aa9bd91
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84508890"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>如何运行管道的触发器来响应事件
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了可以在数据工厂管道中创建的基于事件的触发器。

事件驱动的体系结构 (EDA) 是一种常见数据集成模式，其中涉及到事件的生成、检测、消耗和响应。 数据集成方案通常要求数据工厂客户根据事件（例如，文件抵达了 Azure 存储帐户，或者在存储帐户中删除了文件）触发管道。 数据工厂现在已与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)集成，后者可以根据事件触发管道。

有关此功能的 10 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 本文中所介绍的集成依赖于 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)。 请确保订阅已注册事件网格资源提供程序。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。 你必须能够执行 Microsoft.EventGrid/eventSubscriptions/* 操作。 此操作属于 EventGrid EventSubscription 参与者内置角色。

## <a name="data-factory-ui"></a>数据工厂 UI

本部分介绍如何在 Azure 数据工厂用户界面中创建事件触发器。

1. 转到“创作画布”

1. 在左下角单击“触发器”按钮

1. 单击“+ 新建”会打开“创建触发器”边侧导航栏

1. 选择触发器类型“事件”

    ![创建新的事件触发器](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. 从 Azure 订阅下拉列表中选择你的存储帐户，或使用其存储帐户资源 ID 手动选择。 选择希望事件在哪个容器中发生。 容器是可选的，但是请注意，选择所有容器可能会导致生成大量的事件。

   > [!NOTE]
   > 事件触发器目前仅支持 Azure Data Lake Storage Gen2 和“常规用途”版本 2 存储帐户。 你必须至少具有存储帐户的“所有者”访问权限。  由于 Azure 事件网格的限制，对于每个存储帐户，Azure 数据工厂最多仅支持 500 个事件触发器。

1. 使用“Blob 路径开头为”和“Blob路径结尾为”属性，可以指定要为其接收事件的容器、文件夹和 Blob 的名称。  事件触发器要求至少定义其中的一个属性。 可以为“Blob 路径开头为”和“Blob路径结尾为”属性使用各种模式，如本文中后面的示例所示。

    * **Blob 路径开头为：** Blob 路径必须以文件夹路径开头。 有效值包括 `2018/` 和 `2018/april/shoes.csv`。 如果未选择容器，则无法选择此字段。
    * **Blob 路径结尾为：** Blob 路径必须以文件名或扩展名结尾。 有效值包括 `shoes.csv` 和 `.csv`。 容器和文件夹名称是可选的，但如果指定，它们必须由 `/blobs/` 段分隔。 例如，名为“orders”的容器可以使用 `/orders/blobs/2018/april/shoes.csv` 值。 若要指定任何容器中的某个文件夹，请省略前导“/”字符。 例如，`april/shoes.csv` 将会针对任何容器中名为“april”的文件夹内名为 `shoes.csv` 的任何文件触发事件。 

1. 选择触发器是要响应“已创建 Blob”事件、“已删除 Blob”事件，还是同时响应这两者。  在指定的存储位置中，每个事件将触发与触发器关联的数据工厂管道。

    ![配置事件触发器](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. 选择触发器是否忽略零字节的 Blob。

1. 配置触发器后，单击“下一步:数据预览”。 此屏幕显示事件触发器配置匹配的现有 Blob。 请确保使用具体化的筛选器。 如果配置的筛选器过于宽泛，可能会匹配已创建/删除的大量文件，并可能会严重影响成本。 验证筛选条件后，单击“完成”。

    ![事件触发器数据预览](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. 若要将管道附加到此触发器，请转到管道画布，然后单击“添加触发器”并选择“新建/编辑”。  出现边侧导航栏时，单击“选择触发器...”下拉列表，然后选择创建的触发器。 单击“下一步:数据预览”确认配置是否正确，然后单击“下一步”验证数据预览是否正确。

1. 如果管道具有参数，则你可以在触发器运行参数边侧导航栏中指定这些参数。 事件触发器将 Blob 的文件夹路径和文件名捕获到属性 `@trigger().outputs.body.folderPath` 和 `@trigger().outputs.body.fileName` 中。 若要在管道中使用这些属性的值，必须将这些属性映射至管道参数。 将这些属性映射至参数后，可以通过管道中的 `@pipeline().parameters.parameterName` 表达式访问由触发器捕获的值。 完成后，单击“完成”。

    ![将属性映射至管道参数](media/how-to-create-event-trigger/event-based-trigger-image4.png)

在前面的示例中，触发器配置为在容器 sample-data 中的文件夹 event-testing 内创建以 .csv 结尾的 Blob 路径时激发。 **folderPath** 和 **fileName** 属性捕获新 Blob 的位置。 例如，将 MoviesDB.csv 添加到路径 sample-data/event-testing 时，`@trigger().outputs.body.folderPath` 的值为 `sample-data/event-testing`，`@trigger().outputs.body.fileName` 的值为 `moviesDB.csv`。 示例中的这些值将映射到管道参数 `sourceFolder` 和 `sourceFile`，这两个参数在整个管道中分别可以用作 `@pipeline().parameters.sourceFolder` 和 `@pipeline().parameters.sourceFile`。

## <a name="json-schema"></a>JSON 架构

下表概述了与基于事件的触发器相关的架构元素：

| **JSON 元素** | **说明** | **类型** | **允许的值** | **必需** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **作用域** | 存储帐户的 Azure 资源管理器资源 ID。 | String | Azure 资源管理器 ID | 是 |
| **events** | 导致此触发器触发的事件的类型。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是的，这些值的任意组合。 |
| **blobPathBeginsWith** | blob 路径必须使用为要触发的触发器提供的模式开头。 例如，`/records/blobs/december/` 只会触发 `records` 容器下 `december` 文件夹中的 blob 触发器。 | String   | | 必须为其中至少一个属性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **blobPathEndsWith** | blob 路径必须使用为要触发的触发器提供的模式结尾。 例如，`december/boxes.csv` 只会触发 `december` 文件夹中名为 `boxes` 的 blob 的触发器。 | String   | | 必须为其中至少一个属性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **ignoreEmptyBlobs** | 零字节 Blob 是否触发管道运行。 默认情况下，此元素设置为 true。 | 布尔 | true 或 false | 否 |

## <a name="examples-of-event-based-triggers"></a>基于事件的触发器的示例

本部分提供了基于事件的触发器的设置示例。

> [!IMPORTANT]
> 每当指定容器和文件夹、容器和文件或容器、文件夹和文件时，都必须包含路径的 `/blobs/` 段，如以下示例所示。 对于 **blobPathBeginsWith**，数据工厂 UI 将自动在触发器 JSON 中的文件夹与容器名称之间添加 `/blobs/`。

| properties | 示例 | 说明 |
|---|---|---|
| **Blob 路径开头** | `/containername/` | 接收容器中任何 blob 事件。 |
| **Blob 路径开头** | `/containername/blobs/foldername/` | 接收 `containername` 容器和 `foldername` 文件夹中的任何 blob 事件。 |
| **Blob 路径开头** | `/containername/blobs/foldername/subfoldername/` | 此外可以引用一个子文件夹。 |
| **Blob 路径开头** | `/containername/blobs/foldername/file.txt` | 接收 `containername` 容器下的 `foldername` 文件夹中名为 `file.txt` 的 blob 事件。 |
| **Blob 路径结尾** | `file.txt` | 接收任何路径中名为 `file.txt` 的 blob 事件。 |
| **Blob 路径结尾** | `/containername/blobs/file.txt` | 接收容器 `containername` 下名为 `file.txt` 的 blob 事件。 |
| **Blob 路径结尾** | `foldername/file.txt` | 接收任何容器下 `foldername` 文件夹中名为 `file.txt` 的 blob 事件。 |

## <a name="next-steps"></a>后续步骤
有关触发器的详细信息，请参阅[管道执行和触发器](concepts-pipeline-execution-triggers.md#trigger-execution)。
