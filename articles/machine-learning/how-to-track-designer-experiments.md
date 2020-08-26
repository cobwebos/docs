---
title: '设计器中的日志指标 (预览) '
titleSuffix: Azure Machine Learning
description: 监视 Azure ML 设计器试验。 使用 "执行 Python 脚本" 模块启用日志记录，并查看工作室中记录的结果。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 0d8d9f598da41b2bd39369e063200f5445ba740a
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554754"
---
# <a name="enable-logging-in-azure-machine-learning-designer-preview-pipelines"></a>在 Azure 机器学习设计器中启用日志记录 (预览) 管道
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何将日志记录代码添加到设计器管道。 你还将了解如何使用 Azure 机器学习 studio web 门户查看这些日志。

有关使用 SDK 创作体验记录度量值的详细信息，请参阅[监视 AZURE ML 试验运行和指标](how-to-track-experiments.md)。

## <a name="enable-logging-with-execute-python-script"></a>使用执行 Python 脚本启用日志记录

使用 "__执行 Python 脚本__" 模块启用设计器管道中的日志记录。 虽然您可以记录此工作流的任何值，但从 "__评估模型__" 模块记录指标以跟踪各个运行的模型性能特别有用。

下面的示例演示如何使用 "评估模型" 和 "执行 Python 脚本" 模块记录两个定型模型的平均平方误差。

1. 将__执行 Python 脚本__模块连接到 "__评估模型__" 模块的输出。

    ![将“执行 Python 脚本”模块连接到“评估模型”模块](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. 将以下代码粘贴到__执行 Python 脚本__代码编辑器中，以便记录定型模型的平均绝对错误。 您可以使用类似的模式来记录设计器中的任何其他值：

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```
    
此代码使用 Azure 机器学习 Python SDK 来记录值。 它使用 get_context ( # A1 获取当前运行的上下文。 然后，它通过运行 ( # A1 方法将值记录到该上下文。 它使用 `parent` 将值记录到父管道运行，而不是模块运行。

有关如何使用 Python SDK 记录值的详细信息，请参阅[在 AZURE ML 定型运行中启用日志记录](how-to-track-experiments.md)。

## <a name="view-logs"></a>查看日志

管道运行完成后，可以在 "试验" 页中看到*Mean_Absolute_Error* 。

1. 导航到 "**试验**" 部分。
1. 选择试验。
1. 选择要查看的试验中的 "运行"。
1. 选择“指标”。

    ![在工作室中查看运行度量值](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何在设计器中使用日志。 有关后续步骤，请参阅以下相关文章：

* 了解如何排查设计器管道问题，请参阅[调试 & ML 管道故障排除](how-to-debug-pipelines.md#azure-machine-learning-designer-preview)。
* 了解如何使用 Python SDK 来记录 SDK 创作体验中的指标，请参阅[在 AZURE ML 定型运行中启用日志记录](how-to-track-experiments.md)。
