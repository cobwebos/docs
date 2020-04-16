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
ms.openlocfilehash: d697fb8afe3e92dfe54eb5d89a2ef59425cb0cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414916"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>如何运行管道的触发器来响应事件
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍了可以在数据工厂管道中创建的基于事件的触发器。

事件驱动的体系结构 (EDA) 是一种常见数据集成模式，其中涉及到事件的生成、检测、消耗和响应。 数据集成方案通常要求数据工厂客户根据 Azure 存储帐户中的文件到达或删除等事件触发管道。 数据工厂现在已与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)集成，后者可以根据事件触发管道。

有关此功能的 10 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 本文中所介绍的集成依赖于 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)。 请确保订阅已注册事件网格资源提供程序。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

## <a name="data-factory-ui"></a>数据工厂 UI

本节介绍如何在 Azure 数据工厂用户界面中创建事件触发器。

1. 转到**创作画布**

1. 在左下角，单击 **"触发器"** 按钮

1. 单击 **+ 新建**，这将打开创建触发器侧导航

1. 选择触发器类型**事件**

    ![创建新的事件触发器](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. 从 Azure 订阅下拉列表中选择存储帐户，或使用其存储帐户资源 ID 手动选择存储帐户。 选择要在哪个容器上发生的事件。 容器选择是可选的，但请注意，选择所有容器可能会导致大量事件。

   > [!NOTE]
   > 事件触发器目前仅支持 Azure 数据存储库存储第 2 代和通用版本 2 存储帐户。 由于 Azure 事件网格限制，Azure 数据工厂仅支持每个存储帐户最多 500 个事件触发器。

1. **Blob 路径以"Blob"路径开头****，Blob 路径以属性结尾**，允许您指定要为其接收事件的容器、文件夹和 Blob 名称。 事件触发器至少需要定义其中一个属性。 可以为“Blob 路径开头为”**** 和“Blob路径结尾为”**** 属性使用各种模式，如本文中后面的示例所示。

    * **Blob 路径从：** Blob 路径必须以文件夹路径开头。 有效值包括 `2018/` 和 `2018/april/shoes.csv`。 如果未选择容器，则无法选择此字段。
    * **Blob 路径以：** blob 路径必须以文件名或扩展名结尾。 有效值包括 `shoes.csv` 和 `.csv`。 容器和文件夹名称是可选的，但指定时，它们必须由段`/blobs/`分隔。 例如，名为"订单"的容器的值可以为`/orders/blobs/2018/april/shoes.csv`。 要在任何容器中指定文件夹，请省略前导的"/"字符。 例如，`april/shoes.csv`将在文件夹中名为`shoes.csv`"april"的任何文件中触发任何容器中名为"april"的事件。 

1. 选择触发器是响应**Blob 创建**的事件 **、Blob 已删除**的事件还是两者兼而有之。 在指定的存储位置中，每个事件都将触发与触发器关联的数据工厂管道。

    ![配置事件触发器](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. 选择触发器是否忽略零字节的 Blob。

1. 配置触发器后，单击"**下一步：数据预览**"。 此屏幕显示事件触发器配置匹配的现有 blob。 请确保您有特定的筛选器。 配置过于宽泛的筛选器可以匹配大量创建/删除的文件，并可能显著影响您的成本。 验证筛选条件后，单击 **"完成**"。

    ![事件触发数据预览](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. 要将管道附加到此触发器，请转到管道画布，然后单击"**添加触发器**"并选择 **"新建/编辑**"。 当侧导航出现时，单击 **"选择触发器..."** 下拉列表并选择您创建的触发器。 单击 **"下一步：数据预览**"以确认配置正确，然后**单击"下一步**"以验证数据预览是否正确。

1. 如果管道具有参数，则可以在触发器运行参数侧导航上指定它们。 事件触发器将 blob 的文件夹路径和文件名捕获到 属性`@triggerBody().folderPath`和`@triggerBody().fileName`中。 若要在管道中使用这些属性的值，必须将这些属性映射至管道参数。 将这些属性映射至参数后，可以通过管道中的 `@pipeline().parameters.parameterName` 表达式访问由触发器捕获的值。 完成后，单击"**完成**"。

    ![将属性映射至管道参数](media/how-to-create-event-trigger/event-based-trigger-image4.png)

在前面的示例中，触发器配置为在容器示例数据中的文件夹事件测试中创建以 .csv 结尾的 Blob 路径时触发。 **文件夹 Path**和**文件名**属性捕获新 Blob 的位置。 例如，当 MoviesDB.csv 添加到路径示例数据/事件测试时`@triggerBody().folderPath`，其值 和`sample-data/event-testing``@triggerBody().fileName`的值为`moviesDB.csv`。 这些值在示例中`sourceFolder`映射到管道参数`sourceFile`，并且可以`@pipeline().parameters.sourceFolder``@pipeline().parameters.sourceFile`分别在整个管道中使用。

## <a name="json-schema"></a>JSON 架构

下表概述了与基于事件的触发器相关的架构元素：

| **JSON 元素** | **说明** | 类型  | **允许的值** | **必需** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | 存储帐户的 Azure 资源管理器资源 ID。 | 字符串 | Azure 资源管理器 ID | 是 |
| **事件** | 导致此触发器触发的事件的类型。 | Array    | Microsoft.Storage.BlobCreated、Microsoft.Storage.BlobDeleted | 是的，这些值的任意组合。 |
| **blobPathBeginsWith** | blob 路径必须使用为要触发的触发器提供的模式开头。 例如，`/records/blobs/december/` 只会触发 `records` 容器下 `december` 文件夹中的 blob 触发器。 | 字符串   | | 必须为其中至少一个属性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **blobPathEndsWith** | blob 路径必须使用为要触发的触发器提供的模式结尾。 例如，`december/boxes.csv` 只会触发 `december` 文件夹中名为 `boxes` 的 blob 的触发器。 | 字符串   | | 必须为其中至少一个属性提供值：`blobPathBeginsWith` 或 `blobPathEndsWith`。 |
| **忽略空Blobs** | 零字节 blob 是否会触发管道运行。 默认情况下，这设置为 true。 | Boolean | True 或 False | 否 |

## <a name="examples-of-event-based-triggers"></a>基于事件的触发器的示例

本部分提供了基于事件的触发器的设置示例。

> [!IMPORTANT]
> 每当指定容器和文件夹、容器和文件或容器、文件夹和文件时，都必须包含路径的 `/blobs/` 段，如以下示例所示。 对于**blobPath 开始，** 数据工厂 UI 将自动`/blobs/`在触发器 JSON 中的文件夹和容器名称之间添加。

| properties | 示例 | 描述 |
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
