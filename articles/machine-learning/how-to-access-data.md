---
title: 连接到 Azure 存储服务
titleSuffix: Azure Machine Learning
description: 了解如何在使用 Azure 机器学习训练期间使用数据存储安全连接到 Azure 存储服务
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: ca892b5f360f523ee2b5ff875dfb0707136a5ab5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383443"
---
# <a name="connect-to-azure-storage-services"></a>连接到 Azure 存储服务
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，了解如何通过 Azure 机器学习数据存储连接到 Azure 存储服务。 数据存储在与工作区关联的[密钥保管库中](https://azure.microsoft.com/services/key-vault/)存储连接信息，如订阅 ID 和令牌授权，因此您可以安全地访问存储，而无需在脚本中硬编码它们。

可以从[这些 Azure 存储解决方案](#matrix)创建数据存储。 对于不受支持的存储解决方案，以及为了在机器学习实验期间节省数据出口成本，我们建议您[将数据移动到](#move)支持的 Azure 存储解决方案。 

## <a name="prerequisites"></a>先决条件

需要：
- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

- 具有 [Azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或 [Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 Azure 存储帐户。

- [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)或通过 Python SDK 使用现有工作区。 导入`Workspace`和`Datastore`类，并使用 函数`config.json``from_config()`从文件加载订阅信息。 默认情况下，这将查找当前目录中的 JSON 文件，但您也可以指定路径参数以使用 指向`from_config(path="your/file/path")`该文件。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支持的数据存储服务类型

数据存储目前支持将连接信息存储到以下矩阵中列出的存储服务。

| 存储&nbsp;类型 | 身份验证&nbsp;类型 | [Azure&nbsp;&nbsp;机器学习工作室](https://ml.azure.com/) | [Azure&nbsp;&nbsp;机器学习&nbsp;Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;&nbsp;机器学习 CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;&nbsp;机器学习&nbsp;休息 API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;&nbsp;Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;数据存储&nbsp;第&nbsp;1 代](https://docs.microsoft.com/azure/data-lake-store/)| 服务主体| ✓ | ✓ | ✓ |✓
[Azure&nbsp;数据存储&nbsp;第&nbsp;2 代](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 服务主体| ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 身份验证 <br>服务主体| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL 身份验证| ✓ | ✓ | ✓ |✓
[MySQL&nbsp;&nbsp;的 Azure&nbsp;数据库](https://docs.microsoft.com/azure/mysql/overview) | SQL 身份验证|  | ✓* | ✓* |✓*
[数据砖&nbsp;文件系统&nbsp;](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| 无身份验证 | | ✓** | ✓ ** |✓** 

*MySQL 仅支持管道[数据传输步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)。 <br>
*仅支持管道[数据砖步骤的数据砖](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>存储指导原则

我们建议为[Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)创建数据存储。 标准和高级存储都可用于 blob。 尽管高级存储费用更高，但其吞吐速度也更快，可加速训练运行，特别是在针对大型数据集进行训练时。 有关存储帐户成本的信息，请参阅 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure 数据湖存储 Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json)构建在 Azure Blob 存储之上，专为企业大数据分析而设计。 Data Lake Storage Gen2 的一个基本部分是向 Blob 存储添加[分层命名空间](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 分层命名空间将对象/文件组织到目录层次结构中，以便进行有效的数据访问。

创建工作区时，会将 Azure blob 容器和 Azure 文件共享自动注册到工作区。 它们分别名为 `workspaceblobstore` 和 `workspacefilestore`。 `workspaceblobstore`用于存储工作区工件和机器学习实验日志。 `workspacefilestore`用于存储通过[计算实例](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)授权的笔记本和 R 脚本。 `workspaceblobstore` 容器设置为默认数据存储。

> [!IMPORTANT]
> 在设计器主页中打开示例时，Azure 机器学习设计器（预览版）将自动创建名为**azureml_globaldatasets**的数据存储。 此数据存储仅包含示例数据集。 **请不要**使用此数据存储进行任何机密数据访问。
> ![自动创建设计器示例数据集的数据存储](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>创建和注册数据存储

将 Azure 存储解决方案注册为数据存储时，将自动创建该数据存储并将其注册到特定工作区。 可以使用 Python SDK 或 Azure 机器学习工作室创建数据存储并将其注册到工作区。

>[!IMPORTANT]
> 作为初始数据存储创建和注册过程的一部分，Azure 机器学习验证基础存储服务是否存在，并且用户提供的委托（用户名、服务主体或 SAS 令牌）是否有权访问该存储。 但是，对于 Azure 数据存储第 1 代和第 2 代数据存储，此验证将在以后调用[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py)数据[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)访问方法时进行。 
<br><br>
创建数据存储后，仅对需要访问基础存储容器的方法执行此验证，**而不是**每次检索数据存储对象。 例如，如果要从数据存储下载文件，则进行验证;但是，如果您只想更改默认数据存储，则不会发生验证。

### <a name="python-sdk"></a>Python SDK

所有寄存器方法都在类上[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)，并且具有窗体。 `register_azure_*`

您可以在 Azure`register()`[门户](https://portal.azure.com)上找到填充方法所需的信息。
在左窗格中选择“存储帐户”****，并选择要注册的存储帐户。 “概述”**** 页提供了帐户名称、容器和文件共享名称等信息。 

* 对于身份验证项（如帐户密钥或 SAS 令牌），转到 **"设置"** 窗格上的 **"访问密钥**"。 

* 对于服务主体项目（如租户 ID 和客户端 ID），请转到**应用注册**并选择要使用的应用。 其相应的 **"概述"** 页将包含这些项目。

> [!IMPORTANT]
> 如果您的存储帐户位于虚拟网络中，则仅支持**通过 SDK**创建 Blob、文件共享、ADLS 第 1 代和 ADLS 第 2 代数据存储。 若要授权工作区访问存储帐户，请将参数 `grant_workspace_access` 设置为 `True`。

以下示例演示如何将 Azure Blob 容器、Azure 文件共享和 Azure 数据存储第 2 代作为数据存储注册。 有关其他存储服务，请参阅[适用`register_azure_*`方法的参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)。

#### <a name="blob-container"></a>Blob 容器

要将 Azure Blob 容器注册为数据存储，[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)请使用 。

以下代码会创建 `blob_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储通过使用提供的帐户访问`my-container-name`密钥访问存储帐户`my-account-name`上的 Blob 容器。

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```

#### <a name="file-share"></a>文件共享

要将 Azure 文件共享注册为数据存储，请使用[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

以下代码会创建 `file_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储通过使用提供的帐户访问`my-fileshare-name`密钥访问存储帐户`my-account-name`上的文件共享。

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```

#### <a name="azure-data-lake-storage-generation-2"></a>Azure 数据存储第 2 代

对于 Azure 数据湖存储第 2 代 （ADLS 第 2 代） 数据存储，请使用[register_azure_data_lake_gen2（）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-)注册连接到 Azure DataLake 第 2 代存储的凭据数据存储，并具有[服务主体权限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。 为了利用服务主体，您需要[注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，并授予服务主体正确的数据访问权限。 详细了解为[ADLS 第 2 代设置的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

以下代码会创建 `adlsgen2_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储通过使用提供的服务主体凭据访问存储`test`帐户上的`account_name`文件系统。

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室 

在 Azure 机器学习工作室中通过几个步骤创建新的数据存储：

> [!IMPORTANT]
> 如果您的存储帐户位于虚拟网络中，则仅支持[通过 SDK](#python-sdk)创建数据存储。 

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。
1. 在左窗格中的“管理”下，选择“数据存储”********。
1. 选择“+ 新建数据存储”****。
1. 填写表单以创建新的数据存储。 窗体根据 Azure 存储类型和身份验证类型的选择自行更新。
  
可以使用 [Azure 门户](https://portal.azure.com)找到必要的信息来填充该表单。 在左窗格中选择“存储帐户”****，并选择要注册的存储帐户。 “概述”**** 页提供了帐户名称、容器和文件共享名称等信息。 

* 对于身份验证项（如帐户密钥或 SAS 令牌），转到 **"设置"** 窗格上的 **"访问密钥**"。 

* 对于服务主体项目（如租户 ID 和客户端 ID），请转到**应用注册**并选择要使用的应用。 其相应的 **"概述"** 页将包含这些项目。 

下面的示例演示创建 Azure blob 数据存储时表单的外观： 
    
![新数据存储的表单](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>从工作区获取数据存储

要在当前工作区中注册特定数据存储，请使用 类上的[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)`Datastore`静态方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
要获取在给定工作区中注册的数据存储列表，可以在工作区对象上使用 属性[`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)：

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

若要获取工作区的默认数据存储，请使用以下行：

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>上传和下载数据

以下[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)示例中[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)介绍的 和 方法特定于[AzureBlob 数据存储](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)类，并且操作相同。

### <a name="upload"></a>上载

使用 Python SDK 将目录或独立文件上传到数据存储：

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` 参数指定要上传的文件共享（或 blob 容器）中的位置。 它默认为 `None`，因此数据上传到根目录。 如果 `overwrite=True`，则将覆盖 `target_path` 处的任何现有数据。

也可通过 `upload_files()` 方法将一系列独立文件上传到数据存储。

### <a name="download"></a>下载

将数据从数据存储下载到本地文件系统：

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 参数是要将数据下载到的本地目录位置。 若要在文件共享（或 blob 容器）中指定要下载到的文件夹路径，请提供 `prefix` 的路径。 如果 `prefix` 为 `None`，将下载文件共享（或 blob 容器）的所有内容。

<a name="train"></a>

## <a name="access-your-data-during-training"></a>在训练期间访问数据

要与数据存储中的数据进行交互，或将数据打包到用于机器学习任务的可消耗对象（如培训）时，[请创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。 数据集提供将表格数据加载到 Pandas 或 Spark 数据帧的功能。 数据集还提供从 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 下载或装载任何格式的文件的功能。 [详细了解如何使用数据集进行训练](how-to-train-with-datasets.md)。

### <a name="accessing-source-code-during-training"></a>在训练期间访问源代码

Azure Blob 存储具有比 Azure 文件共享更快的吞吐速度，并将扩展到大量并行启动的作业。 出于此原因，建议将运行配置为使用 Blob 存储来传输源代码文件。

以下代码示例在运行配置中指定用于源代码传输的 Blob 数据存储。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在评分期间访问数据

Azure 机器学习提供多种方法来使用模型进行评分。 其中一些方法不提供对数据存储的访问权限。 使用下表了解允许在评分期间访问数据存储的方法：

| 方法 | 数据存储访问 | 描述 |
| ----- | :-----: | ----- |
| [成批预测](how-to-use-parallel-run-step.md) | ✔ | 以异步方式对大量数据进行预测。 |
| [Web 服务](how-to-deploy-and-where.md) | &nbsp; | 将模型部署为 Web 服务。 |
| [Azure IoT Edge 模块](how-to-deploy-and-where.md) | &nbsp; | 将模型部署到 IoT Edge 设备。 |

对于 SDK 不提供对数据存储的访问权限的情况，也许可以通过使用相关 Azure SDK 访问数据以创建自定义代码。 例如，可使用[适用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python) 这一客户端库来访问存储在 blob 或文件中的数据。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>将数据移动到受支持的 Azure 存储解决方案

Azure 机器学习支持访问 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 中的数据。 如果使用不支持的存储，我们建议您使用[Azure 数据工厂和这些步骤](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)将数据移动到受支持的 Azure 存储解决方案。 将数据移动到支持的存储有助于在机器学习试验期间节省数据出口成本。 

Azure 数据工厂具有超过 80 个预生成的连接器，可提供高效且可复原的数据传输，无需额外付费。 这些连接器包括 Azure 数据服务、本地数据源、Amazon S3 和 Redshift 以及 Google BigQuery。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [训练模型](how-to-train-ml-models.md)
* [部署模型](how-to-deploy-and-where.md)
