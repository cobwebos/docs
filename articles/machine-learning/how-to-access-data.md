---
title: 连接到 Azure 存储服务
titleSuffix: Azure Machine Learning
description: 了解如何在使用 Azure 机器学习训练期间使用数据存储安全地连接到 Azure 存储服务
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 904738d73aaa0580773a085c70cd74f4240fc4b7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773933"
---
# <a name="connect-to-azure-storage-services"></a>连接到 Azure 存储服务
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何通过 Azure 机器学习数据存储连接到 Azure 存储服务。 数据存储会存储连接信息，例如与工作区关联的 [Key Vault](https://azure.microsoft.com/services/key-vault/) 中的订阅 ID 和令牌授权，让你能够安全地访问存储，而无需在脚本中对其进行硬编码。 若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据存储，请参阅[安全地访问数据](concept-data.md#data-workflow)一文。

可从[这些 Azure 存储解决方案](#matrix)创建数据存储。 对于不受支持的存储解决方案，以及为了在机器学习试验期间节省数据出口成本，建议[数据](#move)到受支持的 Azure 存储解决方案。 

## <a name="prerequisites"></a>先决条件

需要：
- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用 [Azure 机器学习的免费版或付费版](https://aka.ms/AMLFree)。

- 具有 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或 [Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 Azure 存储帐户。

- [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md) 或通过 Python SDK 使用现有工作区。 导入 `Workspace` 和 `Datastore` 类，然后使用函数 `from_config()` 从 `config.json` 文件加载订阅信息。 默认情况下，这将在当前目录中查找 JSON 文件，但你也可使用 `from_config(path="your/file/path")` 指定路径参数以指向该文件。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支持的数据存储服务类型

数据存储目前支持将连接信息存储到下表中列出的存储服务。

| 存储类型 | 身份验证类型 | [Azure&nbsp;机器学习工作室](https://ml.azure.com/) | [Azure&nbsp;机器学习&nbsp;Python&nbsp;SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;机器学习&nbsp;CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;机器学习&nbsp;Rest&nbsp;API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| 服务主体| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 服务主体| ✓ | ✓ | ✓ |✓
[Azure&nbsp;SQL&nbsp;数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 身份验证 <br>服务主体| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL 身份验证| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Database&nbsp;for&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | SQL 身份验证|  | ✓* | ✓* |✓*
[Databricks&nbsp;文件系统](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| 无身份验证 | | ✓** | ✓ ** |✓** 

*仅管道 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) 支持 MySQL。 <br>
**仅管道 [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) 支持 Databricks

### <a name="storage-guidance"></a>存储指导原则

建议为 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)创建数据存储。 标准和高级存储都可用于 Blob。 尽管高级存储开销更高，但它的吞吐量速度更快，可提高训练运行的速度，特别是在针对大型数据集进行训练时。 要了解存储帐户的成本，请参阅 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) 基于 Azure Blob 存储而构建，专为企业大数据分析设计。 Data Lake Storage Gen2 的一个基本部分是向 Blob 存储添加[分层命名空间](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 分层命名空间将对象/文件组织到目录层次结构中，以便进行有效的数据访问。

创建工作区时，会自动将 Azure Blob 容器和 Azure 文件共享注册到工作区。 它们分别命名为 `workspaceblobstore` 和 `workspacefilestore`。 `workspaceblobstore` 用于存储工作区项目和机器学习试验日志。 `workspacefilestore` 用于存储通过[计算实例](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)授权的笔记本和 R 脚本。 `workspaceblobstore` 容器被设置为默认数据存储。

> [!IMPORTANT]
> Azure 机器学习设计器（预览版）将在你打开设计器主页中的示例时，自动创建一个名为 azureml_globaldatasets 的数据存储。 此数据存储仅包含示例数据集。 请不要将此数据存储用于任何机密数据访问。
> ![为设计器示例数据集自动创建的数据存储](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>创建并注册数据存储

将 Azure 存储解决方案注册为数据存储时，会自动创建数据存储并将其注册到特定的工作区。 可使用 [Python SDK](#python-sdk) 或 [Azure 机器学习工作室](#azure-machine-learning-studio)创建数据存储并将其注册到工作区。

>[!IMPORTANT]
> 在最初的数据存储创建和注册过程中，Azure 机器学习会验证基础存储服务是否存在，并验证用户提供的主体（用户名、服务主体或 SAS 令牌）是否有权访问该存储。 不过，对于 Azure Data Lake Storage Gen 1 和 2 数据存储，会在稍后调用数据访问方法（如 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) 或 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)）时进行此验证。 
<br><br>
创建数据存储后，仅针对需要访问基础存储容器的方法执行此验证，而不是在每次检索数据存储对象时验证。 例如，如果要从数据存储中下载文件，则会进行验证；但如果只想更改默认数据存储，则不进行验证。

### <a name="python-sdk"></a>Python SDK

所有注册方法都位于 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 类，并具有窗体 `register_azure_*`。
> [!IMPORTANT]
> 如果你的存储帐户位于虚拟网络中，则仅支持 **通过 SDK** 创建数据存储。

可在 [Azure 门户](https://portal.azure.com)上找到填充 `register_azure_*()` 方法所需的信息。

* 如果计划使用帐户密钥或 SAS 令牌进行身份验证，请在左窗格中选择“存储帐户”，然后选择要注册的存储帐户。 
  * “概述”页面提供了帐户名称、容器和文件共享名称等信息。 
      1. 对于帐户密钥，请转到“设置”窗格中的“访问密钥” 。 
      1. 对于 SAS 令牌，请转到“设置”窗格中的“共享访问签名” 。

* 如果计划使用服务主体进行身份验证，请转到“应用注册”，然后选择要使用的应用。 
    * 其对应的“概述”页面将包含租户 ID 和客户端 ID 之类的必需信息。

以下示例演示如何将 Azure Blob 容器、Azure 文件共享和 Azure Data Lake Storage Gen2 作为数据存储进行注册。 这些示例中提供的参数是创建和注册数据存储所必需的。 

若要为其他存储服务创建数据存储，并查看这些方法的可选参数，请参阅[适用 `register_azure_*` 方法的参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)。

#### <a name="blob-container"></a>Blob 容器

若要注册 Azure Blob 容器作为数据存储，请使用 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

以下代码会创建 `blob_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户访问密钥访问 `my-account-name` 存储帐户上的 `my-container-name` Blob 容器。

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
如果 Blob 容器位于虚拟网络中，请在 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) 方法中包含参数 `skip_validation=True`。 

#### <a name="file-share"></a>文件共享

若要注册 Azure 文件共享作为数据存储，请使用 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

以下代码会创建 `file_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户访问密钥访问 `my-account-name` 存储帐户上的 `my-fileshare-name` 文件共享。

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
如果文件共享位于虚拟网络中，请在 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) 方法中包含参数 `skip_validation=True`。 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen2

对于 Azure Data Lake Storage Gen2 (ADLS Gen 2) 数据存储，请使用 [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 通过[服务主体权限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)注册连接到 Azure DataLake Gen 2 存储的凭据数据存储。 若要使用服务主体，需要[注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，并向服务主体授予“存储 Blob 数据读取者”访问权限。 详细了解 [ADLS Gen 2 中的访问控制设置](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

若要使用服务主体，需要[注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，并向服务主体授予适当的数据访问权限。 详细了解 [ADLS Gen 2 中的访问控制设置](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

以下代码会创建 `adlsgen2_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的服务主体凭据访问 `account_name` 存储帐户中的文件系统 `test`。

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

在 Azure 机器学习工作室中，通过几个步骤创建新的数据存储：

> [!IMPORTANT]
> 如果你的存储帐户位于虚拟网络中，则仅支持 [通过 SDK](#python-sdk) 创建数据存储。 

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com/)。
1. 在“管理”下的左窗格中，选择“数据存储” 。
1. 选择“+新建数据存储”。
1. 填写新数据存储的表单。 该表单会根据你选择的 Azure 存储类型和身份验证类型智能地进行更新。
  
可在 [Azure 门户](https://portal.azure.com)上找到填充表单所需的信息。 在左窗格中选择“存储帐户”，然后选择要注册的存储帐户。 “概述”页面提供了帐户名称、容器和文件共享名称等信息。 

* 对于身份验证项（如帐户密钥或 SAS 令牌），请转到“设置”窗格中的“访问密钥” 。 

* 对于租户 ID 和客户端 ID 等服务主体项，请转到“应用注册”，然后选择要使用的应用。 其对应的“概述”页面将包含这些项。 

下面的示例演示创建 Azure Blob 数据存储时表单的外观： 
    
![新数据存储的表单](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>获取工作区中的数据存储

若要获取在当前工作区中注册的特定数据存储，请使用 `Datastore` 类上的 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 静态方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要获取在给定工作区中注册的数据存储的列表，可对工作区对象使用 [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) 属性：

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

若要获取工作区的默认数据存储，请使用此行：

```Python
datastore = ws.get_default_datastore()
```
还可通过以下代码更改默认数据存储。 仅支持通过 SDK 使用此功能。 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>上传和下载数据

下例中所述的 [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) 和 [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 方法特定于 [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 和 [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 类并且操作相同。

> [!NOTE]
> 目前不支持上传到 AzureDataLakeGen2 数据存储。

### <a name="upload"></a>上载

使用 Python SDK 将目录或单个文件上传到数据存储：

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path` 参数指定文件共享（或 Blob 容器）中要上传的的位置。 默认为 `None`，因此数据上传到根目录。 如果为 `overwrite=True`，则将覆盖 `target_path` 上的任何现有数据。

还可通过 `upload_files()` 方法将单个文件的列表上传到数据存储。

### <a name="download"></a>下载

可将数据从数据存储下载到本地文件系统：

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 参数是要将数据下载到的本地目录位置。 若要在文件共享（或 blob 容器）中指定要下载到的文件夹路径，请提供 `prefix` 的路径。 如果 `prefix` 是 `None`，将下载文件共享（或 Blob 容器）的所有内容。

<a name="train"></a>

## <a name="access-your-data-during-training"></a>在训练期间访问数据

若要与数据存储中的数据进行交互，或将数据打包到用于机器学习任务（例如训练）的易耗型对象，请[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。 数据集提供了将表格数据加载到 pandas 或 Spark DataFrame 的函数。 通过数据集，还可从 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 下载或装载任何格式的文件。 [详细了解如何使用数据集进行训练](how-to-train-with-datasets.md)。

### <a name="accessing-source-code-during-training"></a>在训练期间访问源代码

与 Azure 文件共享相比，Azure Blob 存储具有更高的吞吐量速度，并将扩展到大量并行启动的作业。 出于此原因，我们建议配置运行以使用 Blob 存储来传输源代码文件。

下面的代码示例在运行配置中指定用于源代码传输的 Blob 数据存储。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在评分过程中访问数据

Azure 机器学习提供多种方法来使用模型进行评分。 其中一些方法不提供对数据存储的访问权限。 使用下表来了解允许在评分期间访问数据存储的方法：

| 方法 | 数据存储访问 | 说明 |
| ----- | :-----: | ----- |
| [成批预测](how-to-use-parallel-run-step.md) | ✔ | 以异步方式对大量数据进行预测。 |
| [Web 服务](how-to-deploy-and-where.md) | &nbsp; | 将模型部署为 Web 服务。 |
| [Azure IoT Edge 模块](how-to-deploy-and-where.md) | &nbsp; | 将模块部署到 IoT Edge 设备。 |

如果 SDK 无法提供对数据存储的访问权限，可使用相关的 Azure SDK 创建自定义代码来访问数据。 例如，[适用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python) 是可用于访问 Blob 或文件中存储的数据的客户端库。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>将数据移到支持的 Azure 存储解决方案

Azure 机器学习支持从 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 访问数据。 如果你正在使用不受支持的存储，则建议使用 [Azure 数据工厂和这些步骤](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)将数据移动到受支持的 Azure 存储解决方案。 将数据移动到受支持的存储可帮助你在机器学习试验期间节省数据传出成本。 

Azure 数据工厂提供高效且可复原的数据传输，其中包含 80 多个预生成的连接器，无需额外付费。 这些连接器包括 Azure 数据服务、本地数据源、Amazon S3 和 Redshift，以及 Google BigQuery。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [训练模型](how-to-train-ml-models.md)
* [部署模型](how-to-deploy-and-where.md)
