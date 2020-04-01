---
title: 创建 Azure 机器学习数据集以访问数据
titleSuffix: Azure Machine Learning
description: 了解如何创建 Azure 机器学习数据集以访问机器学习试验运行的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: 4025c620aea49dfb26ab203630c121d29d88d9d7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474535"
---
# <a name="create-azure-machine-learning-datasets"></a>创建 Azure 机器学习数据集

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何创建 Azure 机器学习数据集，以访问本地或远程试验的数据。

使用 Azure 机器学习数据集可以：

* 在存储中保留数据的单个副本，供数据集引用。

* 在模型训练期间无缝访问数据，而无需考虑连接字符串或数据路径。

* 与其他用户共享数据和展开协作。

## <a name="prerequisites"></a>先决条件
' 要创建和使用数据集，您需要：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml-datasets 包。

> [!NOTE]
> 某些数据集类依赖于 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) 包。 对于 Linux 用户，这些类仅支持以下发行版：红帽企业 Linux、Ubuntu、Fedora 和 CentOS。

## <a name="compute-size-guidance"></a>计算大小指南

创建数据集时，请查看计算处理能力和内存中数据的大小。 存储中的数据大小与数据帧中的数据大小不同。 例如，CSV 文件中的数据可以在数据帧中扩展多达 10 倍，因此 1 GB 的 CSV 文件可以在数据帧中变为 10 GB。 

主要因素是数据集在内存中有多大，即作为数据框。 我们建议您的计算大小和处理能力包含 RAM 大小的 2 倍。 因此，如果数据帧为 10GB，则希望具有 20 GB RAM 的计算目标，以确保数据帧能够舒适地放入内存并进行处理。 如果数据被压缩，它可以进一步扩展;以压缩镶木地板格式存储的 20 GB 相对稀疏的数据可以扩展到内存中 ±800 GB。 由于 Parquet 文件以列格式存储数据，因此，如果您只需要一半的列，则只需在内存中加载 +400 GB。
 
如果你使用的是熊猫，没有理由有超过1个vCPU，因为这是它将使用的所有。 您可以通过 Modin 和 Dask/Ray 轻松地并行化到单个 Azure 机器学习计算实例/节点上的多个 vCPU，并在需要时扩展到大型群集，只需更改为`import pandas as pd``import modin.pandas as pd`。 
 
如果数据无法获得足够的虚拟数据，则有两个选项：使用 Spark 或 Dask 等框架对"内存不足"的数据执行处理，即数据帧通过分区加载到 RAM 分区并进行处理，最终结果在末尾收集。 如果速度太慢，Spark 或 Dask 允许您横向扩展到仍可交互使用的群集。 

## <a name="dataset-types"></a>数据集类型

