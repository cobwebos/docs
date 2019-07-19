---
title: 访问数据存储 / blob 中的数据以进行训练
titleSuffix: Azure Machine Learning service
description: 了解如何在使用 Azure 机器学习服务训练期间使用数据存储访问 blob 数据存储
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 05/24/2019
ms.custom: seodec18
ms.openlocfilehash: 97a4bc20394553b97211763cedaa76c3711306f2
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319323"
---
# <a name="access-data-from-your-datastores"></a>从数据存储访问数据

 在 Azure 机器学习服务中, 数据存储是独立于计算位置的机制, 可用于访问存储, 而无需更改源代码。 无论你是编写定型代码以将路径作为参数, 还是直接向估计器提供数据存储, Azure 机器学习工作流都确保你的数据存储位置可访问, 并可供你的计算上下文使用。

本操作说明显示以下任务的示例:
* [选择数据存储](#access)
* [获取数据](#get)
* [将数据上传和下载到数据存储](#up-and-down)
* [在定型期间访问数据存储](#train)

## <a name="prerequisites"></a>系统必备

若要使用数据存储，首先需要一个[工作区](concept-workspace.md)。

首先，请[创建新工作区](setup-create-workspace.md#sdk)或检索现有的工作区：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>选择数据存储

可以使用默认数据存储或自带。

### <a name="use-the-default-datastore-in-your-workspace"></a>使用工作区中的默认数据存储

 每个工作区都有一个已注册的默认数据存储, 您可以立即使用它。

若要获取工作区的默认数据存储：

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>向工作区注册您自己的数据存储

如果已有现有的 Azure 存储，可将其注册为工作区中的数据存储。 

<a name="store"></a>

####  <a name="storage-guidance"></a>存储指导原则

建议 blob 存储和 blob 数据存储。 标准存储和高级存储都适用于 blob。 尽管更昂贵, 但我们建议使用高级存储, 因为这样可以提高定型运行的速度, 尤其是在针对大型数据集进行训练时。 请参阅[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)中的存储帐户成本信息。

>[!NOTE]
> Azure 机器学习服务支持其他类型的数据存储, 这在特定情况下可能非常有用。 例如, 如果需要使用数据库中存储的数据进行定型, 则可以使用 AzureSQLDatabaseDatastore 或 AzurePostgreSqlDatastore。 请参阅[此表](#matrix)了解可用的数据存储类型。

#### <a name="register-your-datastore"></a>注册数据存储
所有寄存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)类上, 格式为 register_azure_ *。

以下示例演示如何将 Azure Blob 容器或 Azure 文件共享注册为数据存储。

+ 对于**Azure Blob 容器数据存储**, 请使用[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ 对于**Azure 文件共享数据存储**, 请[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)使用。 例如： 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           file_share_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>查找 & 定义数据存储

若要获取在当前工作区中注册的指定数据[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)存储, 请使用:

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

若要获取给定工作区中所有数据存储的列表, 请使用以下代码:

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

若要为当前工作区定义不同的默认数据存储[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-), 请使用:

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>上载 & 下载数据
下面[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)的[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)示例中所述的和方法特定于和对[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)类具有相同的作用。

### <a name="upload"></a>上载

 使用 Python SDK 将目录或单个文件上传到数据存储。

将目录上传到数据存储 `ds`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

ds.upload(src_dir='your source directory',
          target_path='your target path',
          overwrite=True,
          show_progress=True)
```

`target_path` 指定要上传的文件共享（或 blob 容器）中的位置。 默认设置为 `None`，在这种情况下数据将上传到根目录。 `overwrite=True` 将覆盖 `target_path` 中的任何现有数据。

或者，将单个文件的列表通过数据存储的 `upload_files()` 方法上传到数据存储。

### <a name="download"></a>下载
同样，可将数据从数据存储下载到本地文件系统。

```Python
ds.download(target_path='your target path',
            prefix='your prefix',
            show_progress=True)
```

`target_path` 是要将数据下载到的本地目录位置。 若要在文件共享（或 blob 容器）中指定要下载到的文件夹路径，请提供 `prefix` 的路径。 如果 `prefix` 是 `None`，将下载文件共享（或 blob 容器）的所有内容。

<a name="train"></a>
## <a name="access-datastores-during-training"></a>在定型期间访问数据存储

将数据存储提供给定型计算目标后, 可以通过在训练脚本中将路径作为参数传递给它, 在训练运行期间 (例如, 定型或验证数据) 进行访问。

下表列出[`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)了一些方法, 这些方法告诉计算目标如何在运行期间使用数据存储。

同样|方法|描述|
----|-----|--------
装载| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 用于在计算目标上装载数据存储。
下载|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|使用将数据存储的内容下载到指定`path_on_compute`的位置。 <br> 对于定型运行上下文, 此下载在运行之前发生。
上载|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 使用将文件从指定`path_on_compute`的位置上传到数据存储。 <br> 对于定型运行上下文, 此上载在运行之后发生。

 ```Python
import azureml.data
from azureml.data.data_reference import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

若要引用数据存储中的特定文件夹或文件并使其在计算目标上可用, 请使用数据[`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-)存储的函数。

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> Any `ds` `"$AZUREML_DATAREFERENCE_XXXX"`或`ds.path` object 解析为其值表示目标计算上装载/下载路径的格式的环境变量名称。 目标计算上的数据存储路径可能与训练脚本的执行路径不相同。

<a name="matrix"></a>
### <a name="training-compute-and-datastore-matrix"></a>训练计算和数据存储矩阵

下表显示了不同定型计算目标和数据存储方案的可用数据访问功能。 了解有关 Azure 机器学习的[训练计算目标](how-to-set-up-training-targets.md#compute-targets-for-training)的详细信息。

|计算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| Local|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|不可用         |不可用                                                                         |
| Azure 机器学习计算 |[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管道](concept-ml-pipelines.md)|[as_mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管道](concept-ml-pipelines.md)|不可用         |不可用                                                                         |
| 虚拟机               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |不可用         |不可用                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |不可用         |不可用                                                                         |
| 数据传输                  |[ML&nbsp;管道](concept-ml-pipelines.md)                                               |不可用                                           |[ML&nbsp;管道](concept-ml-pipelines.md)            |[ML&nbsp;管道](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;管道](concept-ml-pipelines.md)                                              |不可用                                           |[ML&nbsp;管道](concept-ml-pipelines.md)             |不可用                                                                         |
| Azure 批处理                    |[ML&nbsp;管道](concept-ml-pipelines.md)                                               |不可用                                           |不可用         |不可用                                                                         |
| Azure DataLake 分析       |不可用                                           |不可用                                           |[ML&nbsp;管道](concept-ml-pipelines.md)             |不可用                                                                         |

> [!NOTE]
> 在某些情况下, 使用`as_download()` `as_mount()`而不是高度迭代、大型数据进程运行速度更快; 这可以通过 experimentally 进行验证。

### <a name="examples"></a>示例 

下面的代码示例特定于在定型[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)期间访问数据存储的类。

此代码通过使用中`train.py` `script_params`定义的参数 (在指定的定型计算目标上) 从指定的源目录创建估计器。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': ds.as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

你还可以将数据存储的列表传递给估计器构造函数`inputs`参数, 以在数据存储中装载或复制数据。 此代码示例:
* 运行训练脚本`train.py`之前, 将`ds1`数据存储中的所有内容下载到计算目标
* 运行之前`'./foo'` `ds2` ,将数据存储中的文件夹`train.py`下载到计算目标
* 脚本运行后`'./bar.pkl'` , 从计算目标到数据存储`ds3`上传文件

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="access-datastores-during-for-scoring"></a>访问中的数据存储 for 评分

Azure 机器学习服务提供了多种方法来使用模型进行评分。 其中一些方法不提供对数据存储的访问权限。 使用下表来了解在评分期间允许访问数据存储的方法:

| 方法 | 数据存储访问 | 描述 |
| ----- | :-----: | ----- |
| [批处理预测](how-to-run-batch-predictions.md) | ✔ | 异步对大量数据进行预测。 |
| [Web 服务](how-to-deploy-and-where.md) | &nbsp; | 将模型部署为 web 服务。 |
| [IoT Edge 模块](how-to-deploy-and-where.md) | &nbsp; | 将模型部署到 IoT Edge 设备。 |

对于 SDK 不提供对数据存储的访问权限的情况, 可以使用相关的 Azure SDK 创建自定义代码来访问数据。 例如, 使用用于[Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)来访问存储在 blob 中的数据。

## <a name="next-steps"></a>后续步骤

* [定型模型](how-to-train-ml-models.md)

* [部署模型](how-to-deploy-and-where.md)
