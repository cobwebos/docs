---
title: 创建 Python 模型：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "创建 Python 模型" 模块创建自定义建模或数据处理模块。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371811"
---
# <a name="create-python-model-module"></a>创建 Python 模型模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

了解如何使用 "创建 Python 模型" 模块通过 Python 脚本创建未经训练的模型。 可以在 Azure 机器学习设计器环境中包含在 Python 包中的任何学习器基础上建模。 

创建模型后，可以使用[定型模型](train-model.md)对数据集上的模型进行定型，就像 Azure 机器学习中的任何其他学习器一样。 训练的模型可以传递给[评分模型](score-model.md)来进行预测。 然后，你可以保存训练的模型，并将评分工作流发布为 web 服务。

> [!WARNING]
> 目前，不能将 Python 模型的评分结果传递给 "[评估模型](evaluate-model.md)"。 如果需要评估模型，可以编写自定义 Python 脚本，并使用 "[执行 Python 脚本](execute-python-script.md)" 模块运行该脚本。  


## <a name="configure-the-module"></a>配置模块

使用此模块需要 Python 的中级或专家知识。 该模块支持使用 Azure 机器学习中已安装的 Python 包中包含的任何学习器。 请参阅[执行 Python 脚本](execute-python-script.md)中的预安装 python 包列表。
  

本文介绍如何使用简单管道**创建 Python 模型**。 下面是管道的关系图：

![创建 Python 模型的关系图](./media/module/create-python-model.png)

1. 选择 "**创建 Python 模型**"，然后编辑脚本以实现建模或数据管理过程。 可以将模型基于 Azure 机器学习环境中包含在 Python 包中的任何学习器。

   以下双类 Naive Bayes 分类器的以下示例代码使用常用的*spark-sklearn*包：

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. 将刚刚创建的 "**创建 Python 模型**" 模块连接到 "**定型**模型" 和 "**评分模型**"。

1. 如果需要评估模型，请添加 "[执行 Python 脚本](execute-python-script.md)" 模块并编辑 Python 脚本。

   下面的脚本是示例计算代码：

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function can contain up to two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 