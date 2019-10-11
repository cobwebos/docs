---
title: 创建数据集以访问包含 azureml 数据集的数据
titleSuffix: Azure Machine Learning
description: 了解如何从各种源创建数据集，以及如何将数据集注册到工作区
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 10/10/2019
ms.openlocfilehash: 54f8a1248688a6d62192e4f34cf6b98a94086da8
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274757"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>在 Azure 机器学习中创建和访问数据集（预览）

本文介绍如何创建 Azure 机器学习数据集（预览版），以及如何从本地或远程试验访问数据。

利用 Azure 机器学习数据集，您可以：

* 在数据集引用的**存储中保留数据的单个副本**。

* **在模型训练过程中轻松访问数据**，无需担心连接字符串或数据路径。

* 与其他用户**协作 & 共享数据**。

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集，需要：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)

* [安装的适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml 数据集包。

> [!Note]
> 某些数据集类（预览版）与[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包之间存在依赖关系。 对于 Linux 用户，仅以下分发版支持这些类：Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="dataset-types"></a>数据集类型

根据用户在定型中使用数据集的方式，将数据集分为两种类型。

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通过分析提供的文件或文件列表，以表格格式表示数据。 这使你能够将数据具体化为 pandas 或 spark 数据帧。 可以从 csv、tsv、parquet 文件、SQL 查询结果等创建一个 @no__t 0 的对象。有关完整列表，请访问我们的[文档](https://aka.ms/tabulardataset-api-reference)。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)引用数据存储或公用 url 中的单个或多个文件。 这使您能够将文件下载或装载到您的计算中。 文件可以是任何格式，这使得各种机器学习方案（包括深度学习）更广泛。

若要了解有关即将发生的 API 更改的详细信息，请参阅[此处](https://aka.ms/tabular-dataset)。

## <a name="create-datasets"></a>创建数据集

通过创建数据集，可以创建对数据源位置的引用以及其元数据的副本。 数据会保留在其现有位置，因此不会产生额外的存储成本。 可以使用 Python SDK 或工作区登陆页面（预览版）创建 TabularDatasets 和 FileDatasets。 

要使数据可 Azure 机器学习访问，必须从[Azure 数据存储](how-to-access-data.md)或公共 web url 中的路径创建数据集。

### <a name="using-the-sdk"></a>使用 SDK

使用 Python SDK 通过[Azure 数据存储](how-to-access-data.md)创建数据集：

* 验证是否有 `contributor` 或 `owner` 访问已注册的 Azure 数据存储。

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
#### <a name="create-tabulardatasets"></a>创建 TabularDatasets

使用 @no__t 2 类上的[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-)方法读取 csv 或 tsv 格式的文件，并创建未注册的 TabularDataset。 如果要从多个文件读取，结果将聚合为一个表格表示形式。

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

| |PassengerId|因|Pclass|名称|性别|Age|SibSp|Parch|入场券|费用|客舱|着手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund，Owen Harris|男|22.0|第|0|A/5 21171|7.2500||S
第|2|第|第|Cumings，Mrs Bradley （Florence Briggs 。|女|38.0|第|0|电脑17599|71.2833|C85|C
2|3|第|3|Heikkinen，未命中。 Laina|女|26.0|0|0|STON/O2。 3101282|7.9250||S

使用 @no__t 2 类上的[`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-)方法，从 Azure SQL 数据库读取数据。

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

在 TabularDatasets 中，可以从数据中的列指定时间戳，或将路径模式数据存储在中以启用时序特征，这允许按时间轻松有效地进行筛选。

使用 @no__t 2 类上的[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)方法来指定时间戳列并按时间启用筛选。 可在[此处](https://aka.ms/azureml-tsd-notebook)找到更多示例和详细信息。

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-filedatasets"></a>创建 FileDatasets

使用 @no__t 2 类上的[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)方法加载任意格式的文件，并创建未注册的 FileDataset。

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="using-the-workspace-landing-page"></a>使用工作区登录页

登录到[工作区登录页](https://ml.azure.com)，通过 web 体验创建数据集。 工作区登录页支持创建 TabularDatasets 和 FileDatasets。

以下动画演示了如何在工作区登录页中创建数据集。

首先，在左窗格的 "**资产**" 部分中选择 "**数据集**"。 然后，选择 " **+ 创建数据集**"，选择数据集的源;这可以是本地文件、数据存储或公共 web url。 选择**数据集类型**： * 表格或文件。 "**设置" 和 "预览**" 和 "**架构**" 窗体根据文件类型进行智能填充。 选择 "**下一步**" 以查看它们，或在创建之前进一步配置数据集。 选择 "**完成**" 以完成数据集创建。

![使用 UI 创建数据集](media/how-to-create-register-datasets/create-dataset-ui.gif)

## <a name="register-datasets"></a>注册数据集

若要完成创建过程，请将数据集注册到工作区。

使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)方法将数据集注册到工作区，以便与其他人共享数据集，并在各种试验中重复使用。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> 通过工作区登陆页面创建的数据集将自动注册到工作区。

## <a name="version-datasets"></a>版本数据集

可以通过创建新的版本，将新的数据集注册到相同的名称。 数据集版本是将数据的状态做成书签的一种方式，因此可以应用特定版本的数据集进行试验或未来的复制。 用于版本控制的典型方案包括： 

* 新数据可用于重新训练。
* 在应用不同的数据准备或功能设计方法时。

```Python
# create a TabularDataset from Titanic training data
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


## <a name="access-datasets-in-your-script"></a>访问脚本中的数据集

已注册的数据集可在 Azure 机器学习计算的计算群集上本地和远程访问。 若要跨试验访问已注册的数据集，请使用以下代码按名称获取工作区和已注册的数据集。 默认情况下，@no__t 2 类的[@no__t](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法返回注册到工作区中的最新版本的数据集。

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

* 了解[如何用数据集定型](how-to-train-with-datasets.md)
* 使用自动机器学习来[通过 TabularDatasets 进行训练](https://aka.ms/automl-dataset)。
* 有关数据集定型的更多示例，请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
