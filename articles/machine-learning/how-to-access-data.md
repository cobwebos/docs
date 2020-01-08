---
title: 访问 Azure 存储服务中的数据
titleSuffix: Azure Machine Learning
description: 了解如何使用数据存储在训练期间安全连接到 Azure 存储服务 Azure 机器学习
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ylxiong
author: YLXiong1125
ms.reviewer: nibaccam
ms.date: 12/10/2019
ms.custom: seodec18
ms.openlocfilehash: ac6ef6341013ca13d5a9f27be8897365c1c2155d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540940"
---
# <a name="access-data-in-azure-storage-services"></a>访问 Azure 存储服务中的数据
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何通过 Azure 机器学习数据存储轻松访问 Azure 存储服务中的数据。 数据存储用于存储连接信息，如订阅 ID 和令牌授权。 当你使用数据存储时，你可以访问存储，而无需在脚本中对连接信息进行硬编码。 

可以从[这些 Azure 存储解决方案](#matrix)创建数据存储。 对于不支持的存储解决方案，并在机器学习试验期间节省数据出口成本，我们建议[将数据移动](#move)到支持的 Azure 存储解决方案。 

## <a name="prerequisites"></a>必备组件
需要：
- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 试用[Azure 机器学习免费或付费版本](https://aka.ms/AMLFree)。

- 使用[azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或[azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 azure 存储帐户。

- [用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或访问[Azure 机器学习 studio](https://ml.azure.com/)。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)，或者通过 Python SDK 使用现有工作区：

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

<a name="access"></a>

## <a name="create-and-register-datastores"></a>创建和注册数据存储

将 Azure 存储解决方案注册为数据存储时，会自动在特定的工作区中创建该数据存储。 可以使用 Python SDK 或 Azure 机器学习 studio 创建数据存储并将其注册到工作区。

### <a name="python-sdk"></a>Python SDK

所有寄存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)类上，并将窗体 `register_azure_*`。

您可以使用[Azure 门户](https://portal.azure.com)查找填充 `register()` 方法所需的信息：

1. 选择左窗格中的 "**存储帐户**"，并选择要注册的存储帐户。 
2. 有关帐户名称、容器和文件共享名称等信息，请参阅**概述**页。 对于身份验证信息，如帐户密钥或 SAS 令牌，请在 "**设置**" 窗格中转到 "**访问密钥**"。 

> [!IMPORTANT]
> 如果你的存储帐户位于虚拟网络中，则仅支持创建 Azure blob 数据存储。 若要向你的工作区授予对你的存储帐户的访问权限，请将参数 `grant_workspace_access` 设置为 `True`。

以下示例演示如何将 Azure blob 容器、Azure 文件共享和 Azure SQL 数据注册为数据存储。

#### <a name="blob-container"></a>Blob 容器

若要将 Azure blob 容器注册为数据存储，请使用[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

以下代码创建 `blob_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户密钥访问 `my-account-name` 存储帐户上的 `my-container-name` blob 容器。

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>文件共享

若要将 Azure 文件共享注册为数据存储，请使用[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

以下代码创建 `file_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户密钥访问 `my-account-name` 存储帐户上的 `my-fileshare-name` 文件共享。

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                 datastore_name=file_datastore_name, 
                                                 file_share_name=file_share_name, 
                                                 account_name=account_name,
                                                 account_key=account_key)
```

#### <a name="sql-data"></a>SQL 数据

对于 Azure SQL 数据存储，请使用[register_azure_sql_database （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-sql-database-workspace--datastore-name--server-name--database-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--endpoint-none--overwrite-false--username-none--password-none-)来注册使用 SQL 身份验证或服务主体权限连接到 Azure SQL 数据库的凭据数据存储。 

通过 SQL 身份验证进行注册：

```python
sql_datastore_name="azsqlsdksql"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the Azure SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the Azure SQL database
username=os.getenv("SQL_USER_NAME", "<my-sql-user-name>") # Username of the database user to access the database
password=os.getenv("SQL_USER_PASSWORD", "<my-sql-user-password>") # Password of the database user to access the database

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                  datastore_name=sql_datastore_name,
                                                  server_name=server_name,
                                                  database_name=database_name,
                                                  username=username,
                                                  password=password)

```

若要通过服务主体进行注册：

```python 
sql_datastore_name="azsqlsdksp"
server_name=os.getenv("SQL_SERVERNAME", "<my-server-name>") # Name of the SQL server
database_name=os.getenv("SQL_DATBASENAME", "<my-database-name>") # Name of the SQL database
client_id=os.getenv("SQL_CLIENTNAME", "<my-client-id>") # Client ID of the service principal with permissions to access the database
client_secret=os.getenv("SQL_CLIENTSECRET", "<my-client-secret>") # Secret of the service principal
tenant_id=os.getenv("SQL_TENANTID", "<my-tenant-id>") # Tenant ID of the service principal

sql_datastore = Datastore.register_azure_sql_database(workspace=ws,
                                                      datastore_name=sql_datastore_name,
                                                      server_name=server_name,
                                                      database_name=database_name,
                                                      client_id=client_id,
                                                      client_secret=client_secret,
                                                      tenant_id=tenant_id)
```

#### <a name="storage-guidance"></a>存储指导原则

建议使用 Azure blob 容器。 标准存储和高级存储都适用于 blob。 尽管高级存储开销较高，但其速度越快，吞吐量也可能会提高定型运行的速度，特别是在您针对大型数据集进行训练时。 有关存储帐户的成本的信息，请参阅[Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

### <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室 

在 Azure 机器学习 studio 中的几个步骤中创建新的数据存储：

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。
1. 在 "**管理**" 下的左窗格中选择 "**数据存储**"。
1. 选择 " **+ 新建数据存储**"。
1. 为新的数据存储填写表单。 该表单会根据你在 Azure 存储类型和身份验证类型中的选择智能地自行更新。
  
您可以找到在[Azure 门户](https://portal.azure.com)上填充窗体所需的信息。 选择左窗格中的 "**存储帐户**"，并选择要注册的存储帐户。 "**概述**" 页提供了帐户名称、容器和文件共享名称等信息。 对于身份验证项（如帐户密钥或 SAS 令牌），请在 "**设置**" 窗格中转到 "**帐户密钥**"。

下面的示例演示创建 Azure blob 数据存储时窗体的外观： 
    
![新数据存储的窗体](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>从工作区获取数据存储

若要获取在当前工作区中注册的特定数据存储，请对 `Datastore` 类使用[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)静态方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要获取在给定工作区中注册的数据存储的列表，可以使用工作区对象的[`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)属性：

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

创建工作区时，会自动将 Azure blob 容器和 Azure 文件共享注册到工作区。 它们分别命名为 `workspaceblobstore` 和 `workspacefilestore`。 它们存储在附加到工作区的存储帐户中预配的 blob 容器和文件共享的连接信息。 `workspaceblobstore` 容器设置为默认数据存储。

若要获取工作区的默认数据存储，请使用以下行：

```Python
datastore = ws.get_default_datastore()
```

若要为当前工作区定义不同的默认数据存储，请对工作区对象使用[`set_default_datastore()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#set-default-datastore-name-)方法：

```Python
# Define the default datastore for the current workspace
ws.set_default_datastore('your datastore name')
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>上传和下载数据

以下示例中所述的[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)和[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)方法特定于、和对[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)类的操作相同。

### <a name="upload"></a>上载

使用 Python SDK 将目录或单个文件上传到数据存储：

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` 参数指定要上传的文件共享（或 blob 容器）中的位置。 默认为 `None`，因此数据上传到 root。 如果 `overwrite=True`，则将覆盖 `target_path` 上的任何现有数据。

还可以通过 `upload_files()` 方法将单个文件的列表上载到数据存储。

### <a name="download"></a>下载

将数据从数据存储下载到本地文件系统：

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 参数是要将数据下载到其中的本地目录的位置。 若要在文件共享（或 blob 容器）中指定要下载到的文件夹路径，请提供 `prefix` 的路径。 如果 `None``prefix`，则将下载文件共享（或 blob 容器）的所有内容。

<a name="train"></a>
## <a name="access-your-data-during-training"></a>在定型过程中访问数据

> [!IMPORTANT]
> 现在，我们建议使用[Azure 机器学习数据集](how-to-create-register-datasets.md)在训练中访问数据。 数据集提供了将表格数据加载到 pandas 或 Spark 数据帧的函数。 数据集还可以从 Azure Blob 存储、Azure 文件、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 下载或装载任何格式的文件。 [详细了解如何对数据集进行训练](how-to-train-with-datasets.md)。

下表列出了一些方法，这些方法告诉计算目标如何在运行期间使用数据存储： 

道路|方法|Description|
----|-----|--------
装载| [`as_mount()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-mount--)| 用于在计算目标上装载数据存储。 装载数据存储后，你的计算目标可访问你的数据存储的所有文件。
下载|[`as_download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-download-path-on-compute-none-)|使用将数据存储的内容下载到 `path_on_compute`指定的位置。 <br><br> 此下载在运行之前发生。
上载|[`as_upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#as-upload-path-on-compute-none-)| 使用将文件从 `path_on_compute` 指定的位置上传到数据存储。 <br><br> 此上传在运行之后发生。

若要引用数据存储中的特定文件夹或文件并使其在计算目标上可用，请使用数据存储[`path()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.abstractazurestoragedatastore?view=azure-ml-py#path-path-none--data-reference-name-none-)方法：

```Python
# To mount the full contents in your storage to the compute target
datastore.as_mount()

# To download the contents of only the `./bar` directory in your storage to the compute target
datastore.path('./bar').as_download()
```
> [!NOTE]
> 任何指定 `datastore` 或 `datastore.path` 对象都解析为 `"$AZUREML_DATAREFERENCE_XXXX"`格式的环境变量名称。 此名称的值表示计算目标上的装载/下载路径。 计算目标上的数据存储路径可能与训练脚本的执行路径不相同。

### <a name="examples"></a>示例 

建议使用[`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类在训练过程中访问数据。 

`script_params` 变量是包含 `entry_script`参数的字典。 使用它传入数据存储，并说明数据在计算目标上如何可用。 从[端到端教程](tutorial-train-models-with-aml.md)了解更多详细信息。

```Python
from azureml.train.estimator import Estimator

# Notice that '/' is in front, which indicates the absolute path
script_params = {
    '--data_dir': datastore.path('/bar').as_mount()
}

est = Estimator(source_directory='your code directory',
                entry_script='train.py',
                script_params=script_params,
                compute_target=compute_target
                )
```

你还可以将数据存储列表传入到 `Estimator` 构造函数的 `inputs` 参数，以便在数据存储中装载或复制数据。 此代码示例：
* 在运行 `train.py` 训练脚本之前，将 `datastore1` 中的所有内容下载到计算目标。
* 在运行 `train.py` 之前，将 `datastore2` 中的 `'./foo'` 文件夹下载到计算目标。
* 运行脚本后，将 `'./bar.pkl'` 文件从计算目标上传到 `datastore3`。

```Python
est = Estimator(source_directory='your code directory',
                compute_target=compute_target,
                entry_script='train.py',
                inputs=[datastore1.as_download(), datastore2.path('./foo').as_download(), datastore3.as_upload(path_on_compute='./bar.pkl')])
```
如果希望使用 `RunConfig` 对象进行定型，则需要设置[`DataReference`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)对象。 

下面的代码演示如何使用估算管道中的 `DataReference` 对象。 有关完整示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-estimatorstep.ipynb)。

```Python
from azureml.core import Datastore
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

def_blob_store = Datastore(ws, "workspaceblobstore")

input_data = DataReference(
       datastore=def_blob_store,
       data_reference_name="input_data",
       path_on_datastore="20newsgroups/20news.pkl")

output = PipelineData("output", datastore=def_blob_store)
```
<a name="matrix"></a>

### <a name="compute-and-datastore-matrix"></a>计算和数据存储矩阵

数据存储目前支持将连接信息存储到下表中列出的存储服务。 此矩阵显示了不同计算目标和数据存储方案的可用数据访问功能。 [详细了解 Azure 机器学习的计算目标](how-to-set-up-training-targets.md#compute-targets-for-training)。

|计算|[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)                                       |[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)                                      |[AzureDataLakeDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakedatastore?view=azure-ml-py) |[AzureDataLakeGen2Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_data_lake_datastore.azuredatalakegen2datastore?view=azure-ml-py) [AzurePostgreSqlDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_postgre_sql_datastore.azurepostgresqldatastore?view=azure-ml-py) [AzureSqlDatabaseDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_sql_database_datastore.azuresqldatabasedatastore?view=azure-ml-py) |
|--------------------------------|----------------------------------------------------------|----------------------------------------------------------|------------------------|----------------------------------------------------------------------------------------|
| 本地|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)|N/A         |N/A                                                                         |
| Azure 机器学习计算 |[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、[机器学习管道](concept-ml-pipelines.md)|[as_mount （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-mount--)、 [as_download （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-)、 [as_upload （）](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)、[机器学习管道](concept-ml-pipelines.md)|N/A         |N/A                                                                         |
| 虚拟机               |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-), [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                           | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| Azure HDInsight                      |[as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            | [as_download()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-download-path-on-compute-none--overwrite-false-) [as_upload()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py#as-upload-path-on-compute-none--overwrite-false-)                            |N/A         |N/A                                                                         |
| 数据传输                  |[机器学习管道](concept-ml-pipelines.md)                                               |N/A                                           |[机器学习管道](concept-ml-pipelines.md)            |[机器学习管道](concept-ml-pipelines.md)                                                                            |
| Azure Databricks                     |[机器学习管道](concept-ml-pipelines.md)                                              |N/A                                           |[机器学习管道](concept-ml-pipelines.md)             |N/A                                                                         |
| Azure 批处理                    |[机器学习管道](concept-ml-pipelines.md)                                               |N/A                                           |N/A         |N/A                                                                         |
| Azure Data Lake Analytics       |N/A                                           |N/A                                           |[机器学习管道](concept-ml-pipelines.md)             |N/A                                                                         |

> [!NOTE]
> 在某些情况下，使用 `as_download()` 而不是 `as_mount()`，可以更快地运行大量数据进程。 你可以验证此 experimentally。

### <a name="accessing-source-code-during-training"></a>在定型期间访问源代码

与 Azure 文件共享相比，azure Blob 存储具有更高的吞吐量速度，并将扩展到大量并行启动的作业。 出于此原因，我们建议配置运行以使用 Blob 存储来传输源代码文件。

下面的代码示例在运行配置中指定用于源代码传输的 blob 数据存储：

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在评分过程中访问数据

Azure 机器学习提供多种方法来使用模型进行评分。 其中一些方法不提供对数据存储的访问权限。 使用下表来了解在评分期间允许访问数据存储的方法：

| 方法 | 数据存储访问 | Description |
| ----- | :-----: | ----- |
| [批处理预测](how-to-run-batch-predictions.md) | ✔ | 异步对大量数据进行预测。 |
| [Web 服务](how-to-deploy-and-where.md) | &nbsp; | 将模型部署为 web 服务。 |
| [Azure IoT Edge 模块](how-to-deploy-and-where.md) | &nbsp; | 将模型部署到 IoT Edge 设备。 |

如果 SDK 无法提供对数据存储的访问权限，则可以通过使用相关的 Azure SDK 访问数据来创建自定义代码。 例如，[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)是一个客户端库，可用于访问 blob 或文件中存储的数据。

<a name="move"></a>
## <a name="move-data-to-supported-azure-storage-solutions"></a>将数据移到支持的 Azure 存储解决方案

Azure 机器学习支持访问 Azure Blob 存储、Azure 文件、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 中的数据。 如果使用不受支持的存储，建议使用 Azure 数据工厂将数据移动到支持的 Azure 存储解决方案。 将数据移动到支持的存储可以帮助你在机器学习试验期间节省数据传出成本。 

Azure 数据工厂提供高效且复原的数据传输，其中包含80个以上的预生成连接器，无需额外付费。 这些连接器包括 Azure 数据服务、本地数据源、Amazon S3 和 Redshift 以及 Google BigQuery。 [按照分步指南操作，使用 Azure 数据工厂移动数据](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

## <a name="next-steps"></a>后续步骤

* [定型模型](how-to-train-ml-models.md)
* [部署模型](how-to-deploy-and-where.md)
