---
title: "能源需求时序预测 | Microsoft Docs"
description: "如何在 Azure Machine Learning Workbench 中运用机器学习来预测能源需求时序。"
services: machine-learning
documentationcenter: 
author: anta
manager: ireiter
editor: anta
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: 17903df93e11b8d1a5b9c6fbe5fd8e53302f45f4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="energy-demand-time-series-forecasting"></a>能源需求时序预测


时序预测是指预测观察时序中未来值的任务。 它是一个常见问题，并且已在许多行业中得到应用。 例如，零售公司需要预测将来的产品销售，从而可以有效安排其供应链来满足需求。 同样，物流公司需要评估其服务需求，以便提前规划劳动力要求和送货路线。 在许多情况下，不准确的预测造成的财务风险可能非常重大。 因此，预测通常是业务关键的活动。

本示例演示如何运用机器学习技术执行时序预测。 其中逐步讲解了过程建模的每个步骤，包括：
- 准备数据，以清理和格式化数据；
- 基于原始时序数据为机器学习模型创建特征；
- 训练各种机器学习模型；
- 通过基于留出测试数据集比较模型的性能来评估模型；
- 操作化最佳模型，以便能够通过 Web 服务使用它来按需生成预测。

Azure Machine Learning Workbench 能够为过程建模的每个步骤提供帮助： 
- 本示例演示可直接通过 Workbench 使用的 Jupyter Notebook 环境如何简化 Python 代码的开发。 可以使用 markdown 注释和图表向其他人更清楚地解释模型开发过程。 可以通过 Workbench 直接查看、编辑和执行这些 Notebook。
- 可将训练的模型保存和上传到 Blob 存储。 这可以帮助数据科学家跟踪训练的模型对象，确保这些对象得到保留，并可以根据需要进行检索。
- 执行 Python 脚本时可以记录指标，使数据科学家能够保留模型性能评分的记录。
- Workbench 生成记录指标的可自定义表，使数据科研家能够轻松比较模型性能指标。 图表会自动显示，因此可以轻松识别表现最佳的模型。
- 最后，本示例演示如何通过将训练的模型部署到实时 Web 服务来将其操作化。

## <a name="link-to-the-gallery-github-repository"></a>库 GitHub 存储库的链接
此实际方案的公共 GitHub 存储库包含此示例需要的所有材料，其中包括代码示例：

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>用例概述

本方案侧重于能源需求预测，其目标是预测电网的未来负载。 这是能源部门公司的一项关键业务运营，因为运营商需要在电网中消耗的能源与电网中供应的能源之间保持适当的均衡。 在电网中供应过多的电源可能会导致能源浪费或技术故障。 但是，如果供应的电源过少，则可能会导致提供过少的电源，则可能会导致断电，使客户无电可用。 通常，电网运营商可以做出短期决策来管理电网中供应的能源，并保持负载的均衡。 因此，准确的能源需求短期预测对于运营商自信做出这些决策至关重要。

