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
ms.date: 02/25/2019
ms.custom: seodec18
ms.openlocfilehash: 25da234e4210c98ce17bdeb502493c5c649dab28
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481631"
---
# <a name="access-data-from-your-datastores"></a>访问你的数据存储中的数据

 在 Azure 机器学习服务中，数据存储是计算独立于位置的机制来访问存储而无需更改你的源代码。 是否编写培训采用路径作为参数，或直接向估算器提供一种数据存储，Azure 机器学习工作流将确保你的数据存储位置可访问，并提供给您的计算上下文。

本操作说明显示了示例的以下任务：
* [选择一种数据存储](#access)
* [获取数据](#get)
* [上传和下载数据到数据存储](#up-and-down)
* [在训练期间访问数据存储](#train)

## <a name="prerequisites"></a>必备组件

若要使用数据存储，首先需要一个[工作区](concept-azure-machine-learning-architecture.md#workspace)。

首先，请[创建新工作区](setup-create-workspace.md#sdk)或检索现有的工作区：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

<a name="access"></a>

## <a name="choose-a-datastore"></a>选择一种数据存储

可以使用默认的数据存储或自带。

### <a name="use-the-default-datastore-in-your-workspace"></a>在你的工作区中使用的默认数据存储

 每个工作区都有可以立即使用的已注册，默认数据存储。

若要获取工作区的默认数据存储：

```Python
ds = ws.get_default_datastore()
```

### <a name="register-your-own-datastore-with-the-workspace"></a>向工作区中注册你自己的数据存储

如果已有现有的 Azure 存储，可将其注册为工作区中的数据存储。   所有注册方法都是在[ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)类，并具有窗体 register_azure_ *。 

以下示例演示了将注册为一种数据存储的 Azure Blob 容器或 Azure 文件共享。

+ 有关**Azure Blob 容器数据存储**，使用 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)

  ```Python
  ds = Datastore.register_azure_blob_container(workspace=ws, 
                                               datastore_name='your datastore name', 
                                               container_name='your azure blob container name',
                                               account_name='your storage account name', 
                                               account_key='your storage account key',
                                               create_if_not_exists=True)
  ```

+ 有关**Azure 文件共享数据存储**，使用[ `register_azure_file_share()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 例如： 
  ```Python
  ds = Datastore.register_azure_file_share(workspace=ws, 
                                           datastore_name='your datastore name', 
                                           container_name='your file share name',
                                           account_name='your storage account name', 
                                           account_key='your storage account key',
                                           create_if_not_exists=True)
  ```

<a name="get"></a>

## <a name="find--define-datastores"></a>查找和定义数据存储

若要获取在当前工作区中注册的指定数据存储，请使用[ `get()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) :

```Python
#get named datastore from current workspace
ds = Datastore.get(ws, datastore_name='your datastore name')
```

若要获取给定工作区中的所有数据存储列表，请使用以下代码：

```Python
#list all datastores registered in current workspace
datastores = ws.datastores
for name, ds in datastores.items():
    print(name, ds.datastore_type)
```

若要定义一种不同的默认数据存储在当前工作区，请使用[ `set_default_datastore()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-):

```Python
#define default datastore for current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload--download-data"></a>上传和下载数据
[ `upload()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)并[ `download()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)下面的示例中所述的方法特定于和的相同操作[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)并[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)类。

### <a name="upload"></a>上载

 使用 Python SDK 将目录或单个文件上传到数据存储。

将目录上传到数据存储 `ds`:

```Python
import azureml.data
from azureml.data import AzureFileDatastore, AzureBlobDatastore

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
## <a name="access-datastores-during-training"></a>在训练期间访问数据存储

一旦您使你的数据存储在计算目标上，可以在训练运行 （例如，培训或验证数据） 只需向其传递路径，作为训练脚本中的参数来访问它。

下表列出[ `DataReference` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)告知如何在运行期间使用的数据存储的计算目标的方法。

方法|方法|描述|
----|-----|--------
装载| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)| 使用计算目标上安装数据存储。
下载|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)|使用你的数据存储的内容下载到指定的位置`path_on_compute`。 <br> 对于训练运行上下文中，此下载运行前发生。
上载|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)| 使用指定的位置中的文件上传`path_on_compute`到你的数据存储。 <br> 有关运行定型的上下文，此上传在运行后发生。

 ```Python
import azureml.data
from azureml.data import DataReference

ds.as_mount()
ds.as_download(path_on_compute='your path on compute')
ds.as_upload(path_on_compute='yourfilename')
```  

若要引用特定的文件夹或你的数据存储中的文件，并能在计算目标上，使用的数据存储[ `path()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#path-path-none--data-reference-name-none-)函数。

```Python
#download the contents of the `./bar` directory in ds to the compute target
ds.path('./bar').as_download()
```

> [!NOTE]
> 任何`ds`或`ds.path`对象将解析为格式的环境变量名`"$AZUREML_DATAREFERENCE_XXXX"`其值表示在目标计算上的装入/下载路径。 在目标计算上的数据存储路径可能不可用于训练脚本的执行路径相同。

### <a name="compute-context-and-datastore-type-matrix"></a>计算上下文和数据存储类型矩阵

以下矩阵将显示不同的计算上下文和数据存储方案的可用数据访问功能。 此矩阵中的"管道"一词是指使用数据存储作为输入或输出中的功能[Azure 机器学习管道](https://docs.microsoft.com/azure/machine-learning/service/concept-ml-pipelines)。

||本地计算|Azure 机器学习计算|数据传输|Databricks|HDInsight|Azure 批处理|Azure DataLake Analytics|虚拟机|
-|--|-----------|----------|---------|-----|--------------|---------|---------|
|AzureBlobDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] <br> 管道|管道|管道|[`as_download()`] <br> [`as_upload()`]|管道||[`as_download()`] <br> [`as_upload()`]|
|AzureFileDatastore|[`as_download()`] [`as_upload()`]|[`as_mount()`]<br> [`as_download()`] [`as_upload()`] Pipeline |||[`as_download()`] [`as_upload()`]|||[`as_download()`] [`as_upload()`]|
|AzureDataLakeDatastore|||管道|管道|||管道||
|AzureDataLakeGen2Datastore|||管道||||||
|AzureDataPostgresSqlDatastore|||管道||||||
|AzureSqlDatabaseDataDatastore|||管道||||||


> [!NOTE]
> 可能有在其中具有高迭代性，大型数据的进程运行速度更快的使用的方案 [`as_download()`] 而不是 [`as_mount()`]; 这可以呈下降趋式验证。

### <a name="examples"></a>示例 

下面的代码示例是特定于[ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类用于在训练过程中访问你的数据存储。

此代码将创建使用训练脚本，估算器`train.py`，从使用中定义的参数指示的源目录`script_params`，所有内容位于指定的计算目标。

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

您还可以在数据存储列表中向传递估算器构造函数`inputs`参数来装载或向/从你的数据存储复制。 此代码示例：
* 数据存储中的所有内容都下载`ds1`到计算目标之前训练脚本`train.py`运行
* 下载文件夹`'./foo'`数据存储中`ds2`到计算目标之前`train.py`运行
* 将文件上传`'./bar.pkl'`从数据存储到计算目标`ds3`运行脚本后

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[ds1.as_download(), ds2.path('./foo').as_download(), ds3.as_upload(path_on_compute='./bar.pkl')])
```

## <a name="next-steps"></a>后续步骤

* [定型模型](how-to-train-ml-models.md)

* [部署模型](how-to-deploy-and-where.md)
