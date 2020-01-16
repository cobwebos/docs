---
title: 创建 Azure 机器学习数据集以访问数据
titleSuffix: Azure Machine Learning
description: 了解如何创建 Azure 机器学习数据集来访问用于机器学习试验的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: d55dc2a1311d66eae01ae12a3dae798fbab20677
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045617"
---
# <a name="create-azure-machine-learning-datasets"></a>创建 Azure 机器学习数据集

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何创建 Azure 机器学习的数据集，以便为本地或远程试验访问数据。

利用 Azure 机器学习数据集，您可以：

* 在存储中保留数据的单个副本，由数据集引用。

* 无需担心连接字符串或数据路径即可在模型训练过程中无缝地访问数据。

* 与其他用户共享数据和进行协作。

## <a name="prerequisites"></a>必备组件

若要创建和使用数据集，需要：

* Azure 订阅。 如果没有，请在开始之前创建一个免费帐户。 试用[Azure 机器学习免费或付费版本](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [安装的适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml 数据集包。

> [!NOTE]
> 某些数据集类依赖于[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包。 对于 Linux 用户，仅以下分发版支持这些类： Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="dataset-types"></a>数据集类型

有两种数据集类型，具体取决于用户在定型中使用它们的方式：

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通过分析提供的文件或文件列表，以表格格式表示数据。 这使你能够将数据具体化为 Pandas 或 Spark 数据帧。 可从 .csv、tsv、parquet 文件和 SQL 查询结果创建 `TabularDataset` 对象。 有关完整列表，请参阅[TabularDatasetFactory 类](https://aka.ms/tabulardataset-api-reference)。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)类引用数据存储或公用 url 中的单个或多个文件。 通过此方法，您可以将文件作为 FileDataset 对象下载或装载到您的计算中。 文件可以采用任何格式，这可以实现更广泛的机器学习方案，包括深度学习。

若要了解有关即将发生的 API 更改的详细信息，请参阅[数据集 api 更改通知](https://aka.ms/tabular-dataset)。

## <a name="create-datasets"></a>创建数据集

通过创建数据集，可以创建对数据源位置的引用以及其元数据的副本。 由于数据仍保留在其现有位置，因此不会产生额外的存储成本。 您可以使用 Python SDK 或工作区登陆页面（预览版）创建 `TabularDataset` 和 `FileDataset` 数据集。

要使数据可 Azure 机器学习访问，必须从[Azure 数据存储](how-to-access-data.md)或公共 web url 中的路径创建数据集。

### <a name="use-the-sdk"></a>使用 SDK

使用 Python SDK 通过[Azure 数据存储](how-to-access-data.md)创建数据集：

1. 验证你是否已 `contributor` 或 `owner` 对已注册的 Azure 数据存储的访问权限。

2. 通过引用数据存储中的路径来创建数据集。
> [!Note]
> 可以从多个数据存储中的多个路径创建数据集。 你可以从其创建数据集的文件数量或数据大小没有硬性限制。 但是，对于每个数据路径，会将几个请求发送到存储服务，以检查它是否指向文件或文件夹。 此开销可能会导致性能下降或发生故障。 引用其中包含1000文件的文件夹的数据集被视为引用一个数据路径。 为了获得最佳性能，我们建议创建在数据存储中引用小于100路径的数据集。


#### <a name="create-a-tabulardataset"></a>创建 TabularDataset

可以通过 SDK 或 Azure 机器学习 Studio 创建 TabularDatasets。 您可以从数据中的列或存储数据的路径模式指定时间戳，以启用时序特征。 此规范允许按时间轻松有效地筛选。

使用 `TabularDatasetFactory` 类的[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-)方法可读取 .csv 或 tsv 格式的文件，以及创建未注册的 TabularDataset。 如果要从多个文件读取，结果将聚合为一个表格表示形式。

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'ather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

默认情况下，当您创建 TabularDataset 时，将自动推断列数据类型。 如果推断出的类型与预期不符，则可以使用以下代码指定列类型。 你还可以[了解有关支持的数据类型的详细信息](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)。

```Python
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|已保留|Pclass|名称|性别|年龄|SibSp|Parch|工作单|车费|客舱|着手
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|第|错误|3|Braund，Owen Harris|男|22.0|第|0|A/5 21171|7.2500||S
第|2|正确|第|Cumings，Mrs Bradley （Florence Briggs 。|女|38.0|第|0|电脑17599|71.2833|C85|C
2|3|正确|3|Heikkinen，未命中。 Laina|女|26.0|0|0|STON/O2。 3101282|7.9250||S

使用 `TabularDatasetFactory` 类的[`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-)方法从 Azure SQL 数据库读取：

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

在 TabularDatasets 中，可以从数据中的列指定时间戳，或从存储路径模式数据的位置指定时间戳，以启用时序特征。 此规范允许按时间轻松有效地筛选。

使用`TabularDataset` 类的[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)方法可以指定时间戳列并按时间启用筛选。 有关详细信息，请参阅[具有 NOAA 天气数据的表格时序相关 API 演示](https://aka.ms/azureml-tsd-notebook)。

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

#### <a name="create-a-filedataset"></a>创建 FileDataset

使用 `FileDatasetFactory` 类的[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)方法以任意格式加载文件，并创建未注册的 FileDataset：

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>在 Web 上 
以下步骤和动画演示了如何在 Azure 机器学习 studio https://ml.azure.com 中创建数据集。

![使用 UI 创建数据集](./media/how-to-create-register-datasets/create-dataset-ui.gif)

在工作室中创建数据集：
1. 在 https://ml.azure.com 登录。
1. 在左侧窗格的 "**资产**" 部分中选择 "**数据集**"。 
1. 选择 "**创建数据集**"，选择数据集的源。 此源可以是本地文件、数据存储或公共 Url。
1. 选择 "**表格**或**文件**" 作为数据集类型。
1. 选择 "**下一步**" 查看**设置和预览**、**架构**和**确认详细信息**窗体;它们根据文件类型进行智能填充。 使用这些窗体检查你的选择，并在创建之前进一步配置你的数据集。  
1. 选择 "**创建**" 以完成数据集创建。

## <a name="register-datasets"></a>注册数据集

若要完成创建过程，请将数据集注册到工作区。 使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)方法将数据集注册到你的工作区，以便与其他人共享数据集，并跨各种试验重复使用这些数据集：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> 通过 Azure 机器学习 Studio 创建的数据集自动注册到工作区。

## <a name="create-datasets-with-azure-open-datasets"></a>用 Azure 开放数据集创建数据集

[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 数据集包括不受任何限制的天气、人口普查、节假日、公共安全和位置数据，有助于定型机器学习模型和扩充预测解决方案。 打开的数据集位于 Microsoft Azure 云中，同时包含在 SDK 和工作区 UI 中。

### <a name="use-the-sdk"></a>使用 SDK

若要从 SDK 创建包含 Azure 开放数据集的数据集，请确保已安装 `pip install azureml-opendatasets`的包。 每个离散数据集均由其在 SDK 中的类来表示，某些类可作为 `TabularDataset`、`FileDataset`或这两者。 有关类的完整列表，请参阅[参考文档](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)。

您可以检索某些类作为 `TabularDataset` 或 `FileDataset`，这允许您直接操作和/或下载文件。 其他类只能通过使用 `get_tabular_dataset()` 或 `get_file_dataset()` 函数**之一来获取数据集**。 下面的代码示例演示了这些类类型的一些示例。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

在注册从打开的数据集创建的数据集时，不会立即下载数据，但会在以后请求时（例如在训练期间）访问数据（例如，在训练过程中）。

### <a name="use-the-ui"></a>使用 UI

还可以通过 UI 从开放式数据集类创建数据集。 在工作区中，选择 "**资产**" 下的 "**数据集**" 选项卡。 在 "**创建数据集**" 下拉菜单上，选择 "**从打开的数据集**"。

![用 UI 打开数据集](./media/how-to-create-register-datasets/open-datasets-1.png)

选择数据集的磁贴来选择数据集。 （您可以选择使用搜索栏进行筛选。）选择 "**下一步**"。

![选择数据集](./media/how-to-create-register-datasets/open-datasets-2.png)

选择要在其下注册数据集的名称，并根据需要使用可用的筛选器来筛选数据。 在这种情况下，对于公共假日数据集，请将时间段筛选为一年，将国家/地区代码筛选为仅限美国。 选择“创建”。

![设置数据集参数并创建数据集](./media/how-to-create-register-datasets/open-datasets-3.png)

数据集现已在工作区中的 "**数据集**" 下提供。 您可以使用与创建的其他数据集相同的方式来使用它。

## <a name="version-datasets"></a>版本数据集

可以通过创建新的版本，将新的数据集注册到相同的名称。 数据集版本是将数据的状态做成书签的一种方法，以便可以应用特定版本的数据集进行试验或未来的复制。 详细了解[数据集版本](how-to-version-track-datasets.md)。
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-datasets-in-your-script"></a>访问脚本中的数据集

在计算群集上本地和远程都可以访问已注册的数据集，如 Azure 机器学习计算。 若要跨试验访问已注册的数据集，请使用以下代码访问工作区，并按名称访问已注册的数据集。 默认情况下，`Dataset` 类的[`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法返回注册到工作区中的最新版本的数据集。

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

* 了解[如何定型数据集](how-to-train-with-datasets.md)。
* 使用自动机器学习来[通过 TabularDatasets 进行训练](https://aka.ms/automl-dataset)。
* 有关数据集定型示例的详细信息，请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
