---
title: 转换数据
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习设计器中转换数据以创建自己的数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.custom: designer
ms.openlocfilehash: 718023424834ffca7a026dc5b3d35b8fb11fe633
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644299"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>在 Azure 机器学习设计器（预览版）中转换数据
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何在 Azure 机器学习设计器中转换和保存数据集，以便可以为机器学习准备好自己的数据。

你将使用示例 [Adult Census Income Binary Classification](sample-designer-datasets.md) 数据集来准备两个数据集：一个数据集包含仅来自美国的成年人口信息，另一个数据集包含来自非美国成人的人口信息。

在本文中，学习如何：

1. 转换数据集以准备用于训练。
1. 将生成的数据集导出到数据存储。
1. 查看结果。

此操作说明是[如何重新训练设计器模型](how-to-retrain-designer.md)文章的先决条件。 在该文章中，你将了解如何使用转换的数据集，通过管道参数训练多个模型。

## <a name="transform-a-dataset"></a>转换数据集

在此部分中，你会了解如何导入示例数据集并将数据拆分为美国和非美国数据集。 有关如何将自己的数据导入设计器中的详细信息，请参阅[如何导入数据](how-to-designer-import-data.md)。

### <a name="import-data"></a>导入数据

使用下列步骤导入示例数据集。

1. 登录到 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，选择要使用的工作区。

1. 转到设计器。 选择 Easy-to-use-prebuild 模块以创建新管道。

1. 选择默认计算目标以运行管道。

1. 管道画布左侧是数据集和模块的控制板。 选择“数据集”。 然后，查看“示例”部分。

1. 将“Adult Census Income Binary classification”数据集拖放到画布上。

1. 选择“Adult Census Income”数据集模块。

1. 在画布右侧出现的详细信息窗格中，选择“输出”。

1. 选择可视化图标 ![可视化图标](media/how-to-designer-transform-data/visualize-icon.png)。

1. 使用数据预览窗口浏览数据集。 请特别注意“native-country”列值。

### <a name="split-the-data"></a>拆分数据

在此部分中，会使用[“拆分数据”模块](algorithm-module-reference/split-data.md)标识和拆分在“native-country”列中包含“United-States”的行。 

1. 在画布左侧的模块控制板中，展开“数据转换”部分并找到“拆分数据”模块。 

1. 将“拆分数据”模块拖动到画布上，并将模块放置在数据集模块下。

1. 将数据集模块连接到“拆分数据”模块。

1. 选择“拆分数据”模块。

1. 在画布右侧的模块详细信息窗格中，将“拆分模式”设置为“正则表达式”。 

1. 输入“正则表达式”：`\"native-country" United-States`。

    “正则表达式”模式对值测试单列。 有关“拆分数据”模块的详细信息，请参阅相关[算法模块参考页面](algorithm-module-reference/split-data.md)。

管道应如下所示：

![显示如何配置管道和“拆分数据”模块的屏幕截图](media/how-to-designer-transform-data/split-data.png)。


## <a name="save-the-datasets"></a>保存数据集

现在管道已设置为拆分数据，便需要指定保存数据集的位置。 对于此示例，请使用“导出数据”模块将数据集保存到数据存储。 有关数据存储的详细信息，请参阅[连接到 Azure 存储服务](how-to-access-data.md)

1. 在画布左侧的模块控制板中，展开“数据输入和输出”部分并找到“导出数据”模块。 

1. 将两个“导出数据”模块拖放到“拆分数据”模块下。

1. 将“拆分数据”模块的每个输出端口都连接到不同的“导出数据”模块。 

    管道应如下所示：

    ![显示如何连接“导出数据”模块的屏幕截图](media/how-to-designer-transform-data/export-data-pipeline.png)。

1. 选择连接到“拆分数据”模块的最左侧端口的“导出数据”模块。

    输出端口的顺序对于“拆分数据”模块十分重要。 第一个输出端口包含正则表达式为 true 的行。 在此例中，第一个端口包含基于美国的收入的行，第二个端口包含基于美国以外的收入的行。

1. 在画布右侧的模块详细信息窗格中，设置以下选项：
    
    数据存储类型：Azure Blob 存储

    **数据存储**：选择现有数据存储，或选择“新建数据存储”以立即创建一个。

    路径：`/data/us-income`

    文件格式：csv

    > [!NOTE]
    > 本文假设你有权访问注册到当前 Azure 机器学习工作区的数据存储。 有关如何设置数据存储的说明，请参阅[连接到 Azure 存储服务](how-to-access-data.md#azure-machine-learning-studio)。

    如果没有数据存储，则可以立即创建一个。 例如，本文会将数据集保存到与工作区关联的默认 blob 存储帐户。 它会将数据集保存到名为 `data` 的新文件夹中的 `azureml` 容器。

1.  选择连接到“拆分数据”模块的最右侧端口的“导出数据”模块。

1. 在画布右侧的模块详细信息窗格中，设置以下选项：
    
    数据存储类型：Azure Blob 存储

    **数据存储**：选择与上面相同的数据存储

    路径：`/data/non-us-income`

    文件格式：csv

1. 确认连接到“拆分数据”左侧端口的“导出数据”模块的“路径”为 `/data/us-income`。

1. 确认连接到右侧端口的“导出数据”模块的“路径”为 `/data/non-us-income`。

    管道和设置应如下所示：
    
    ![显示如何配置“导出数据”模块的屏幕截图](media/how-to-designer-transform-data/us-income-export-data.png)。

### <a name="submit-the-run"></a>提交运行

现在，管道已设置为拆分和导出数据，可提交管道运行。

1. 在画布顶部选择“提交”。

1. 在“设置管道运行”对话框中，选择“新建”以创建试验。

    试验将相关管道运行以逻辑方式分组在一起。 如果在将来运行此管道，则应使用相同试验进行日志记录和跟踪。

1. 提供描述性试验名称，如“split-census-data”。

1. 选择“提交”。

## <a name="view-results"></a>查看结果

管道运行完成之后，可以通过在 Azure 门户中导航到 blob 存储来查看结果。 还可以查看“拆分数据”模块的中间结果，以确认数据已正确拆分。

1. 选择“拆分数据”模块。

1. 在画布右侧的模块详细信息窗格中，选择“输出 + 日志”。 

1. 选择“结果数据集 1”旁的可视化图标 ![可视化图标](media/how-to-designer-transform-data/visualize-icon.png)。 

1. 验证“native-country”列是否只包含值“United-States”。

1. 选择“结果数据集 2”旁的可视化图标 ![可视化图标](media/how-to-designer-transform-data/visualize-icon.png)。 

1. 验证“native-country”列是否不包含值“United-States”。

## <a name="clean-up-resources"></a>清理资源

如果要继续学习此操作说明的第 2 部分[使用 Azure 机器学习设计器重新训练模型](how-to-retrain-designer.md)，请跳过此部分。

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了如何转换数据集并将它保存到已注册的数据存储中。

继续学习此操作说明系列的下一个部分[使用 Azure 机器学习设计器重新训练模型](how-to-retrain-designer.md)，以使用转换的数据集和管道参数训练机器学习模块。
