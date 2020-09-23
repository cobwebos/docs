---
title: 将数据导入设计器
titleSuffix: Azure Machine Learning
description: 了解如何将数据从各种数据源导入 Azure 机器学习设计器中。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 15fca48327c46480546764be1b2ab40c1635e874
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985613"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>将数据导入 Azure 机器学习设计器

在本文中，你将了解如何在设计器中导入自己的数据，以创建自定义解决方案。 可以通过两种方式将数据导入到设计器中： 

* **Azure 机器学习数据集** - 在 Azure 机器学习中注册 [数据集](concept-data.md#datasets)，以启用可帮助你管理数据的高级功能。
* **导入数据模块** - 使用[导入数据](algorithm-module-reference/import-data.md)模块直接访问联机数据源中的数据。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>使用 Azure 机器学习数据集

建议使用[数据集](concept-data.md#datasets)将数据导入到设计器中。 注册数据集时，可以充分利用高级数据功能，例如[版本控制和跟踪](how-to-version-track-datasets.md)以及[数据监视](how-to-monitor-datasets.md)。

### <a name="register-a-dataset"></a>注册数据集

你可以[使用 SDK 以编程方式](how-to-create-register-datasets.md#datasets-sdk)注册现有数据集，也可以[直观地在 Azure 机器学习工作室](how-to-connect-data-ui.md#create-datasets)中注册现有数据集。

你还可以将任何设计器模块的输出注册为数据集。

1. 选择输出你要注册的数据的模块。

1. 在 "属性" 窗格中，选择 "**输出 + 日志**" "  >  **注册数据集**"。

    ![屏幕截图，其中显示了如何导航到“注册数据集”选项](media/how-to-designer-import-data/register-dataset-designer.png)

如果模块输出数据为表格格式，则必须选择将输出注册为 **文件数据集** 或 **表格数据集**。

 - **文件数据集** 将模块的输出文件夹注册为文件数据集。 Output 文件夹包含设计器在内部使用的数据文件和元文件。 如果要继续在设计器中使用已注册的数据集，请选择此选项。 

 - **表格数据集** 仅将模块的输出数据文件注册为表格数据集。 此格式可以由其他工具轻松使用，例如自动机器学习或 Python SDK。 如果计划在设计器外使用注册的数据集，请选择此选项。  



### <a name="use-a-dataset"></a>使用数据集

可在模块面板的 " **数据集**" 下找到已注册的数据集。 若要使用某个数据集，请将其拖放到管道画布上。 然后，将该数据集的输出端口连接到画布中的其他模块。 

![屏幕截图，其中显示了设计器面板中已保存数据集的位置](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> 设计器支持 [数据集版本控制](how-to-version-track-datasets.md)。 在数据集模块的属性面板中指定数据集版本。


## <a name="import-data-using-the-import-data-module"></a>使用“导入数据”模块导入数据

尽管我们建议使用数据集来导入数据，但也可以使用[导入数据](algorithm-module-reference/import-data.md)模块。 “导入数据”模块会跳过在 Azure 机器学习中注册数据集，并直接从[数据存储](concept-data.md#datastores) 或 HTTP URL 导入数据。

有关如何使用“导入数据”模块的详细信息，请参阅[导入数据引用页](algorithm-module-reference/import-data.md)。

> [!NOTE]
> 如果数据集包含的列过多，你可能会遇到以下错误：“由于大小限制，验证失败”。 若要避免这种情况，请[在数据集接口中注册数据集](how-to-connect-data-ui.md#create-datasets)。

## <a name="supported-sources"></a>受支持的源

本部分列出了设计器支持的数据源。 数据通过数据存储或[表格数据集](how-to-create-register-datasets.md#dataset-types)进入设计器。

### <a name="datastore-sources"></a>数据存储源
有关支持的数据存储源的列表，请参阅[访问 Azure 存储服务中的数据](how-to-access-data.md#supported-data-storage-service-types)。

### <a name="tabular-dataset-sources"></a>表格数据集源

设计器支持通过以下源创建的表格数据集：
 * 带分隔符的文件
 * JSON 文件
 * Parquet 文件
 * SQL 查询

## <a name="data-types"></a>数据类型

设计器在内部可以识别以下数据类型：

* 字符串
* Integer
* 小数
* Boolean
* Date

设计器使用一个内部数据类型在模块之间传递数据。 可使用[转换为数据集](algorithm-module-reference/convert-to-dataset.md)模块将数据显式转换为数据表格式。 接受非内部格式的任何模块都将在不提示的情况对数据进行转换，然后再将其传递给下一个模块。

## <a name="data-constraints"></a>数据约束

设计器中的模块受计算目标的大小限制。 对于较大的数据集，应使用较大的 Azure 机器学习计算资源。 有关 Azure 机器学习计算的详细信息，请参阅[什么是 Azure 机器学习中的计算目标？](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>访问虚拟网络中的数据

如果工作区位于虚拟网络中，则必须执行其他配置步骤，以便在设计器中实现数据的可视化。 有关如何在虚拟网络中使用数据存储和数据集的详细信息，请参阅 [在 Azure 虚拟网络中使用 Azure 机器学习 studio](how-to-enable-studio-virtual-network.md)。

## <a name="next-steps"></a>后续步骤

在本教程中学习设计器基础知识 [：通过设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)。
