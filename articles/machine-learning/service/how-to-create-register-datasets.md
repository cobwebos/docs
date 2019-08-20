---
title: 创建数据集以访问包含 azureml 数据集的数据
titleSuffix: Azure Machine Learning service
description: 了解如何从各种源创建数据集, 以及如何将数据集注册到工作区
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 67dda1ab56c6a706a9fdbef45fabdae9167ffe2b
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616342"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>在 Azure 机器学习中创建和访问数据集 (预览)

本文介绍如何创建 Azure 机器学习数据集 (预览版), 以及如何从本地或远程试验访问数据。

利用 Azure 机器学习数据集, 您可以: 

* 在数据集引用的**存储中保留数据的单个副本**。 

* **在模型训练过程中轻松访问数据**, 无需担心连接字符串或数据路径。

* 与其他用户**协作 & 共享数据**。

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集, 需要:

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* [Azure 机器学习服务工作区](how-to-manage-workspace.md)

* [安装的适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), 其中包含 azureml 数据集包。

> [!Note]
> 某些数据集类 (预览版) 与[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包之间存在依赖关系。 对于 Linux 用户, 仅以下分发版支持这些类:Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="dataset-types"></a>数据集类型
数据集根据用户在定型中使用它们的方式分类为各种类型。 目前, 我们支持 TabularDatasets, 它通过分析提供的文件或文件列表来表示表格格式的数据。 这使你能够将数据具体化为 pandas 数据帧。 可以从 csv、tsv、parquet 文件、SQL 查询结果等创建 TabularDataset。有关完整列表, 请访问我们的文档。

若要了解有关即将发生的 API 更改的详细信息, 请参阅[什么是 Azure 机器学习服务？](https://aka.ms/tabular-dataset) 

## <a name="create-datasets"></a>创建数据集 

通过创建数据集, 可以创建对数据源位置的引用以及其元数据的副本。 数据会保留在其现有位置, 因此不会产生额外的存储成本。

要使数据 Azure 机器学习服务可访问, 必须从[Azure 数据存储](how-to-access-data.md)或公共 web url 中的路径创建数据集。

若要从[Azure 数据存储](how-to-access-data.md)创建数据集:

* 验证你是否`contributor`具有`owner`注册的 Azure 数据存储的访问权限。

* 通过引用数据存储中的路径来创建数据集。

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### <a name="create-tabulardatasets"></a>创建 TabularDatasets

使用`TabularDatasetFactory`类`from_delimited_files()`的方法读取 csv 或 tsv 格式的文件, 并创建未注册的 TabularDataset。 如果要从多个文件读取, 结果将聚合为一个表格表示形式。

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|因|Pclass|姓名|性别|期限|SibSp|Parch|入场券|费用|客舱|着手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Owen Harris|男|22.0|1|0|A/5 21171|7.2500||S
1|2|1|1|Cumings, Mrs Bradley (Florence Briggs 。|女|38.0|1|0|电脑17599|71.2833|C85|C
2|3|1|3|Heikkinen, 未命中。 Laina|女|26.0|0|0|STON/O2。 3101282|7.9250||S

## <a name="register-datasets"></a>注册数据集

若要完成创建过程, 请将数据集注册到工作区:

`register()`使用方法可将数据集注册到工作区, 以便与其他人共享数据集, 并在各种试验中重复使用。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## <a name="version-datasets"></a>版本数据集

可以通过创建新的版本, 将新的数据集注册到相同的名称。 数据集版本是将数据的状态做成书签的一种方式, 因此可以应用特定版本的数据集进行试验或未来的复制。 考虑版本控制的典型方案: 
* 新数据可用于重新训练。
* 在应用不同的数据准备或功能设计方法时。

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-your-data-during-training"></a>在定型过程中访问数据

已注册的数据集可在 Azure 机器学习计算的计算群集上本地和远程访问。 若要跨试验访问已注册的数据集, 请使用以下代码按名称获取工作区和已注册的数据集。 默认`get_by_name`情况下, `Dataset`类上的方法返回注册到工作区中的最新版本的数据集。

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="next-steps"></a>后续步骤

* 使用自动机器学习来[通过 TabularDatasets 进行训练](https://aka.ms/automl-dataset)。
* 有关数据集定型的更多示例, 请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
