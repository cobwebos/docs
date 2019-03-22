---
title: 创建自动化机器学习试验
titleSuffix: Azure Machine Learning service
description: 自动化机器学习将为你选择一种算法，并生成随时可用于部署的模型。 了解可用于配置自动化机器学习试验的选项。
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: 79d013e5836555547cbf254bb25c06add0a717e1
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295379"
---
# <a name="configure-automated-machine-learning-experiments"></a>配置自动化机器学习试验

自动化机器学习将自动选择算法和超参数，并生成随时可用于部署的模型。 可以使用多个选项来配置自动化机器学习试验。 本指南介绍如何定义各种配置设置。

若要查看自动化机器学习试验的示例，请参阅[教程：使用自动化机器学习训练分类模型](tutorial-auto-train-models.md)或[使用云中的自动化机器学习训练模型](how-to-auto-train-remote.md)。

自动化机器学习提供的配置选项：

* 选择试验类型：分类、回归或预测
* 数据源、格式和提取数据
* 选择计算目标：本地或远程
* 自动化机器学习试验设置
* 运行自动化机器学习试验
* 探索模型指标
* 注册和部署模型

## <a name="select-your-experiment-type"></a>选择试验类型
在开始试验之前，应确定要解决的机器学习问题类型。 自动化机器学习支持分类、回归和预测任务类型。

自动化机器学习功能已推出正式版，不过，**预测功能仍以公共预览版提供。**

在自动化和优化过程中，自动化机器学习支持以下算法。 用户不需要指定算法。

