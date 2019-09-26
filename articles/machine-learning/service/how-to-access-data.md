---
title: 访问 Azure 存储服务中的数据
titleSuffix: Azure Machine Learning
description: 了解如何使用数据存储在训练期间访问 Azure 存储服务 Azure 机器学习
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 08/2/2019
ms.custom: seodec18
ms.openlocfilehash: 9de3232bcd7908f775dadff4dc584f2a687b0c68
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299759"
---
# <a name="access-data-in-azure-storage-services"></a>访问 Azure 存储服务中的数据

本文介绍如何通过 Azure 机器学习数据存储轻松访问 Azure 存储服务中的数据。 数据存储用于存储连接信息，如订阅 ID 和令牌授权。 使用数据存储可以访问存储，而无需在脚本中对连接信息进行硬编码。

本操作说明显示以下任务的示例:
* [注册数据存储](#access)
* [从工作区获取数据存储](#get)
* [使用数据存储上传和下载数据](#up-and-down)
* [在定型过程中访问数据](#train)

## <a name="prerequisites"></a>先决条件

若要使用数据存储，首先需要一个[工作区](concept-workspace.md)。

首先，请[创建新工作区](how-to-manage-workspace.md)或检索现有的工作区：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="register-datastores"></a>注册数据存储

所有寄存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)类上，格式为 register_azure_ *。

以下示例演示如何将 Azure Blob 容器或 Azure 文件共享注册为数据存储。

+ 对于**Azure Blob 容器数据存储**, 请使用[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)

  ```Python
  datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                      datastore_name='your datastore name', 
                                                      container_name='your azure blob container name',
                                                      account_name='your storage account name', 
                                                      account_key='your storage account key',
                                                      create_if_not_exists=True)
  ```

+ 对于**Azure 文件共享数据存储**, 请[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)使用。 例如： 
  ```Python
  datastore = Datastore.register_azure_file_share(workspace=ws, 
                                                  datastore_name='your datastore name', 
                                                  file_share_name='your file share name',
                                                  account_name='your storage account name', 
                                                  account_key='your storage account key',
                                                  create_if_not_exists=True)
  ```

####  <a name="storage-guidance"></a>存储指导原则

建议 Azure Blob 容器。 标准存储和高级存储都适用于 blob。 尽管更昂贵, 但我们建议使用高级存储, 因为这样可以提高定型运行的速度, 尤其是在针对大型数据集进行训练时。 请参阅[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)中的存储帐户成本信息。

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>从工作区获取数据存储

若要获取在当前工作区中注册的特定数据存储[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) ，请对数据存储类使用静态方法：

```Python
#get named datastore from current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要获取在给定工作区中注册的数据存储的列表，可以在[`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)工作区对象上使用属性：

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

创建工作区时，azure Blob 容器和 azure 文件共享分别注册到名为`workspaceblobstore`和`workspacefilestore`的工作区。 它们存储在附加到工作区的存储帐户中设置的 Blob 容器和文件共享的连接信息。 `workspaceblobstore`设置为默认数据存储。

若要获取工作区的默认数据存储：

```Python
datastore = ws.get_default_datastore()
```

若要为当前工作区定义不同的默认数据存储[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-) ，请对工作区对象使用方法：

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>上载 & 下载数据
下面[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)的[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)示例中所述的和方法特定于和对[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)类具有相同的作用。

### <a name="upload"></a>上传

 使用 Python SDK 将目录或单个文件上传到数据存储。

将目录上传到数据存储 `datastore`:

```Python
import azureml.data
from azureml.data.azure_storage_datastore import AzureFileDatastore, AzureBlobDatastore

datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path`参数指定要上传的文件共享（或 blob 容器）中的位置。 默认设置为 `None`，在这种情况下数据将上传到根目录。 当`overwrite=True`覆盖上`target_path`的任何现有数据时。

或通过`upload_files()`方法将单个文件的列表上载到数据存储。

### <a name="download"></a>下载

同样，可将数据从数据存储下载到本地文件系统。

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path`参数是要将数据下载到其中的本地目录的位置。 若要在文件共享（或 blob 容器）中指定要下载到的文件夹路径，请提供 `prefix` 的路径。 如果 `prefix` 是 `None`，将下载文件共享（或 blob 容器）的所有内容。

<a name="train"></a>
## <a name="access-your-data-during-training"></a>在定型过程中访问数据

> [!IMPORTANT]
> 使用[Azure 机器学习数据集（预览版）](how-to-create-register-datasets.md)是在训练中访问数据的新建议方法。 数据集提供了将表格数据加载到 pandas 或 spark 数据帧的函数，以及从 Azure Blob、Azure 文件、Azure Data Lake 第1代、Azure Data Lake 第2代、Azure SQL、Azure PostgreSQL 下载任何格式的文件的功能。 详细了解[如何对数据集进行训练](how-to-train-with-datasets.md)。

下表列出了一些方法，这些方法告诉计算目标如何在运行期间使用数据存储。 

同样|方法|描述|
----|-----|--------
装载| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| 用于在计算目标上装载数据存储。
下载|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|使用将数据存储的内容下载到指定`path_on_compute`的位置。 <br><br> 此下载在运行之前发生。
上传|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| 使用将文件从指定`path_on_compute`的位置上传到数据存储。 <br><br> 此上传在运行之后发生。

若要引用数据存储中的特定文件夹或文件并使其在计算目标上可用，请使用[`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-)数据存储方法。

```Python
#to mount the full contents in your storage to the compute target
datastore.as_mount()

#to download the contents of the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 任何指定`datastore`的`datastore.path`或对象解析为格式`"$AZUREML_DATAREFERENCE_XXXX"`的环境变量名称，其值表示目标计算上的装载/下载路径。 目标计算上的数据存储路径可能与训练脚本的执行路径不相同。

### <a name="examples"></a>示例 

下面的代码示例特定于在定型[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)期间访问数据的类。 

`script_params`包含 entry_script 的参数的字典。 使用它传入数据存储，并说明数据在计算目标上如何可用。 通过我们的端到端[教程](tutorial-train-models-with-aml.md)了解更多。

```Python
from azureml.train.estimator import Estimator

script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

你还可以将数据存储的列表传递给估计器构造函数`inputs`参数，以在数据存储中装载或复制数据。 此代码示例:
* 运行训练脚本`train.py`之前， `datastore1`将所有内容下载到计算目标中
* 运行之前`'./foo'` `datastore2` ，将文件夹下载`train.py`到计算目标中
* 脚本运行`datastore3`后`'./bar.pkl'` ，从计算目标将文件上传到

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```

### <a name="compute-and-datastore-matrix"></a>计算和数据存储矩阵

数据存储目前支持将连接信息存储到下表中列出的存储服务。 此矩阵显示了不同计算目标和数据存储方案的可用数据访问功能。 详细了解 Azure 机器学习的[计算目标](how-to-set-up-training-targets.md#compute-targets-for-training)。

|计算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 本地|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|不可用         |不可用                                                                         |
| Azure 机器学习计算 |[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管道](concept-ml-pipelines.md)|[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、 [ML&nbsp;管道](concept-ml-pipelines.md)|不可用         |不可用                                                                         |
| 虚拟机               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |不可用         |不可用                                                                         |
| HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |不可用         |不可用                                                                         |
| 数据传输                  |[ML&nbsp;管道](concept-ml-pipelines.md)                                               |不可用                                           |[ML&nbsp;管道](concept-ml-pipelines.md)            |[ML&nbsp;管道](concept-ml-pipelines.md)                                                                            |
| Databricks                     |[ML&nbsp;管道](concept-ml-pipelines.md)                                              |不可用                                           |[ML&nbsp;管道](concept-ml-pipelines.md)             |不可用                                                                         |
| Azure Batch                    |[ML&nbsp;管道](concept-ml-pipelines.md)                                               |不可用                                           |不可用         |不可用                                                                         |
| Azure DataLake 分析       |不可用                                           |不可用                                           |[ML&nbsp;管道](concept-ml-pipelines.md)             |不可用                                                                         |

> [!NOTE]
> 在某些情况下, 使用`as_download()` `as_mount()`而不是高度迭代、大型数据进程运行速度更快; 这可以通过 experimentally 进行验证。

### <a name="accessing-source-code-during-training"></a>在定型期间访问源代码

与 Azure 文件共享相比，azure blob 存储具有更高的吞吐量速度，并将扩展到大量并行启动的作业。 出于此原因，我们建议配置运行以使用 blob 存储来传输源代码文件。

下面的代码示例在运行配置中指定用于源代码传输的 blob 数据存储。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在评分过程中访问数据

Azure 机器学习提供多种方法来使用模型进行评分。 其中一些方法不提供对数据存储的访问权限。 使用下表来了解在评分期间允许访问数据存储的方法:

| 方法 | 数据存储访问 | 描述 |
| ----- | :-----: | ----- |
| [批处理预测](how-to-run-batch-predictions.md) | ✔ | 异步对大量数据进行预测。 |
| [Web 服务](how-to-deploy-and-where.md) | &nbsp; | 将模型部署为 web 服务。 |
| [IoT Edge 模块](how-to-deploy-and-where.md) | &nbsp; | 将模型部署到 IoT Edge 设备。 |

如果 SDK 无法提供对数据存储的访问权限，则可以使用相关的 Azure SDK 创建自定义代码来访问数据。 例如，[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)是一个客户端库，可用于访问 blob 或文件中存储的数据。


## <a name="next-steps"></a>后续步骤

* [定型模型](how-to-train-ml-models.md)

* [部署模型](how-to-deploy-and-where.md)