根据用户在训练中使用数据集的方式，存在两种数据集类型：

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 表示表格格式的、通过分析提供的文件或文件列表所创建的数据。 使用此类数据集可将数据具体化为 Pandas 或 Spark 数据帧。 可以从 .csv、.tsv、.parquet、.jsonl 文件和 SQL 查询结果创建`TabularDataset`对象。 有关完整列表，请参阅 [TabularDatasetFactory 类](https://aka.ms/tabulardataset-api-reference)。

* [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 类引用数据存储或公共 URL 中的单个或多个文件。 通过此方法可以下载文件或将其作为 FileDataset 对象装载到计算中。 文件可以采用任何格式，因此可以实现更广泛的机器学习方案，包括深度学习。

若要详细了解即将发生的 API 更改，请参阅[数据集 API 更改通知](https://aka.ms/tabular-dataset)。

## <a name="create-datasets"></a>创建数据集

创建数据集时，将会创建对数据源位置的引用及其元数据的副本。 由于数据保留在其现有位置，因此不会产生额外的存储成本。 您可以使用`TabularDataset``FileDataset`Python SDK 或在https://ml.azure.com

要使数据可供 Azure 机器学习访问，必须从 [Azure 数据存储](how-to-access-data.md)或公共 Web URL 中的路径创建数据集。 

### <a name="use-the-sdk"></a>使用 SDK

若要使用 Python SDK 从 [Azure 数据存储](how-to-access-data.md)创建数据集：

1. 验证是否对已注册的 Azure 数据存储拥有 `contributor` 或 `owner` 访问权限。

2. 通过引用数据存储中的路径来创建数据集。
> [!Note]
> 可以从多个数据存储中的多个路径创建数据集。 可以从中创建数据集的文件数或数据大小没有硬限制。 但是，对于每个数据路径，将发送一些请求到存储服务，以检查它是指向文件还是文件夹。 此开销可能导致性能下降或失败。 引用包含 1000 个文件的文件夹的数据集被视为引用一个数据路径。 我们建议创建引用数据存储中少于 100 个路径的数据集，以实现最佳性能。

#### <a name="create-a-tabulardataset"></a>创建 TabularDataset

使用[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-)`TabularDatasetFactory`类上的方法读取 .csv 或 .tsv 格式的文件，并创建未注册的 TabularDataset。 如果从多个文件进行读取，结果将聚合为一种表格表示形式。 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

默认情况下，在创建 TabularDataset 时，将自动推断列数据类型。 如果推断出的类型与预期不符，可以使用以下代码指定列类型。 该参数`infer_column_type`仅适用于从分隔文件创建的数据集。您还可以[了解有关受支持的数据类型的更多信息](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)。

> [!IMPORTANT] 
> 如果您的存储位于虚拟网络或防火墙后面，则仅支持通过 SDK 创建数据集。 要创建数据集，请确保在`validate=False``infer_column_types=False``from_delimited_files()`方法中包括参数和方法。 这将绕过初始验证检查，并确保可以从这些安全文件创建数据集。 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|名称|Sex|年龄|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|男|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|女|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|女|26.0|0|0|STON/O2. 3101282|7.9250||S


若要从内存中 pandas 数据帧创建数据集，请将数据写入本地文件（例如 csv），然后从该文件创建数据集。 以下代码演示此工作流。

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

使用类[`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)上`TabularDatasetFactory`的方法从 Azure SQL 数据库读取：

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

在 TabularDataset 中，可以从数据中的列或者从数据所存储到的任何路径模式指定一个时间戳，以启用时序特征。 此规范允许按时间轻松有效地进行筛选。

使用类[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)上`TabularDataset`的方法指定时间戳列，并启用按时间进行筛选。 有关详细信息，请参阅[使用 NOAA 天气数据的表格时序相关 API 演示](https://aka.ms/azureml-tsd-notebook)。

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

使用[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-)`FileDatasetFactory`类上的方法以任何格式加载文件并创建未注册的文件数据集。 如果您的存储位于虚拟网络或防火墙后面，请设置方法`validate =False``from_files()`中的参数。 这将绕过初始验证步骤，并确保可以从这些安全文件创建数据集。

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
以下步骤和动画演示如何在 Azure 机器学习工作室 (https://ml.azure.com) 中创建数据集。

![使用 UI 创建数据集](./media/how-to-create-register-datasets/create-dataset-ui.gif)

若要在工作室中创建数据集：
1. 在 https://ml.azure.com 上登录。
1. 在左侧窗格的“资产”部分，选择“数据集”。******** 
1. 选择“创建数据集”以选择数据集的源。**** 此源可以是本地文件、数据存储或公共 URL。
1. 为“数据集类型”选择“表格”或“文件”。********
1. 选择 **"下一步**"以打开**数据存储和文件选择**窗体。 在此窗体上，您可以选择创建后保留数据集的位置，以及选择要用于数据集的数据文件。 
1. 选择 **"下一步**"以填充 **"设置"和"预览**"**和"架构"** 窗体;它们根据文件类型进行智能填充，您可以在在这些窗体上创建之前进一步配置数据集。 
1. 选择 **"下一步**"以查看 **"确认详细信息**"表单。 检查您的选择并为数据集创建可选的数据配置文件。 详细了解[数据分析](how-to-use-automated-ml-for-ml-models.md#profile)。 
1. 选择“创建”以完成数据集的创建。****

## <a name="register-datasets"></a>注册数据集

若要完成创建过程，请将数据集注册到工作区。 使用[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-)方法将数据集注册到工作区，以便与他人共享数据并在各种实验中重用它们：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> 通过 Azure 机器学习工作室创建的数据集将自动注册到工作区。

## <a name="create-datasets-with-azure-open-datasets"></a>使用 Azure 开放数据集创建数据集

[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 数据集包括不受任何限制的天气、人口普查、节假日、公共安全和位置数据，有助于定型机器学习模型和扩充预测解决方案。 开放数据集位于 Microsoft Azure 云中，并且会同时包含在 SDK 和工作区 UI 中。

### <a name="use-the-sdk"></a>使用 SDK

若要通过 SDK 使用 Azure 开放数据集创建数据集，请确保已使用 `pip install azureml-opendatasets` 安装相应的包。 每个离散数据集在 SDK 中由其自身的类表示，某些类作为 `TabularDataset` 和/或 `FileDataset` 提供。 有关完整的类列表，请参阅[参考文档](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)。

可以检索 `TabularDataset` 或 `FileDataset` 形式的某些类，这样就可以直接操控和/或下载文件。 其他类**只能通过**使用 或`get_tabular_dataset()``get_file_dataset()`函数之一获取数据集。 下面的代码示例显示了这些类型的类的几个示例。

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY return TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

注册从开放数据集创建的数据集时，不会立即下载数据，但以后可按请求（例如，在训练期间）从某个中心存储位置访问数据。

### <a name="use-the-ui"></a>使用 UI

也可以通过 UI 从开放数据集类创建数据集。 在工作区中，选择“资产”下的“数据集”选项卡。******** 在“创建数据集”下拉菜单中，选择“从开放数据集”。********

![UI 中的开放数据集](./media/how-to-create-register-datasets/open-datasets-1.png)

选择数据集对应的磁贴将其选中。 （您可以选择使用搜索栏进行筛选。选择**下一个**。

![选择数据集](./media/how-to-create-register-datasets/open-datasets-2.png)

选择数据集的注册名称，并（可选）使用可用的筛选器筛选数据。 在本例中，对于公共节假日数据集，可按如下所述进行筛选：将时间段设置为一年，将国家/地区代码设置为仅限 US。 选择“创建”  。

![设置数据集参数并创建数据集](./media/how-to-create-register-datasets/open-datasets-3.png)

现在，该数据集已显示在工作区中的“数据集”下。**** 可以像使用创建的其他数据集一样来使用它。

## <a name="version-datasets"></a>对数据集进行版本控制

可以通过创建新版本，以相同的名称注册新数据集。 数据集版本是为数据状态设置书签的一种方法，以便可以应用数据集的特定版本进行试验或者在将来重现该数据集。 详细了解[数据集版本](how-to-version-track-datasets.md)。
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

## <a name="access-datasets-in-your-script"></a>在脚本中访问数据集

可以在本地以及在 Azure 机器学习计算等计算群集上远程访问已注册的数据集。 若要跨试验访问已注册的数据集，请使用以下代码按名称访问工作区和已注册的数据集。 默认情况下，[`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)`Dataset`类上的方法返回在工作区注册的数据集的最新版本。

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

* 了解[如何使用数据集进行训练](how-to-train-with-datasets.md)。
* 通过自动化机器学习[使用 TabularDataset 进行训练](https://aka.ms/automl-dataset)。
* 有关更多数据集训练示例，请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
