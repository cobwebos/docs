---
title: 使用 Azure 机器学习设计器运行批量预测（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用设计器训练模型并设置批量预测管道。 将管道部署为参数化 Web 服务，该服务可从任何 HTTP 库触发。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: a44ac821271cc07a790c380287391f41650ced19
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276741"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器运行批量预测
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本操作方法指南中，你将学习如何使用设计器训练模型并设置批量预测管道和 Web 服务。 通过批量预测可以对大型数据集上已训练的模型进行连续且按需评分，选择性地将其配置为可从任何 HTTP 库触发的 Web 服务。 

有关使用 SDK 设置批量评分服务的说明，请参阅随附的[操作方法](how-to-run-batch-predictions.md)。

在本操作指南中，你将学习如何执行以下任务：

> [!div class="checklist"]
> * 在管道中创建基本 ML 试验
> * 创建参数化批量推理管道
> * 手动或从 REST 终结点管理和运行管道

## <a name="prerequisites"></a>先决条件

1. 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

1. 创建[工作区](tutorial-1st-experiment-sdk-setup.md)。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。

本操作方法假定在设计器中生成简单管道的基本知识。 有关设计器的引导式简介，请完成[教程](tutorial-designer-automobile-price-train-score.md)。 

## <a name="create-a-pipeline"></a>创建管道

若要创建批量推理管道，首先需要机器学习试验。 若要创建一个，请导航到工作区中的“设计器”  选项卡，然后通过选择“易用的预建模块”  选项来创建新的管道。

![设计器主页](media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

下面是用于演示目的的简单机器学习模型。 数据是从 Azure 开放数据集糖尿病数据创建的已注册数据集。 若要从 Azure 开放数据集注册数据集，请参阅[操作方法部分](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)。 数据拆分为训练集和验证集，提升决策树已训练且已评分。 必须至少运行一次管道，才能创建推断管道。 单击“运行”  按钮以运行管道。

![创建简单试验](media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>创建批量推理管道

现在已经运行了管道，“运行”和“发布”旁边有一个新选项，名为“创建推理管道”    。 单击下拉列表并选择“批量推理管道”  。

![创建批量推理管道](media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

结果为默认批量推理管道。 这包括用于原始管道试验设置的节点、用于对原始数据进行评分的节点，以及用于根据原始管道对原始数据进行评分的节点。

![默认批量推理管道](media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

可以添加其他节点来更改批量推断过程的行为。 在此示例中，添加一个节点，用于在评分之前从输入数据中随机采样。 创建“分区和示例”  节点并将其放置在原始数据和评分节点之间。 接下来，单击“分区和示例”  节点以获取对设置和参数的访问权限。

![新建节点](media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

“采样率”参数控制随机采样占原始数据集的百分比  。 这是一个对频繁调整非常有用的参数，因此可将其用作管道参数。 管道参数可在运行时更改，并且可在从 REST 终结点重新运行管道时在有效负载对象中指定。 

若要将此字段用作管道参数，请单击该字段上方的省略号，然后单击“添加到管道参数”  。 

![示例设置](media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

接下来，为参数提供一个名称和默认值。 该名称将用于标识参数，并在 REST 调用中指定。

![管道参数](media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>部署批量推断管道

现在可开始部署管道了。 单击“部署”  按钮，这将打开用于设置终结点的接口。 单击下拉列表并选择“新 PipelineEndpoint”  。

![管道部署](media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

为终结点提供一个名称和可选说明。 接近底部，你将看到使用默认值 0.8 配置的 `sample-rate` 参数。 准备就绪后，单击“部署”  。

![设置终结点](media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>管理终结点 

部署完成后，请转到“终结点”  选项卡，然后单击刚刚创建的终结点的名称。

![终结点链接](media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

此屏幕显示特定终结点下的所有已发布管道。 单击推断管道。

![推理管道](media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

“管道详细信息”页显示管道的详细运行历史记录和连接字符串信息。 单击“运行”  按钮以创建管道的手动运行。

![管道详细信息](media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

在运行设置中，可以提供运行的说明，并更改任何管道参数的值。 这次，按照 0.9 的采样率重新运行推断管道。 单击“运行”  以运行管道。

![管道运行](media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

 “使用”选项卡包含用于重新运行管道的 REST 终结点。 若要进行 rest 调用，需要 OAuth 2.0 持有者类型身份验证标头。 请参阅以下[教程部分](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)，以详细了解如何设置工作区的身份验证并进行参数化 REST 调用。

## <a name="next-steps"></a>后续步骤

按照设计器[教程](tutorial-designer-automobile-price-train-score.md)训练和部署回归模型。
