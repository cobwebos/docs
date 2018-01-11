---
title: "适用于 Azure 机器学习的 Azure 数据源向导 | Microsoft Docs"
description: "对 AML Workbench 数据源向导进行说明"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: ff0159facd693b83230c731eb7e76f0a9495fdf2
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="data-source-wizard"></a>数据源向导 #

借助数据源向导，无需使用代码即可快速轻松地将数据集载入 Azure ML Workbench。 此向导还可用于选择数据集的采样策略。 

## <a name="step-1-trigger-the-data-source-wizard"></a>步骤 1：触发数据源向导 ## 

若要使用数据源向导将数据载入项目， 请选择搜索框旁边的 **+** 按钮，然后选择“添加数据源”。 

![添加数据源](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>步骤 2：选择数据存储位置 ##
首先，指定数据目前采用的存储方式。 它可存储在平面文件、目录、parquet 文件、Excel 文件或数据库中。 有关详细信息，请参阅[受支持的数据源](data-prep-appendix2-supported-data-sources.md)。

![步骤 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>步骤 3：选择数据文件 ##
对于文件/目录，指定文件路径。 从下拉列表中选择数据所在的位置，它可以是本地文件路径或 Azure Blob 存储。 

通过键入相关信息指定路径，或单击“浏览...” 按钮浏览路径。 可浏览目录或一个或多个文件。

单击“完成”接受剩余步骤的默认值，或单击“下一步”继续执行下一步。


![步骤 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>步骤 4：选择文件参数 ##

数据源向导可自动检测文件类型、分隔符和编码。 它还会显示数据的示例。 用户还可手动更改其中任何参数。 

![步骤 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>步骤 5：设置列的数据类型 ##

数据源向导会自动检测数据集的列的数据类型。 如果缺少某个数据类型，或如果想要强制使用某个数据类型，可手动更改数据类型。 **SAMPLE OUTPUT DATA** 列显示数据的示例。

![步骤 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>步骤 6：选择数据采样策略 ##

可为数据集指定一个或多个采样策略，并选择一个策略作为活动策略。 默认策略为加载前 10000 行。 可通过单击工具栏中的“编辑”按钮来编辑此示例，或通过单击“+新建”添加新策略。 我们目前支持的策略包括

-     前 xx 行
-     随机行数
-     随机行百分比
-     整个文件

用户可根据需要指定任意数量的采样策略，但准备数据时仅可将一个策略设置为活动策略。 可通过选择策略并在工具栏中单击“设置为活动”来将任何策略设置为活动策略。

根据数据来源，某些采样策略可能不会受到支持。 有关采样的详细信息，请参阅[此文档](data-prep-user-guide.md)中的采样部分 

![步骤 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>步骤 7：路径列处理 ##

如果文件路径包含重要数据，可选择将其作为数据集的第一列包含在内。 如果要载入多个文件，这很有用。 否则可选择不包含。

![步骤 7](media/data-source-wizard/step6.png)

单击“完成”后，项目中会添加新的数据源。 在数据视图中，可在“数据源”组下找到它；在**文件视图**中，可看到它以 .dsource 文件的形式显示。