分类 | 回归 | 预测
|-- |-- |--
[逻辑回归](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[朴素贝叶斯](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[C 支持向量分类 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)|[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)
[线性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[决策树](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|


## <a name="data-source-and-format"></a>数据源和格式
自动化机器学习支持驻留在本地桌面上或云中（例如 Azure Blob 存储）的数据。 可将数据读取成 scikit-learn 支持的数据格式。 可将数据读取成：
* Numpy 数组 X（特征）和 y（目标变量，也称为标签）
* Pandas 数据帧

示例：

*   Numpy 数组

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Pandas 数据帧

    ```python
    import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>在远程计算中提取用于运行试验的数据

如果使用远程计算来运行试验，必须将数据提取包装在单独的 Python 脚本 `get_data()` 中。 此脚本在运行自动化机器学习试验的远程计算中运行。 `get_data` 消除了通过网络为每个迭代提取数据的需要。 如果不使用 `get_data`，则在远程计算中运行时，试验将会失败。

下面是 `get_data` 的示例：

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

在 `AutoMLConfig` 对象中，指定 `data_script` 参数并提供 `get_data` 脚本文件的路径，如下所示：

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` 脚本可以返回：

密钥 | Type |    与以下对象互斥 | 描述
---|---|---|---
X | Pandas 数据帧或 Numpy 数组 | data_train、label、columns |  用于训练的所有特征
y | Pandas 数据帧或 Numpy 数组 |   label   | 用于训练的标签数据。 对于分类，应是一个整数数组。
X_valid | Pandas 数据帧或 Numpy 数组   | data_train、label | （可选）用于验证的所有特征。 如果未指定，则在 train 与 validate 之间拆分 X
y_valid |   Pandas 数据帧或 Numpy 数组 | data_train、label | （可选）用于验证的标签数据。 如果未指定，则在 train 与 validate 之间拆分 y
sample_weight | Pandas 数据帧或 Numpy 数组 |   data_train、label、columns| （可选）每个样本的权重值。 需要为数据点分配不同的权重时使用
sample_weight_valid | Pandas 数据帧或 Numpy 数组 | data_train、label、columns |    （可选）每个验证样本的权重值。 如果未指定，则在 train 与 validate 之间拆分 sample_weight
data_train |    Pandas 数据帧 |  X、y、X_valid、y_valid |    用于训练的所有数据（特征+标签）
label | 字符串  | X、y、X_valid、y_valid |  data_train 中的哪个列表示标签
列 | 字符串数组  ||  （可选）用于特征的列的白名单
cv_splits_indices   | 整数数组 ||  （可选）用于拆分数据以进行交叉验证的索引列表

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>使用 DataPrep SDK 加载和准备数据
自动化机器学习试验支持使用 DataPrep SDK 加载和转换数据。 使用 SDK 可以

>* 使用分析参数推理（编码、分隔符和标头）从多种文件类型进行加载
>* 在文件加载时使用推理进行类型转换
>* 支持 MS SQL Server 和 Azure Data Lake Storage 连接
>* 使用表达式添加列
>* 估算缺失值
>* 按示例派生列
>* 筛选
>* 自定义 Python 转换

若要了解 DataPrep SDK，请参阅[如何准备要建模的数据](how-to-load-data.md)一文。
下面是使用 DataPrep SDK 加载数据的示例。
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>训练和验证数据

可以通过 get_data() 或直接在 `AutoMLConfig` 方法中指定单独的训练集和验证集。

## <a name="cross-validation-split-options"></a>交叉验证拆分选项

### <a name="k-folds-cross-validation"></a>K 折交叉验证

使用 `n_cross_validations` 设置指定交叉验证的数目。 训练数据集将随机拆分为大小相等的 `n_cross_validations` 折。 在每个交叉验证轮次，某个折将用于验证剩余折上训练的模型。 重复此过程 `n_cross_validations` 次，直到每个折作为验证集使用了一次。 将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>蒙特卡洛交叉验证（也称为 重复随机子采样）

使用 `validation_size` 指定应该用于验证的训练数据集百分比，并使用 `n_cross_validations` 指定交叉验证的数目。 在每个交叉验证轮次，将随机选择 `validation_size` 大小的子集来验证基于剩余数据训练的模型。 最后，将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。

### <a name="custom-validation-dataset"></a>自定义验证数据集

如果随机拆分不可接受，请使用自定义验证数据集（通常是时序数据或不平衡的数据）。 可以指定自己的验证数据集。 将会根据指定的验证数据集而不是随机数据集来评估模型。

## <a name="compute-to-run-experiment"></a>用于运行试验的计算环境

接下来，确定要在何处训练模型。 自动化机器学习训练试验可在以下计算选项中运行：
*   本地台式机或便携式计算机等本地计算机  – 如果数据集较小，并且仍处于探索阶段，则通常使用此选项。
*   云中的远程计算机 – [Azure 机器学习托管计算](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets)是一个托管服务，可用于在 Azure 虚拟机群集上训练机器学习模型。

有关包含本地和远程计算目标的示例 Notebook，请参阅 [GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/automl)。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>配置试验设置

可以使用多个选项来配置自动化机器学习试验。 通过实例化 `AutoMLConfig` 对象来设置这些参数。

示例包括：

1.  分类试验使用加权 AUC 作为主要指标，每次迭代的最大时间为 12,000 秒，试验在完成 50 次迭代和 2 个交叉验证折后结束。

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  下面是设置为在 100 次迭代后结束的回归试验示例，每次迭代最长持续 600 秒，并完成 5 个交叉验证折。

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

有三个不同的 `task` 参数值，可确定要应用的算法的列表。  使用 `whitelist` 或 `blacklist` 参数可进一步修改迭代，从而包含或排除可用算法。
* 分类
    * LogisticRegression
    * SGD
    * MultinomialNaiveBayes
    * BernoulliNaiveBayes
    * SVM
    * LinearSVM
    * KNN
    * DecisionTree
    * RandomForest
    * ExtremeRandomTrees
    * LightGBM
    * GradientBoosting
    * TensorFlowDNN
    * TensorFlowLinearClassifier
* 回归
    * ElasticNet
    * GradientBoosting
    * DecisionTree
    * KNN
    * LassoLars
    * SGD 
    * RandomForest
    * ExtremeRandomTree
    * LightGBM
    * TensorFlowLinearRegressor
    * TensorFlowDNN
* 预测
    * ElasticNet
    * GradientBoosting
    * DecisionTree
    * KNN
    * LassoLars
    * SGD 
    * RandomForest
    * ExtremeRandomTree
    * LightGBM
    * TensorFlowLinearRegressor
    * TensorFlowDNN

有关参数的完整列表，请参阅 [AutoMLConfig 类](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)。  

## <a name="data-pre-processing-and-featurization"></a>数据预处理和特征化

如果使用 `preprocess=True`，系统会自动执行以下数据预处理步骤：
1.  删除较大的基数或者无差异的特征
    * 从训练和验证集中删除不包含任何有用信息的特征。 这包括缺少所有值的特征、在所有行中使用相同值的特征，或者包含极高基数（例如哈希、ID 或 GUID）的特征。
1.  缺失值插补
    *   对于数字特征，将在列中插补采用平均值的缺失值。
    *   对于分类特征，将插补采用最常用值的缺失值。
1.  生成其他特征
    * 对于日期时间特征：年、月、日、星期、年日期、季、年周、小时、分钟、秒。
    * 对于文本特征：基于单词单元语法的字词频率、双元语法和三元语法、计数向量器。
1.  转换和编码
    * 唯一值很少的数字特征将转换为分类特征。
    * 根据分类特征的基数，执行标签编码或（哈希）独热编码。

## <a name="run-experiment"></a>运行试验

提交试验以运行和生成模型。 将 `AutoMLConfig` 传递给 `submit` 方法以生成模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>首先在新的计算机上安装依赖项。  最长可能需要在 10 分钟后才会显示输出。
>将 `show_output` 设置为 `True` 可在控制台上显示输出。


## <a name="explore-model-metrics"></a>探索模型指标
如果在笔记本中操作，可以在小组件或内联单元中查看结果。 有关更多详细信息，请参阅[跟踪和评估模型](how-to-track-experiments.md#view-run-details)。


### <a name="classification-metrics"></a>分类指标
在分类任务的每次迭代中保存以下指标。

|主要指标|描述|计算|其他参数
--|--|--|--|
AUC_macro| AUC 是接收方操作特性曲线下面的区域。 Macro 是每个类的 AUC 算术平均值。  | [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC 是接收方操作特性曲线下面的区域。 通过组合每个类中的真报率和误报率来全局计算 Micro| [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC 是接收方操作特性曲线下面的区域。 Weighted 是每个类的评分算术平均值，按每个类中的真实实例数加权| [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy 是与真实标签完全匹配的预测标签百分比。 |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |无|
average_precision_score_macro|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 Macro 是每个类的平均精度评分算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 通过组合每个交接中的真报率和误报率来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 Weighted 是每个类的平均精度评分算术平均值，按每个类中的真实实例数加权|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|平衡准确度是每个类的召回率算术平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 评分是精度和召回率的调和平均值。 Macro 是每个类的 F1 评分算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 评分是精度和召回率的调和平均值。 通过统计真报率、漏报率和误报率总值来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 评分是精度和召回率的调和平均值。 按每个类的 F1 评分类频率计算的加权平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|这是（多项式） 逻辑回归及其扩展（例如神经网络）中使用的损失函数，在给定概率分类器的预测的情况下，定义为真实标签的负对数可能性。 对于在 {0,1} 中包含真实标签 yt，且包含 yt=1 的估计概率 yp 的单个样本，对数损失为 -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|无|
norm_macro_recall|规范化宏召回率是已规范化的宏召回率，因此，随机性能的评分为 0，完美性能的评分为 1。 可以通过公式 norm_macro_recall := (recall_score_macro - R)/(1 - R) 来计算此值，其中，R 是随机预测的 recall_score_macro 预期值（例如，对于二元分类，R=0.5；对于 C 类分类问题，R=(1/C)）|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro"，(recall_score_macro - R)/(1 - R)，其中，R 是随机预测的 recall_score_macro 预期值（例如，对于二元分类，R=0.5；对于 C 类分类问题，R=(1/C)）|
precision_score_macro|Precision 是标记为特定类的，实际位于该类中的元素百分比。 Macro 是每个类的精度算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision 是标记为特定类的，实际位于该类中的元素百分比。 通过统计真报率和误报率总值来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision 是标记为特定类的，实际位于该类中的元素百分比。 Weighted 是每个类的精度算术平均值，按每个类中的真实实例数加权|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall 是实际位于某个类中的已正确标记的元素百分比。 Macro 是每个类的召回率算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall 是实际位于某个类中的已正确标记的元素百分比。 通过统计真报率和漏报率总值来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall 是实际位于某个类中的已正确标记的元素百分比。 Weighted 是每个类的召回率算术平均值，按每个类中的真实实例数加权|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|加权准确度是当分配给每个示例的权重等于该示例的真实类中的真实实例比例时的准确度|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是等于目标中每个元素的该类比例的向量|

### <a name="regression-and-forecasting-metrics"></a>回归和预测指标
在回归或预测任务的每次迭代中保存以下指标。

|主要指标|描述|计算|其他参数
--|--|--|--|
explained_variance|解释方差是数学模型计算给定数据集的方差时遵循的比例。 它是原始数据方差与误差方差之间的递减百分比。 如果误差平均值为 0，则它等于解释方差。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|无|
r2_score|R2 是与输出平均值的基线模型相比，平方误差的确定系数或递减百分比。 如果误差平均值为 0，则它等于解释方差。|[计算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|无|
spearman_correlation|斯皮尔曼相关是两个数据集之间的关系单一性的非参数测量法。 与皮尔逊相关不同，斯皮尔曼相关不假设两个数据集呈正态分布。 与其他相关系数一样，此参数在 -1 和 +1 之间变化，0 表示不相关。 -1 或 +1 相关表示确切的单一关系。 正相关表示 y 随着 x 的递增而递增。 负相关表示 y 随着 x 的递增而递减。|[计算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|无|
mean_absolute_error|平均绝对误差是目标与预测之间的差的预期绝对值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|无|
normalized_mean_absolute_error|规范化平均绝对误差是平均绝对误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以数据范围|
median_absolute_error|平均绝对误差是目标与预测之间的所有绝对差的中间值。 此损失值可靠地反映离群值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|无|
normalized_median_absolute_error|规范化中间绝对误差是中间绝对误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以数据范围|
root_mean_squared_error|均方根误差是目标与预测之间的预期平方差的平方根|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|无|
normalized_root_mean_squared_error|规范化均方根误差是均方根误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以数据范围|
root_mean_squared_log_error|均方根对数误差是预期平方对数误差的平方根|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|无|
normalized_root_mean_squared_log_error|规范化均方根对数误差指均方根对数误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以数据范围|

## <a name="explain-the-model"></a>解释模型

自动化机器学习功能已推出正式版，不过，**模型解释功能仍以公共预览版提供。**

使用自动化机器学习可以了解特征重要性。  在训练过程中，可以获取模型的全局特征重要性。  对于分类方案，还可以获取类级特征重要性。  必须提供验证数据集 (X_valid) 才能获取特征重要性。

可通过两种方式生成特征重要性。

*   完成试验后，可以针对任何迭代使用 `explain_model` 方法。

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   若要查看所有迭代的特征重要性，请在 AutoMLConfig 中将 `model_explainability` 标志设置为 `True`。

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    完成后，可以使用 retrieve_model_explanation 方法检索特定迭代的特征重要性。

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

可以在 Azure 门户中的工作区内可视化特征重要性图表。 在 Notebook 中使用 Jupyter 小组件时，也会显示该图表。 若要详细了解图表，请参阅[示例 Azure 机器学习服务 Notebook](samples-notebooks.md) 一文。

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![特征重要性图形](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>后续步骤

详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

详细了解如何[如何训练回归模型使用自动机器学习](tutorial-auto-train-models.md)或[如何训练使用自动远程资源的机器学习](how-to-auto-train-remote.md)。
