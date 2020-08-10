---
title: 创建 Azure 机器学习数据集以访问数据
titleSuffix: Azure Machine Learning
description: 了解如何创建 Azure 机器学习数据集以访问机器学习试验运行的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: b2252a70aea6df755bb8b37c36b77b08db819ba9
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037535"
---
# <a name="create-azure-machine-learning-datasets"></a>创建 Azure 机器学习数据集

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何创建 Azure 机器学习数据集，以访问本地或远程试验的数据。 若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据集，请参阅[安全地访问数据](concept-data.md#data-workflow)一文。

通过创建数据集，可以创建对数据源位置的引用及其元数据的副本。 由于数据仍保留在其现有位置，因此不会产生额外的存储成本，也不会带来数据源的完整性。 此外，还会对数据集进行延迟计算，从而有助于工作流性能速度。 可以从数据存储、公共 Url 和[Azure 开放式数据集](../open-datasets/how-to-create-dataset-from-open-dataset.md)创建数据集。

使用 Azure 机器学习数据集可以：

* 在存储中保留数据的单个副本，供数据集引用。

* 无需担心连接字符串或数据路径即可在模型训练过程中无缝地访问数据。[详细了解如何对数据集进行训练](how-to-train-with-datasets.md)。

* 与其他用户共享数据和展开协作。

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集，需要做好以下准备：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* 一个 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml-datasets 包。

    * 创建一个[Azure 机器学习的计算实例](concept-compute-instance.md#managing-a-compute-instance)，该实例是一个完全配置且托管的开发环境，其中包括集成的笔记本和已安装的 SDK。

    **OR**

    * 使用你自己的 Jupyter 笔记本，并使用[这些说明](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)自行安装 SDK。

> [!NOTE]
> 某些数据集类依赖于[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包，此包仅兼容64位 Python。 对于 Linux 用户，仅在以下分发版上支持这些类： Red Hat Enterprise Linux (7、8) 、Ubuntu (14.04、16.04、18.04) 、Fedora (27、28) 、Debian (8、9) 和 CentOS (7) 。

## <a name="compute-size-guidance"></a>计算大小指南

创建数据集时，请查看计算处理能力和内存中的数据大小。 存储中的数据大小不同于数据帧中的数据大小。 例如，CSV 文件中的数据在数据帧中最多可以扩展到 10 倍，因此，1 GB 的 CSV 文件在数据帧中可能会变成 10 GB。 

如果数据已压缩，则可进一步让其扩展；以压缩 parquet 格式存储的 20 GB 相对稀疏的数据可以在内存中扩展到大约 800 GB。 由于 Parquet 文件以纵栏格式存储数据，因此，如果只需要一半的列，则只需在内存中加载大约 400 GB。

[在 Azure 机器学习中了解有关优化数据处理的详细信息](concept-optimize-data-processing.md)。

## <a name="dataset-types"></a>数据集类型

有两个数据集类型，具体取决于用户在定型中使用它们的方式;FileDatasets 和 TabularDatasets。 这两种类型均可用于涉及、估算、AutoML、hyperDrive 和管道的 Azure 机器学习定型工作流。 

### <a name="filedataset"></a>FileDataset

[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) 引用数据存储或公共 URL 中的单个或多个文件。 如果你的数据已清理，并可在训练试验中使用，则可以将这些文件作为 FileDataset 对象[下载或装载](how-to-train-with-datasets.md#mount-vs-download)到你的计算中。 

建议将 FileDatasets 用于机器学习工作流，因为源文件可以采用任何格式，这将支持更广泛的机器学习方案，包括深度学习。

使用[PYTHON SDK](#create-a-filedataset)或[Azure 机器学习 studio](#create-datasets-in-the-studio)创建 FileDataset

### <a name="tabulardataset"></a>TabularDataset

[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 通过分析提供的文件或文件列表来以表格格式表示数据。 这使你能够将数据具体化为 pandas 或 Spark 数据帧，因此你可以使用熟悉的数据准备和培训库，而无需离开你的笔记本。 可 `TabularDataset` 从 .csv、tsv、. parquet、. jsonl 文件和[SQL 查询结果](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)中创建对象。

使用 TabularDatasets，你可以从数据中的列指定时间戳，或从存储路径模式数据的位置指定时间戳，以启用时序特征。 此规范允许按时间轻松有效地进行筛选。 有关示例，请参阅[具有 NOAA 天气数据的表格时序相关 API 演示](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)。

使用[PYTHON SDK](#create-a-tabulardataset)或[Azure 机器学习 studio](#create-datasets-in-the-studio)创建 TabularDataset。

>[!NOTE]
> 通过 Azure 机器学习 studio 生成的 AutoML 工作流目前仅支持 TabularDatasets。 

## <a name="access-datasets-in-a-virtual-network"></a>访问虚拟网络中的数据集

如果工作区位于虚拟网络中，则必须将数据集配置为跳过验证。 若要详细了解如何在虚拟网络中使用数据存储和数据集，请参阅[使用专用虚拟网络进行训练和推理的过程中的网络隔离](how-to-enable-virtual-network.md#use-datastores-and-datasets)。

<a name="datasets-sdk"></a>

## <a name="create-datasets-via-the-sdk"></a>通过 SDK 创建数据集

 要使数据可供 Azure 机器学习访问，必须从 [Azure 数据存储](how-to-access-data.md)或公共 Web URL 中的路径创建数据集。 

使用 Python SDK 通过[Azure 数据存储](how-to-access-data.md)创建数据集：

1. 验证是否对已注册的 Azure 数据存储拥有 `contributor` 或 `owner` 访问权限。

2. 通过引用数据存储中的路径创建数据集。 可以从多个数据存储中的多个路径创建数据集。 可以从其创建数据集的文件数量或数据大小没有硬性限制。 

> [!Note]
> 对于每个数据路径，会将几个请求发送到存储服务，以检查它是否指向文件或文件夹。 此开销可能会导致性能下降或故障。 数据集引用内含 1000 个文件的文件夹的行为被视为引用一个数据路径。 为了获得最佳性能，我们建议创建在数据存储中引用小于100路径的数据集。

### <a name="create-a-filedataset"></a>创建 FileDataset

使用 `FileDatasetFactory` 类中的 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) 方法可以加载任意格式的文件并创建未注册的 FileDataset。 

如果存储位于虚拟网络或防火墙后面，请 `validate=False` 在方法中设置参数 `from_files()` 。 这会绕过初始验证步骤，确保可以从这些安全文件创建数据集。 详细了解如何[在虚拟网络中使用数据存储和数据集](how-to-enable-virtual-network.md#use-datastores-and-datasets)。

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="create-a-tabulardataset"></a>创建 TabularDataset

使用 `TabularDatasetFactory` 类中的 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) 方法可以读取 .csv 或 .tsv 格式的文件，以及创建未注册的 TabularDataset。 如果从多个文件进行读取，结果将聚合为一种表格表示形式。 

如果存储位于虚拟网络或防火墙后面，请 `validate=False` 在方法中设置参数 `from_delimited_files()` 。 这会绕过初始验证步骤，确保可以从这些安全文件创建数据集。 详细了解如何[在虚拟网络中使用数据存储和数据集](how-to-enable-virtual-network.md#use-datastores-and-datasets)。

下面的代码按名称获取现有工作区和所需的数据存储区。 然后将数据存储和文件位置传递给 `path` 参数以创建新的 TabularDataset `weather_ds`。

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

默认情况下，在创建 TabularDataset 时，将自动推断列数据类型。 如果推断出的类型与预期不符，可以使用以下代码指定列类型。 此参数 `infer_column_type` 仅适用于从分隔文件创建的数据集。 [了解有关支持的数据类型的详细信息](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)。


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| (索引) |PassengerId|Survived|Pclass|名称|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|男|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|女|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|女|26.0|0|0|STON/O2. 3101282|7.9250||S

### <a name="create-a-dataset-from-pandas-dataframe"></a>从 pandas 数据帧创建数据集

若要从内存中的 pandas 数据帧创建 TabularDataset，请将数据写入本地文件（例如 csv），并从该文件创建数据集。 下面的代码演示了此工作流。

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud

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

## <a name="register-datasets"></a>注册数据集

若要完成创建过程，请将数据集注册到工作区。 使用 [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) 方法将数据集注册到工作区，以便与其他人共享，并在工作区中的实验中重复使用这些数据集：

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

<a name="datasets-ui"></a>
## <a name="create-datasets-in-the-studio"></a>在工作室中创建数据集
以下步骤和动画演示了如何在[Azure 机器学习 studio](https://ml.azure.com)中创建数据集。

> [!Note]
> 通过 Azure 机器学习 studio 创建的数据集自动注册到工作区。

![使用 UI 创建数据集](./media/how-to-create-register-datasets/create-dataset-ui.gif)

若要在工作室中创建数据集：
1. 在 https://ml.azure.com 上登录。
1. 在左侧窗格的“资产”部分，选择“数据集”。  
1. 选择“创建数据集”以选择数据集的源。 此源可以是本地文件、数据存储或公共 URL。
1. 为“数据集类型”选择“表格”或“文件”。 
1. 选择“下一步”，打开“数据存储和文件选择”窗体。 在此窗体上，可以选择在创建数据集后保留数据集的位置，还可以选择要用于数据集的具体数据文件。 
    1. 如果数据位于虚拟网络中，请启用 "跳过验证"。 了解有关[虚拟网络隔离和隐私](how-to-enable-virtual-network.md#machine-learning-studio)的详细信息。
1. 选择“下一步”以填充“设置和预览”以及“架构”窗体；它们是根据文件类型智能填充的。在这些窗体上进行创建之前，可以进一步配置数据集。   
1. 选择“下一步”，查看“确认详细信息”窗体。 检查所做的选择，为数据集创建可选的数据配置文件。 详细了解[数据分析](how-to-use-automated-ml-for-ml-models.md#profile)。 
1. 选择“创建”以完成数据集的创建。

## <a name="create-datasets-with-azure-open-datasets"></a>使用 Azure 开放数据集创建数据集

[Azure 开放数据集](https://azure.microsoft.com/services/open-datasets/)是精选公共数据集，可用于将方案专属特征添加到机器学习解决方案，以提高模型的准确度。 数据集包括不受任何限制的天气、人口普查、节假日、公共安全和位置数据，有助于定型机器学习模型和扩充预测解决方案。 打开的数据集位于 Microsoft Azure 云中，同时包含在 SDK 和工作室中。

了解如何[从 Azure 开放数据集创建 Azure 机器学习数据集](../open-datasets/how-to-create-dataset-from-open-dataset.md)。 

## <a name="train-with-datasets"></a>使用数据集进行训练

在机器学习试验中使用您的数据集来训练 ML 模型。 [了解有关如何用数据集定型的详细信息](how-to-train-with-datasets.md)

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

## <a name="next-steps"></a>后续步骤

* 了解[如何使用数据集进行训练](how-to-train-with-datasets.md)。
* 通过自动化机器学习[使用 TabularDataset 进行训练](https://aka.ms/automl-dataset)。
* 有关更多数据集训练示例，请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