本方案详细描述了机器学习能源需求预测解决方案的构造。 该解决方案是基于[纽约独立系统运营商 (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument)（运营纽约州电网）提供的公共数据集训练的。 该数据集包含纽约市在过去五年的每小时电源需求数据。 此外，还采用了 [darksky.net](https://darksky.net) 上提供的一个数据集，其中包含纽约市在同一时间段的每小时天气状况。

## <a name="prerequisites"></a>先决条件

- [Azure 帐户](https://azure.microsoft.com/free/)（有免费试用版可用）。
- 遵循[安装快速入门指南](./quickstart-installation.md)安装 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的副本，以安装程序并创建工作区。
- 本示例假设在本地装有 [Docker 引擎](https://www.docker.com/)的 Windows 10 上运行 Azure ML Workbench。 如果使用 macOS，操作说明大致相同。
- 已根据此[指南](./model-management-configuration.md)中所述，设置了装有本地开发环境的 Azure 机器学习操作化，并创建了模型管理帐户。
- 本示例要求将 Pandas 安装更新到 0.20.3 或更高版本，并安装 matplotlib。 在 Workbench 上的“文件”菜单中单击“打开命令提示符”，运行以下命令安装这些依赖项：

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

使用本示例作为模板，创建一个新项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“能源需求时序预测”，并选择模板
5.  单击“创建” 


## <a name="data-description"></a>数据说明

此示例提供了两个数据集，可使用 `1-data-preparation.ipynb` 笔记本下载：`nyc_demand.csv` 和 `nyc_weather.csv`。

**nyc_demand.csv** 包含纽约市 2012 至 2017 年每小时的能源需求值。 数据采用以下简单结构：

| timeStamp | demand |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

需求值以兆瓦时 (MWh) 为单位。 下面是 2017 年 7 月以 7 天为周期的能源需求图表：

![能源需求](./media/scenario-time-series-forecasting/energy_demand.png  "能源需求")

**nyc_weather.csv** 包含纽约市 2012 至 2017年的每小时天气值：

| timeStamp | precip | temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* 是降雨量百分比度量值。 *temp*（温度）值已经过调整，使所有值都在 [0, 100] 的间隔内。

## <a name="scenario-structure"></a>方案结构

首次在 Azure Machine Learning Workbench 中打开此项目时，请导航到左侧的“文件”窗格。 本示例随附以下代码文件：
- `1-data-preparation.ipynb` - 此 Jupyter Notebook 下载并处理数据，以准备将数据用于建模。 这是要运行的第一个 Notebook。
- `2-linear-regression.ipynb` - 此 Notebook 基于训练数据训练线性回归模型。
- `3-ridge.ipynb` - 训练岭回归模型。
- `4-ridge-poly2.ipynb` - 基于 2 阶多项式特征训练岭回归模型。
- `5-mlp.ipynb` - 训练多层感知器神经网络。
- `6-dtree.ipynb` - 训练决策树模型。
- `7-gbm.ipynb` - 训练渐变提升机模型。
- `8-evaluate-model.py` - 此脚本加载训练的模型，并使用它基于留出测试数据集进行预测。 此脚本生成模型评估指标，以便可以比较不同模型的性能。
- `9-forecast-output-exploration.ipynb` - 此 Notebook 针对生成机器学习模型生成的预测生成视觉对象。
- `10-deploy-model.ipynb` - 此 Notebook 演示可如何在实时 Web 服务中操作化已训练的预测模型。
- `evaluate-all-models.py` - 此脚本评估所有已训练的模型。 它提供一种替代方法用于单独针对每个训练的模型运行 `8-evaluate-model.py`。

### <a name="1-data-preparation-and-cleaning"></a>1.数据准备和清理

若要开始运行示例，请先在预览模式下单击 `1-data-preparation.ipynb` 打开 Notebook。 单击“启动 Notebook 服务器”，在计算机上启动 Jupyter Notebook 服务器和 Python 内核。 可以在 Workbench 内部运行 Notebook，也可以使用浏览器并导航到 [http://localhost:8888](http://localhost:8888) 来运行 Notebook。 请注意，必须将内核更改为 *PROJECT_NAME local*。 可以通过 Notebook 中“内核”菜单下面的“更改内核”进行此项更改。 按 ***Shift+Enter*** 运行单个 Notebook 单元中的代码，或单击“单元”菜单中的“全部运行”运行整个 Notebook。

Notebook 首先从 Azure 上托管的 Blob 存储容器中下载数据。 然后将数据存储在计算机上的 `AZUREML_NATIVE_SHARE_DIRECTORY` 中。 可以通过任何 Notebook 或者从 Workbench 运行的脚本访问此位置，因此，它是数据和已训练模型的适当存储位置。

下载数据后，Notebook 会通过填充时序中的间隙并通过内插填充缺失值来清理数据。 以这种方式清理时序数据可以最大程度地增多用于训练模型的数据量。

从清理的原始数据创建若干个模型特征。 这些特征可划分为两个组：

- **时间驱动的特征**派生自 *timestamp* 变量例，例如 *month*、*dayofweek* 和 *hour*。
- **滞后特征**是实际需求或温度值的时移值。 这些特征旨在捕获模型中连续时段之间的条件依赖关系。

然后，可在开发预测模型时使用生成的特征数据集。

### <a name="2-model-development"></a>2.模型开发

第一种建模方法可能是简单的线性回归模型。 `2-linear-regression.ipynb` Notebook 演示如何针对未来的能源需求训练线性回归预测模型。 具体而言，将训练模型，以提前当前时段 (*t*) 一小时 (*t + 1*) 预测能源需求。 但是，可以在测试时以递归方式应用此“单步”模型，以针对未来时段 *t+n* 生成预测。

若要训练模型，需创建一个预测管道，该管道涉及以下三个不同的步骤：

- **数据转换**：输入数据所需的格式可能根据机器学习算法的不同而异。 在此情况下，线性回归模型要求对分类变量进行独热编码。
- **交叉验证例程个**：机器学习模型通常有一个或多个需要通过试验优化的超参数。 交叉验证可用于查找最佳的参数值集。 根据数据集的不同折数反复训练和评估模型。 最佳参数是取交叉验证折数平均值后可实现最佳模型性能的参数。 `2-linear-regression.ipynb` 中更详细介绍了此过程。
- **训练最终模型**：使用最佳超参数集基于整个数据集训练模型。

我们在编号 2-7 的 Notebook 中包含了 6 个不同的模型系列。 每个 Notebook 训练不同的模型，并将其存储在 `AZUREML_NATIVE_SHARE_DIRECTORY` 位置。 训练针对此方案开发的所有模型后，我们可以根据下一部分所述对模型进行评估和比较。

### <a name="3-model-evaluation-and-comparison"></a>3.模型的评估和比较

可以使用训练的模型针对未来的时段做出预测。 最好在基于留出测试数据集评估这些模型。 通过基于相同的不可见数据集评估不同的模型，可以公平地比较模型的性能并确定最佳模型。 在此方案中，我们以递归方式应用训练的模型，以预测未来的多个时间步长。 具体而言，我们要提前当前小时数 (*t*) 最多六个小时 (*t+6*) 生成预测。 这些预测是针对每个时段观察到的实际需求评估的。

若要评估模型，请通过 Workbench 中的“文件”窗格打开 `8-evaluate-model.py` 脚本。 检查“运行配置”是否设置为 **local**，然后在“参数”字段中键入模型名称。 模型名称需与启动用于训练模型的 Notebook 时设置的 *model_name* 变量完全匹配。 例如，输入“linear_regression”可评估已训练的线性回归模型。 或者，在训练所有模型后，可以通过运行 `evaluate-all-models.py`，以一条命令评估所有这些模型。 若要运行此脚本，请从 Workbench 打开命令提示符并执行以下命令：

```
python evaluate-all-models.py
```
`8-evaluate-model.py` 脚本执行以下操作：

- 从磁盘加载训练的模型管道
- 基于测试数据集做出预测
- 计算并记录模型性能指标
- 将测试数据集预测保存到 `AZUREML_NATIVE_SHARE_DIRECTORY` 供稍后检查和分析
- 将训练的模型管道保存到 *outputs* 文件夹。

> [!Note]
> 将模型保存到 *outputs* 文件夹会自动将模型对象复制到与试验帐户关联的 Azure Blob 存储帐户。 这意味着，即使更改了计算机或计算上下文，也始终可以从 Blob 检索已保存的模型对象。

导航到“运行历史记录”窗格并单击 `8-evaluate-model.py`。 此时会看到显示多个模型性能指标的图表：

- **ME**：预测的平均错误。 这可以解释为预测平均偏差。
- **MPE**：[平均百分比误差](https://en.wikipedia.org/wiki/Mean_percentage_error)（ME 表示实际需求的百分比）
- **MSE**：[均方误差](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE**：均方根误差（MSE 的平方根）
- **MAPE**：[平均绝对百分比误差](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE**：[对称平均绝对百分比误差](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base**：基线预测的 MAPE，其中的预测等于最后一个已知需求值。
- **MdRAE**：中间相对绝对误差。 预测误差与基线预测误差的中间比。 如果值小于 1，则表示预测性能比基线更好。

上述所有指标指 *t+1* 预测。 除上述指标以外，还会看到一组带有 *_horizon* 后缀的相应指标。 这是基于时段 *t+1* 到时段 *t+6* 预测范围中所有时段求平均值后的指标。

如果指标未显示在图表区域中，请单击右上角的齿轮符号。 确保感兴趣的模型性能指标已被选中。 指标也会显示在图表下面的表中。 同样，可单击齿轮符号自定义此表。 按性能指标为表排序可识别最佳模型。 如果想要了解从时段 *t+1* 到 *t+6* 的预测性能如何变化，请单击表中模型对应的条目。 显示整个预测时段的 MAPE、MPE 和 MdRAE 指标的图表显示在“视觉对象”的下面。

评估预测模型时，将输出预测可视化可能很有作用。 这可以帮助数据科学家确定生成的预测看上去是否现实。 此外，举例而言，如果在特定的时段预测性能不佳，则这还有助于识别预测中的问题。 `9-forecast-output-exploration.ipynb` Notebook 会生成针对测试数据集生成的预测的视觉对象。

### <a name="4-deployment"></a>4.部署

将最佳模型部署为实时 Web 服务可将其操作化。 然后，在新数据可用时，可调用此 Web 服务按需生成预测。 在此方案中，需要每隔一小时生成新的预测，用于预测后续一小时的能源需求。 若要执行此任务，可以部署一个 Web 服务，用于将给定小时时段的特征数组用作输入，并返回预测的需求作为输出。

在本示例中，Web 服务已部署到 Windows 10 计算机。 请确保完成这篇适用于操作化 CLI 的[入门指南](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md)中规定的本地部署先决条件步骤。 设置本地环境和模型管理帐户后，请运行 `10-deploy-model.ipynb`  Notebook 来部署 Web 服务。

## <a name="conclusion"></a>结束语

本示例演示了如何生成端到端时序预测解决方案用于预测能源需求。 可将本示例中探讨的许多原理延伸到其他预测方案和行业。

本方案演示了 Azure Machine Learning Workbench 如何通过 Jupyter Notebook 环境和指标日志记录等功能，帮助数据科学家开发真实解决方案。 本示例还引导读者了解如何使用 Azure 机器学习操作化 CLI 来操作化和部署模型。 部署后，Web 服务 API 可让开发人员或数据工程师将预测模型集成到更广泛的数据管道中。
