---
title: 创建使用 azureml 数据集访问数据的数据集
titleSuffix: Azure Machine Learning service
description: 了解如何从各种源创建数据集，并注册你的工作区的数据集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: 949468dfe26b076b5c5cf5cab8bbdc2038c7bd2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165886"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>创建和访问 Azure 机器学习中的数据集 （预览）

在本文中，将了解如何创建 Azure 机器学习数据集 （预览版） 以及如何从本地和远程试验访问数据。

使用托管的数据集，你可以： 
* **在模型定型过程中轻松地访问数据**而无需重新连接到基础存储

* **确保数据一致性和可再现性**在试验中使用相同的指针： 笔记本、 自动的机器学习、 管道、 直观的界面

* **共享数据和进行协作**与其他用户

* **浏览数据**& 管理生命周期的数据快照和版本

* **比较数据**培训到生产环境中


## <a name="prerequisites"></a>必备组件

若要创建和使用数据集，需要：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* [Azure 机器学习服务工作区中 Azure 机器学习服务工作区](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* [Azure 机器学习 SDK for Python 安装](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包括数据集的 azureml 包。

> [!Note]
> 某些数据集类 （预览版） 具有依赖项[azureml dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包 (GA）)。 对于 Linux 用户，这些类支持仅在以下分发版上：Red Hat Enterprise Linux、 Ubuntu、 Fedora 和 CentOS。

## <a name="data-formats"></a>数据格式

您可以从以下数据创建 Azure 机器学习数据集：
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-delimited-files-path--separator------header--promoteheadersbehavior-allfileshavesameheaders--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-norows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL 数据库](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake 代。1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>创建数据集 

可以与你的数据集的 azureml 包中使用的数据集进行交互[Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)且明确地[`Dataset`类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py)。

### <a name="create-from-local-files"></a>创建从本地文件

从本地计算机中加载文件，通过指定的文件或文件夹路径[ `auto_read_files()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-)方法从`Dataset`类。  此方法而无需你指定的文件类型或分析自变量执行以下步骤：

* 推断并设置分隔符。
* 跳过空记录在文件顶部。
* 推断并设置标题行。
* 推断和转换列数据类型。

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

或者，使用特定于文件的函数来显式控制分析你的文件。 


### <a name="create-from-azure-datastores"></a>创建从 Azure 数据存储

若要从 Azure 数据存储创建数据集：

* 验证是否有`contributor`或`owner`访问已注册 Azure 数据存储。

* 导入[ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)并[ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition)和`Dataset`SDK 中的包。

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 `get()`方法检索工作区中的现有数据存储。

```
dstore = Datastore.get(workspace, datastore_name)
```

使用`from_delimited_files()`方法来读取带分隔符的文件，并创建未注册的数据集。

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>注册数据集

若要完成创建过程，请向工作区中注册你的数据集：

使用[ `register()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)方法来注册到工作区的数据集，以便它们可与其他人共享和重用跨各种试验。

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> 如果`exist_ok = False`（默认值），并且你尝试注册数据集具有相同名称与另一个，就会出错。 设置为`True`以覆盖现有的。

## <a name="access-data-in-datasets"></a>访问数据集中的数据

已注册的数据集是可访问，并且可使用本机、 远程以及在 Azure 机器学习计算等计算群集上。 若要在试验中重复使用你已注册的数据集和计算环境，请使用以下代码以按名称获取工作区和已注册的数据集。

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>后续步骤

* [探索和准备数据集](how-to-explore-prepare-data.md)。
* [管理数据集定义的生命周期](how-to-manage-dataset-definitions.md)。
* 使用数据集的示例，请参阅[示例笔记本](https://aka.ms/dataset-tutorial)。
