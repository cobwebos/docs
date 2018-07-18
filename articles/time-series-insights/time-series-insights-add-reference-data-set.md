---
title: 如何向 Azure 时序见解环境添加参考数据集
description: 本文介绍了如何添加参考数据集来增强 Azure 时序见解环境中的数据。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.openlocfilehash: 962c3c8d439884cacf010b0bbcab03bd216dbd43
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294194"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>使用 Azure 门户为时序见解环境创建引用数据集

本文介绍了如何向 Azure 时序见解环境添加引用数据集。 引用数据可用于联接到源数据以增强值。

参考数据集是各种项的集合，这些项对事件源中的事件进行了增强。 时序见解入口引擎将事件源中的每个事件与参考数据集中的相应数据行联接到一起。 然后即可使用此增强的事件进行查询。 该联接基于在参考数据集中定义的主键列。

参考数据不以追溯方式进行联接。 这意味着在配置并上传数据后，只会将当前和将来的入口数据匹配并联接到参考数据集。

## <a name="add-a-reference-data-set"></a>添加引用数据集

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 查找现有时序见解环境。 在 Azure 门户左侧的菜单中，单击“所有资源”。 选择时序见解环境。

3. 选择“概述”页面。 找到**时序见解资源管理器 URL** 并打开此链接。  

   查看 TSI 环境的资源管理器。

4. 在 TSI 资源管理器中展开环境选择器。 选择活动的环境。 选择资源管理器页面右上角的参考数据图标。

   ![添加参考数据](media/add-reference-data-set/add_reference_data.png)

5. 选择“+ 添加数据集”按钮以开始添加新数据集。

   ![添加数据集](media/add-reference-data-set/add_data_set.png)

6. 在“新建参考数据集”页面上，选择数据格式： 
   - 选择“CSV”表示以逗号分隔的数据。 第一行被视为标题行。 
   - 选择“JSON 数组”表示 javascript 对象表示法 (JSON) 格式的数据。

   ![选择数据格式。](media/add-reference-data-set/add_data.png)

7. 使用下列两种方法之一提供数据：
   - 将数据粘贴到文本编辑器中。 然后，选择“分析参考数据”按钮。
   - 选择“选择文件”按钮来从本地文本文件添加数据。 

   例如，粘贴 CSV 数据：![粘贴的 CSV 数据](media/add-reference-data-set/csv_data_pasted.png)

   例如，粘贴 JSON 数组数据：![粘贴 JSON 数据](media/add-reference-data-set/json_data_pasted.png)

   如果分析数据值时发生错误，则错误将以红色显示在页面底部，例如 `CSV parsing error, no rows extracted`。

8. 在成功分析数据后，会显示一个数据网格，其中显示了表示数据的行和列。  查看数据网格以确保正确性。

   ![添加参考数据](media/add-reference-data-set/parse_data.png)

9. 检查每个列以查看采用的数据类型，并根据需要更改数据类型。  在列标题中选择数据类型符号：**#** 表示双精度（数字数据）、**T|F** 表示布尔值，**Abc** 表示字符串。

   ![在列标题上选择数据类型。](media/add-reference-data-set/choose_datatypes.png)

10. 如果需要，重命名列标题。 键列名称是联接到事件源中的对应属性所必需的。 请确保参考数据键列名称与传入数据中的事件名称完全匹配，包括区分大小写。 非键列名称用来使用对应的参考数据值增强传入数据。

11. 单击“添加行”或“添加列”以根据需要添加更多的参考数据值。

12. 根据需要在“筛选行...”字段中键入一个值来查看特定行。 筛选器适合用来查看数据，但在上传数据时不会应用。
 
13. 通过填写数据网格上方的“数据集名称”字段为数据集命名。

   ![为数据集命名。](media/add-reference-data-set/name_reference_dataset.png)

14. 通过选择数据网格上方的下拉列表，提供数据集中的**主键**列。

   ![选择键列。](media/add-reference-data-set/set_primary_key.png)

   （可选）选择 **+** 按钮来将一个辅键列添加为复合主键。 如果需要撤消选择，请从下拉列表中选择空值来删除辅键。

15.  若要上传数据，请选择“上传行”按钮。

   ![上载](media/add-reference-data-set/upload_rows.png)

   页面会确认完成的上传并显示消息“已成功上传数据集”。

## <a name="next-steps"></a>后续步骤
* 以编程方式[管理引用数据](time-series-insights-manage-reference-data-csharp.md)。
* 如需完整的 API 引用，请参阅[引用数据 API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api)文档。
