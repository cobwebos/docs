---
title: 导入数据
titleSuffix: Azure Machine Learning
description: 了解如何将数据从各种数据源导入 Azure 机器学习设计器中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: 8cd49f9714746578ec701e22f9e6b0ccce772c6b
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942284"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>将数据导入 Azure 机器学习设计器（预览）

本文介绍如何在设计器中导入自己的数据，以创建自定义解决方案。 可以通过两种方式将数据导入到设计器中： 

* **Azure 机器学习数据集**-在 Azure 机器学习中注册[数据集](concept-data.md#datasets)，以启用可帮助你管理数据的高级功能。
* **导入数据模块**-使用 "[导入数据](algorithm-module-reference/import-data.md)" 模块直接访问联机数据源中的数据。

## <a name="use-azure-machine-learning-datasets"></a>使用 Azure 机器学习数据集

建议使用数据[集](concept-data.md#datasets)将数据导入到设计器中。 注册数据集时，可以充分利用高级数据功能，如[版本控制、跟踪](how-to-version-track-datasets.md)和[数据监视](how-to-monitor-datasets.md)。

### <a name="register-a-dataset"></a>注册数据集

可以[通过 SDK 以编程](how-to-create-register-datasets.md#use-the-sdk)方式注册现有数据集，也可以[在 Azure 机器学习 studio 中直观地](how-to-create-register-datasets.md#use-the-ui)进行注册。

你还可以将任何设计器模块的输出注册为数据集。

1. 选择输出要注册的数据的模块。

1. 在 "属性" 窗格中，选择 "**输出**" > "**注册数据集**"。

    ![显示如何导航到 Register Dataset 选项的屏幕截图](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>使用数据集

已注册的数据集可在模块面板的 "数据集 > **数据** **集"** 下找到。 若要使用数据集，请将其拖放到管道画布上。 然后，将该数据集的输出端口连接到调色板中的其他模块。

![显示设计器调色板中保存的数据集位置的屏幕截图](media/how-to-designer-import-data/use-datasets-designer.png)



> [!NOTE]
> 设计器当前仅支持处理[表格数据集](how-to-create-register-datasets.md#dataset-types)。 如果要使用[文件数据集](how-to-create-register-datasets.md#dataset-types)，请使用适用于 Python 和 R 的 Azure 机器学习 SDK。

## <a name="import-data-using-the-import-data-module"></a>使用 "导入数据" 模块导入数据

尽管我们建议使用数据集导入数据，但也可以使用 "[导入数据](algorithm-module-reference/import-data.md)" 模块。 导入数据模块会跳过在 Azure 机器学习中注册数据集，并直接从数据[存储](concept-data.md#datastores)或 HTTP URL 导入数据。

有关如何使用导入数据模块的详细信息，请参阅[导入数据引用页](algorithm-module-reference/import-data.md)。

[!NOTE]
> 如果数据包含多个列，则可能会遇到 "导入数据" 模块中的 "由于大小限制而失败"。 这是因为在编码后，列会排除 maxmium 模块参数长度。 在这种情况下，我们建议[在数据集 UI 中注册数据集](how-to-create-register-datasets.md#use-the-ui)，这样可以避免此错误。  

## <a name="supported-sources"></a>支持的源

本部分列出了设计器支持的数据源。 数据从数据存储或[表格数据集](how-to-create-register-datasets.md#dataset-types)进入设计器。

### <a name="datastore-sources"></a>数据存储源
有关支持的数据存储源的列表，请参阅[访问 Azure 存储服务中的数据](how-to-access-data.md#supported-data-storage-service-types)。

### <a name="tabular-dataset-sources"></a>表格数据集源

设计器支持通过以下源创建的表格数据集：
 * 带分隔符的文件
 * JSON 文件
 * Parquet 文件
 * SQL 查询

## <a name="data-types"></a>数据类型

设计器在内部识别以下数据类型：

* String
* Integer
* Decimal
* Boolean
* Date

设计器使用内部数据类型在模块之间传递数据。 您可以使用 "[转换为数据集](algorithm-module-reference/convert-to-dataset.md)" 模块将数据显式转换为数据表格式。 接受除内部格式以外的其他格式的任何模块在将数据传递到下一个模块之前都将进行无提示转换。

## <a name="data-constraints"></a>数据约束

设计器中的模块受计算目标的大小限制。 对于较大的数据集，应使用较大的 Azure 机器学习计算资源。 有关 Azure 机器学习计算的详细信息，请参阅[什么是 Azure 机器学习中的计算目标？](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>后续步骤

通过教程了解设计器的基础知识[：通过设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)。
