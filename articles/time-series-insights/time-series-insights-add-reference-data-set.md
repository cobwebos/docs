---
title: 如何向环境添加参考数据集 - Azure 时序见解 | Microsoft Docs
description: 本文介绍了如何添加参考数据集来增强 Azure 时序见解环境中的数据。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/05/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 010f7b908ceb9c8d07208db1312d8af8d4e27051
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838512"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>使用 Azure 门户为时序见解环境创建引用数据集

本文介绍了如何向 Azure 时序见解环境添加引用数据集。 引用数据可用于联接到源数据以增强值。

参考数据集是各种项的集合，这些项对事件源中的事件进行了增强。 时序见解入口引擎将事件源中的每个事件与参考数据集中的相应数据行联接到一起。 然后即可使用此增强的事件进行查询。 该联接基于在参考数据集中定义的主键列。

参考数据不以追溯方式进行联接。 因此，在配置并上传数据后，只会将当前和将来的流入数据匹配并联接到参考数据集。

## <a name="video"></a>视频

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>了解时序见解的参考数据模型。</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>添加引用数据集

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 查找现有的 Azure 时序见解环境。 在 Azure 门户左侧的菜单中，选择“所有资源”****。 选择时序见解环境。

1. 选择“概述”页面。**** 展开页面顶部附近的“概要”**** 部分，找到“时序见解资源管理器 URL”**** 并打开链接。  

   [![展开概要部分](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   查看时序见解环境的资源管理器。

1. 在时序见解资源管理器中展开环境选择器。 选择活动的环境。 选择资源管理器页面右上角的参考数据图标。

   [![添加参考数据](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. 选择“+ 添加数据集”**** 按钮以开始添加新数据集。

   [![添加数据集](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. 在“新建参考数据集”**** 页面上，选择数据格式：

   - 选择“CSV”**** 表示以逗号分隔的数据。 第一行被视为标题行。
   - 选择**JSON 数组**作为 JavaScript 对象表示法（JSON）格式的数据。

   [![选择数据格式。](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. 使用下列两种方法之一提供数据：

   - 将数据粘贴到文本编辑器中。 然后，选择“分析参考数据”**** 按钮。
   - 选择“选择文件”**** 按钮来从本地文本文件添加数据。

   例如，粘贴 csv 数据： [ ![粘贴的 csv 数据](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   例如，粘贴 json 数组数据： [ ![粘贴 json 数据](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   如果分析数据值时发生错误，则错误将以红色显示在页面底部，例如 `CSV parsing error, no rows extracted`。

1. 在成功分析数据后，会显示一个数据网格，其中显示了表示数据的行和列。 查看数据网格以确保正确性。

   [![查看引用数据](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. 查看每个列以了解所采用的数据类型，并根据需要更改数据类型。  在列标题中选择数据类型符号： **#** 对于双精度（数值数据）， **T |** 对于 boolean，为 F; 对于 string，则为**Abc** 。

   [![在列标题上选择数据类型。](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. 如果需要，重命名列标题。 键列名称是联接到事件源中的对应属性所必需的。

   > [!IMPORTANT]
   > 请确保参考数据键列名称与传入数据中的事件名称完全匹配，包括区分大小写。 非键列名称用来使用对应的参考数据值增强传入数据。

1. 根据需要在“筛选行...”**** 字段中键入一个值来查看特定行。 筛选器适合用来查看数据，但在上传数据时不会应用。

1. 通过填写数据网格上方的“数据集名称”**** 字段为数据集命名。

    [![为数据集命名。](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. 通过选择数据网格上方的下拉列表，提供数据集中的**主键**列。

    [![选择键列。](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    （可选）选择 + 按钮，将一个辅键列添加为复合主键********。 如果需要撤消选择，请从下拉列表中选择空值来删除辅键。

1. 若要上传数据，请选择“上传行”**** 按钮。

    [![上传行并确认数据。](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    页面会确认完成的上传并显示消息“已成功上传数据集”。****

    > [!WARNING]
    > 参考数据集之间共享的列或属性将显示上传错误“属性名重复”****。 此错误不会阻止参考数据集的成功上传。 合并共享重复的属性名的行即可删除该错误。

1. 选择“添加行”****、“批量导入行”**** 或“添加列”****，根据需要添加更多的参考数据值。

    [![添加行、大容量导入行或添加列。](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > 任何与另一行共享唯一键的行，其列都将由最后添加的共享该唯一键的行替代。

   > [!NOTE]
   > 添加的行不必为矩形 - 与参考数据集中的其他项相比，它们的列可以更少、更大或不同******。

## <a name="next-steps"></a>后续步骤

* 以编程方式[管理引用数据](time-series-insights-manage-reference-data-csharp.md)。

* 有关完整的 API 参考，请阅读[参考数据 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) 文档。
