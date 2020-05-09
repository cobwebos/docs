---
title: 转换数据
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习设计器中转换数据，以创建自己的数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 05/04/2020
ms.openlocfilehash: 5296ac54cab403ef78b3e8bd32fe5ebe6ea43119
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842872"
---
# <a name="transform-data-in-azure-machine-learning-designer-preview"></a>在 Azure 机器学习设计器中转换数据（预览）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何在 Azure 机器学习设计器中转换和保存数据集，以便为机器学习准备自己的数据。

你将使用示例[成人人口收入二元分类](sample-designer-datasets.md)数据集来准备两个数据集：一个数据集包含来自美国的成人人口统计信息，另一个包含来自非美国成人的人口统计信息。

在本文中，学习如何：

1. 转换数据集以准备用于定型的数据集。
1. 将生成的数据集导出到数据存储。
1. 查看结果。

本操作说明是如何重新训练[设计器模型](how-to-retrain-designer.md)一文的先决条件。 在这篇文章中，您将学习如何使用转换的数据集来使用管道参数训练多个模型。

## <a name="transform-a-dataset"></a>转换数据集

在本部分中，将了解如何导入示例数据集并将数据拆分为 US 和非美国数据集。 有关如何将自己的数据导入设计器的详细信息，请参阅[如何导入数据](how-to-designer-import-data.md)。

### <a name="import-data"></a>导入数据

使用以下步骤导入示例数据集。

1. 登录到 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，选择要使用的工作区。

1. 中转到设计器。 选择**易于使用的称之为 fakes.prebuild 模块**以创建新的管道。

1. 选择要运行管道的默认计算目标。

1. 管道画布左侧是数据集和模块的控制板。 选择“数据集”。**** 然后查看 "**示例**" 部分。

1. 将**成人人口收入二元分类**数据集拖放到画布上。

1. 选择 "**成人人口收入**数据集" 模块。

1. 在显示在画布右侧的详细信息窗格中，选择 "**输出**"。

1. 选择 "可视化" 图标 ![可视化图标](media/how-to-designer-transform-data/visualize-icon.png).

1. 使用 "数据预览" 窗口浏览数据集。 请特别注意 "本机国家" 列值。

### <a name="split-the-data"></a>拆分数据

在本部分中，您将使用[拆分数据模块](algorithm-module-reference/split-data.md)来标识和拆分 "本国国家" 列中包含 "美国" 的行。 

1. 在画布左侧的模块面板中，展开 "**数据转换**" 部分，然后找到 "**拆分数据**" 模块。

1. 将 "**拆分数据**" 模块拖放到画布上，并将模块拖放到数据集模块下方。

1. 将数据集模块连接到**拆分数据**模块。

1. 选择“拆分数据”  模块。

1. 在画布右侧的 "模块详细信息" 窗格中，将 "**拆分模式**" 设置为 "**正则表达式**"。

1. 输入**正则表达式**： `\"native-country" United-States`。

    **正则表达式**模式测试值的单个列。 有关拆分数据模块的详细信息，请参阅相关[算法模块参考页](algorithm-module-reference/split-data.md)。

管道应如下所示：

![显示如何配置管道和拆分数据模块的屏幕截图](media/how-to-designer-transform-data/split-data.png).


## <a name="save-the-datasets"></a>保存数据集

现在，已将管道设置为拆分数据，接下来需要指定保存数据集的位置。 在此示例中，使用 "**导出数据**" 模块将数据集保存到数据存储。 有关数据存储的详细信息，请参阅[连接到 Azure 存储服务](how-to-access-data.md)

1. 在画布左侧的模块面板中，展开 "**数据输入和输出**" 部分，然后找到 "**导出数据**" 模块。

1. 将两个**导出数据**模块拖放到**拆分数据**模块下。

1. 将**拆分数据**模块的每个输出端口连接到不同的**导出数据**模块。

    管道应如下所示：

    ![显示如何连接导出数据模块的屏幕截图](media/how-to-designer-transform-data/export-data-pipeline.png).

1. 选择连接到**拆分数据**模块最*左侧*端口的 "**导出数据**" 模块。

    **拆分数据**模块的输出端口的顺序。 第一个输出端口包含正则表达式为 true 的行。 在这种情况下，第一个端口包含基于美国收入的行，第二个端口包含非美国收入的行。

1. 在画布右侧的 "模块详细信息" 窗格中，设置以下选项：
    
    **数据存储类型**： Azure Blob 存储

    **数据存储**：选择现有数据存储，或选择 "新建数据存储" 立即创建一个。

    **路径**：`/data/us-income`

    **文件格式**： csv

    > [!NOTE]
    > 本文假设你有权访问注册到当前 Azure 机器学习工作区的数据存储。 有关如何设置数据存储的说明，请参阅[连接到 Azure 存储服务](how-to-access-data.md#azure-machine-learning-studio)。

    如果没有数据存储，可以立即创建一个。 例如，本文会将数据集保存到与工作区关联的默认 blob 存储帐户。 它会将这些数据集保存`azureml`到名`data`为的新文件夹中的容器中。

1.  选择连接到**拆分数据**模块的最*右侧*端口的 "**导出数据**" 模块。

1. 在画布右侧的 "模块详细信息" 窗格中，设置以下选项：
    
    **数据存储类型**： Azure Blob 存储

    **数据存储**：选择与上面相同的数据存储

    **路径**：`/data/non-us-income`

    **文件格式**： csv

1. 确认连接到**拆分数据**的左端口的 "**导出数据**" 模块具有**路径** `/data/us-income`。

1. 确认连接到正确端口的 "**导出数据**" 模块具有**路径** `/data/non-us-income`。

    管道和设置应如下所示：
    
    ![显示如何配置导出数据模块的屏幕截图](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>提交运行

现在，管道已设置为拆分和导出数据，请提交管道运行。

1. 在画布顶部选择“提交”。 

1. 在 "**设置管道运行**" 对话框中，选择 "**新建**" 以创建试验。

    试验将逻辑分组到相关的管道运行。 如果以后运行此管道，则应使用相同的试验进行日志记录和跟踪。

1. 提供描述性试验名称，如 "拆分-人口统计数据"。

1. 选择“提交”。 

## <a name="view-results"></a>查看结果

管道运行完毕后，可以通过导航到 Azure 门户中的 blob 存储来查看结果。 您还可以查看**拆分数据**模块的中间结果以确认您的数据已正确拆分。

1. 选择“拆分数据”  模块。

1. 在画布右侧的模块详细信息窗格中，选择“输出 + 日志”。  

1. 选择 " ![**结果 dataset1**"](media/how-to-designer-transform-data/visualize-icon.png)旁边的 "直观显示" 图标。 

1. 验证 "本机国家/地区" 列是否只包含值 "美国"。

1. 选择 " ![**结果 dataset2**"](media/how-to-designer-transform-data/visualize-icon.png)旁边的 "直观显示" 图标。 

1. 验证 "本机国家/地区" 列不包含值 "美国"。

## <a name="clean-up-resources"></a>清理资源

若要继续学习本操作方法的第2部分， [Azure 机器学习](how-to-retrain-designer.md)请跳过此部分。

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本文介绍了如何转换数据集并将其保存到已注册的数据存储中。

转到此操作方法系列的下一部分，通过[Azure 机器学习设计器](how-to-retrain-designer.md)与重新定型模型一起使用转换后的数据集和管道参数来训练机器学习模型。
