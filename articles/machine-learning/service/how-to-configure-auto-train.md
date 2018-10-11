---
title: 配置自动化机器学习试验 - Azure 机器学习
description: 自动化机器学习将为你选择一种算法，并生成随时可用于部署的模型。 了解可用于配置自动化机器学习试验的选项。
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 33126c094a55bc57edd49a54fbc4f5acd7401998
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078998"
---
# <a name="configure-your-automated-machine-learning-experiment"></a>配置自动化机器学习试验

自动化机器学习（自动化 ML）将为你选择算法和超数组，并生成随时可用于部署的模型。 也可以下载该模型供进一步自定义。 可以使用多个选项来配置自动化机器学习试验。 本指南介绍如何定义各种配置设置。

若要查看自动化机器学习示例，请参阅[教程：使用自动化机器学习训练分类模型](tutorial-auto-train-models.md)或[使用云中的自动化机器学习训练模型](how-to-auto-train-remote.md)。

自动化机器学习提供的配置选项：

* 选择试验类型，例如“分类”、“回归” 
* 数据源、格式和提取数据
* 选择计算目标（本地或远程）
* 自动机器学习试验设置
* 运行自动化机器学习试验
* 探索模型指标
* 注册和部署模型

## <a name="select-your-experiment-type"></a>选择试验类型
在开始试验之前，应确定要解决的机器学习问题类型。 自动化机器学习支持两种类别的监督式学习：“分类”和“回归”。 在自动化和优化过程中，自动化机器学习支持以下算法。 用户不需要指定算法。
分类 | 回归
--|--
sklearn.linear_model.LogisticRegression | sklearn.linear_model.ElasticNet
sklearn.linear_model.SGDClassifier  | sklearn.ensemble.GradientBoostingRegressor
sklearn.naive_bayes.BernoulliNB | sklearn.tree.DecisionTreeRegressor
sklearn.naive_bayes.MultinomialNB | sklearn.neighbors.KNeighborsRegressor
sklearn.svm.SVC | sklearn.linear_model.LassoLars
sklearn.svm.LinearSVC | sklearn.linear_model.SGDRegressor
sklearn.calibration.CalibratedClassifierCV |    sklearn.ensemble.RandomForestRegressor
sklearn.neighbors.KNeighborsClassifier |    sklearn.ensemble.ExtraTreesRegressor
sklearn.tree.DecisionTreeClassifier |   lightgbm.LGBMRegressor
sklearn.ensemble.RandomForestClassifier |
sklearn.ensemble.ExtraTreesClassifier   |
sklearn.ensemble.GradientBoostingClassifier |
lightgbm.LGBMClassifier |


## <a name="data-source-and-format"></a>数据源和格式
自动化机器学习支持驻留在本地桌面上或云中 Azure Blob 存储内的数据。 可将数据读取成 scikit-learn 支持的数据格式。 可将数据读取成 1) Numpy 数组 X（特征）和 y（目标变量，也称为标签），或 2) Pandas 数据帧。 

示例：

1.  Numpy 数组

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data 
    y_digits = digits.target
    ```

2.  Pandas 数据帧

    ```python
    Import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"') 
    # get integer labels 
    le = LabelEncoder() 
    le.fit(df["Label"].values) 
    y = le.transform(df["Label"].values) 
    df = df.drop(["Label"], axis=1) 
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>在远程计算中提取用于运行试验的数据

如果使用远程计算来运行试验，必须将数据提取包装在单独的 Python 脚本 `get_data()` 中。 此脚本在运行自动化机器学习试验的远程计算中运行。 `get_data` 消除了通过网络为每个迭代提取数据的需要。 如果不使用 `get_data`，则在远程计算中运行时，试验将会失败。

下面是 `get_data` 的示例：

