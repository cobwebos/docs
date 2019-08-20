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
ms.openlocfilehash: c5b423fca3e0ec116fceefb6867189f4f8413b96
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856089"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>在 Azure 机器学习中创建和访问数据集 (预览)

本文介绍如何创建 Azure 机器学习数据集 (预览版), 以及如何从本地或远程试验访问数据。

利用 Azure 机器学习数据集, 您可以: 

* 在数据集引用的**存储中保留数据的单个副本**

* 通过探索数据分析**分析数据** 

* **在模型训练过程中轻松访问数据**, 无需担心连接字符串或数据路径

* 与其他用户**协作 & 共享数据**

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集, 需要:

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* [Azure 机器学习服务工作区](how-to-manage-workspace.md)

* [安装的适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), 其中包含 azureml 数据集包。

> [!Note]
> 某些数据集类 (预览版) 与[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包 (GA) 有依赖关系。 对于 Linux 用户, 仅以下分发版支持这些类:Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="data-formats"></a>数据格式

您可以使用以下格式创建 Azure 机器学习数据集:
+ [界限](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none--partition-format-none-)
+ [json](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false--partition-format-none-)
+ [Excel](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true--partition-format-none-)
+ [Parquet](/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false--partition-format-none-)
+ [pandas 数据帧](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-pandas-dataframe-dataframe--path-none--in-memory-false-)
+ [SQL 查询](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)

## <a name="create-datasets"></a>创建数据集 

通过创建数据集, 可以创建对数据源位置的引用以及其元数据的副本。 数据会保留在其现有位置, 因此不会产生额外的存储成本。

### <a name="create-from-local-files"></a>从本地文件创建

通过使用[`auto_read_files()`](/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false--partition-format-none-) `Dataset`类中的方法指定文件或文件夹路径, 从本地计算机加载文件。  此方法执行以下步骤, 无需指定文件类型或分析参数:

* 推断并设置分隔符。
* 跳过文件顶部的空记录。
* 推断并设置标题行。
* 推断和转换列数据类型。

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

或者, 使用特定于文件的函数显式控制文件的分析。 


### <a name="create-from-azure-datastores"></a>从 Azure 数据存储创建

若要从[Azure 数据存储](how-to-access-data.md)创建数据集:

* 验证你是否`contributor`具有`owner`注册的 Azure 数据存储的访问权限。

* 通过引用数据存储中的路径来创建数据集 

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
dstore = Datastore.get(workspace, datastore_name)
```

使用方法从 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) 中读取带分隔符的文件, 并创建未注册的数据集。  `from_delimited_files()`

```Python
# create an in-memory Dataset on your local machine
dataset = Dataset.from_delimited_files(dstore.path('data/src/crime.csv'))

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>注册数据集

若要完成创建过程, 请将数据集注册到工作区:

[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)使用方法可将数据集注册到工作区, 以便与其他人共享数据集, 并在各种试验中重复使用。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> 如果`exist_ok = False`为 (默认值), 并且您尝试使用同一名称注册一个数据集, 则会出现错误。 设置为`True`可覆盖现有。

## <a name="access-data-in-datasets"></a>访问数据集中的数据

已注册的数据集可在 Azure 机器学习计算的计算群集上本地和远程访问。 若要跨试验访问已注册的数据集, 请使用以下代码按名称获取工作区和已注册的数据集。

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
print(Dataset.list(workspace))

# Get dataset by name
dataset = Dataset.get(workspace, 'dataset_crime')

# Load data into pandas DataFrame
dataset.to_pandas_dataframe()
```

## <a name="next-steps"></a>后续步骤

* [浏览和准备数据集](how-to-explore-prepare-data.md)。
* 有关使用数据集的示例, 请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