```python
%%writefile $project_folder/get_data.py 
import pandas as pd 
from sklearn.model_selection 
import train_test_split 
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

`get_data` 脚本可以返回以下信息：
密钥 | Type |    与以下对象互斥 | Description
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

## <a name="train-and-validation-data"></a>训练和验证数据

可以通过 get_data() 或直接在 `AutoMLConfig` 方法中指定单独的训练集和验证集。

## <a name="cross-validation-split-options"></a>交叉验证拆分选项

### <a name="k-folds-cross-validation"></a>K 折交叉验证

使用 `n_cross_validations` 设置指定交叉验证的数目。 训练数据集将随机拆分为大小相等的 `n_cross_validations` 折。 在每个交叉验证轮次，某个折将用于验证剩余折上训练的模型。 重复此过程 `n_cross_validations` 次，直到每个折作为验证集使用了一次。 最后，将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>蒙特卡洛交叉验证（也称为 重复随机子采样）

使用 `validation_size` 指定应该用于验证的训练数据集百分比，并使用 `n_cross_validations` 指定交叉验证的数目。 在每个交叉验证轮次，将随机选择 `validation_size` 大小的子集来验证基于剩余数据训练的模型。 最后，将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。

### <a name="custom-validation-dataset"></a>自定义验证数据集

如果随机拆分不可接受，请使用自定义验证数据集（通常是时序数据或不平衡的数据）。 这样，便可以指定自己的验证数据集。 将会根据指定的验证数据集而不是随机数据集来评估模型。

## <a name="compute-to-run-experiment"></a>用于运行试验的计算环境

接下来，确定要在何处训练模型。 自动化机器学习训练试验将在你拥有和管理的计算目标中运行。 

支持的计算选项包括：
1.  本地台式机或便携式计算机等本地计算机  – 如果数据集较小，并且仍处于探索阶段，则通常使用此选项。
2.  云中的远程计算机 – 运行 Linux 的 [Azure Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) – 如果数据集较大，并且你要纵向扩展到 Azure 云中可用的大型计算机，则可以使用此选项。 
3.  Azure Batch AI 群集 — 可设置为横向扩展并以并行方式运行自动化机器学习迭代的托管群集。 


## <a name="configure-your-experiment-settings"></a>配置试验设置

可以使用多个控件来配置自动化机器学习试验。 通过实例化 `AutoMLConfig` 对象来设置这些参数。

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

    ````python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        primary_metric='r2_score',
        X=X, 
        y=y,
        n_cross_validations=5)
    ````

下表列出了可用于试验的参数设置及其默认值。

属性 |  Description | 默认值
--|--|--
`task`  |指定机器学习问题的类型。 允许的值为 <li>分类</li><li>回归</li>    | 无 |
`primary_metric` |在生成模型时要优化的指标。 例如，如果将准确度指定为 primary_metric，则自动化机器学习将查找准确度最高的模型。 对于每个试验，只能指定一个 primary_metric。 允许的值为 <br/>**分类**：<br/><li> accuracy  </li><li> AUC_weighted</li><li> precision_score_weighted </li><li> balanced_accuracy </li><li> average_precision_score_weighted </li><br/>**回归**： <br/><li> normalized_mean_absolute_error </li><li> spearman_correlation </li><li> normalized_root_mean_squared_error </li><li> normalized_root_mean_squared_log_error</li><li> R2_score    </li> | 分类：accuracy <br/>回归：spearman_correlation <br/> |
`exit_score` |  可为 primary_metric 设置目标值。 找到符合 primary_metric 目标的模型后，自动化机器学习将停止迭代，试验将会终止。 如果未设置此值（默认值），自动化机器学习试验将按照迭代中指定的迭代次数继续运行。 取双精度值。 如果永远达不到目标，自动化机器学习将会继续，直到达到了迭代中指定的迭代次数为止。|   无
`iterations` |最大迭代数。 每个迭代相当于生成管道的训练作业。 管道是数据预处理机制和模型。 若要获得优质模型，请使用 250 或更大的值 | 100
`Concurrent_iterations`|    要并行运行的最大迭代数。 此设置仅适用于远程计算。|    1
`max_cores_per_iteration`   | 指示要使用计算目标上的多少个核心来训练单个管道。 如果算法可以利用多个核心，则可以提高多核计算机的性能。 可将其设置为 -1，以使用计算机上的所有可用核心。|  1
`max_time_sec` |    限制特定迭代占用的时间（秒）。 如果某个迭代超过指定的时间，将取消该迭代。 如果未设置，则迭代会继续运行，直到完成。 |   无
`n_cross_validations`   |交叉验证拆分数| 无
`validation_size`   |验证集的大小（占所有训练样本的百分比）。|  无
`preprocess` | True/False <br/>如果为 True，则试验可以基于输入执行预处理。 下面是预处理子集<li>缺少数据：插补缺失数据 - 带平均值的数字，出现次数最多的文本 </li><li>分类值：如果数据类型为数字，并且唯一值的数量小于 5%，则转换为独热编码 </li><li>等等。有关完整列表，请查看 [GitHub 存储库](https://aka.ms/aml-notebooks)</li><br/>注意：如果数据比较稀疏，则不能使用 preprocess = true |  False | 
`blacklist_algos`   | 自动化机器学习试验会尝试许多不同的算法。 可将自动化机器学习配置为从试验中排除某些算法。 如果你知道某些算法不适合你的数据集，则这种做法很有用。 排除算法可以节省计算资源和训练时间。<br/>分类允许的值<br/><li>逻辑回归</li><li>SGD 分类器</li><li>MultinomialNB</li><li>BernoulliNB</li><li>SVM</li><li>LinearSVM</li><li>kNN</li><li>DT</li><li>RF</li><li>附加树</li><li>渐进提升</li><li>lgbm_classifier</li><br/>回归允许的值<br/><li>弹性网络</li><li>渐进提升回归器</li><li>DT 回归器</li><li>kNN 回归器</li><li>Lasso lars</li><li>SGD 回归器</li><li>RF 回归器</li><li>附加树回归器</li>|   无
`verbosity` |使用 INFO（最详细）和 CRITICAL（最精简）控制日志记录级别。<br/>允许值包括：<br/><li>logging.INFO</li><li>logging.WARNING</li><li>logging.ERROR</li><li>logging.CRITICAL</li>  | logging.INFO</li> 
`X` | 用于训练的所有特征 |  无
`y` |   用于训练的标签数据。 对于分类，应是一个整数数组。|  无
`X_valid`|（可选）用于验证的所有特征。 如果未指定，则在 train 与 validate 之间拆分 X |   无
`y_valid`   |（可选）用于验证的标签数据。 如果未指定，则在 train 与 validate 之间拆分 y    | 无
`sample_weight` |   （可选）每个样本的权重值。 需要为数据点分配不同的权重时使用 |   无
`sample_weight_valid`   |   （可选）每个验证样本的权重值。 如果未指定，则在 train 与 validate 之间拆分 sample_weight   | 无
`run_configuration` |   RunConfiguration 对象。  用于远程运行。 |无
`data_script`  |    包含 get_data 方法的文件的路径。  远程运行需要此参数。   |无


## <a name="run-experiment"></a>运行试验

接下来，我们可以启动试验以生成模型。 将 `AutoMLConfig` 传递给 `submit` 方法以生成模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>首先在新的 DSVM 上安装依赖项。  最长可能需要在 10 分钟后才会显示输出。
>将 `show_output` 设置为 True 可在控制台上显示输出。


## <a name="explore-model-metrics"></a>探索模型指标
如果在笔记本中操作，可以在小组件或内联单元中查看结果。 请参阅“跟踪和评估模型”中的详细信息。 （确保在 AML 内容包含自动化机器学习的相关信息）

将在每次迭代中保存以下指标
* AUC_macro
* AUC_micro
* AUC_weighted
* accuracy
* average_precision_score_macro
* average_precision_score_micro
* average_precision_score_weighted
* balanced_accuracy
* f1_score_macro
* f1_score_micro
* f1_score_weighted
* log_loss
* norm_macro_recall
* precision_score_macro
* precision_score_micro
* precision_score_weighted
* recall_score_macro
* recall_score_micro
* recall_score_weighted
* weighted_accuracy

## <a name="next-steps"></a>后续步骤

详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

详细了解[如何使用自动化机器学习训练分类模型](tutorial-auto-train-models.md)或[如何在远程资源上使用自动化机器学习进行训练](how-to-auto-train-remote.md)。 