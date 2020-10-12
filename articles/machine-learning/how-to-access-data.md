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
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: e118c22829bc55e8baafb87db20817ab043ef4df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540772"
---
# <a name="connect-to-azure-storage-services"></a>连接到 Azure 存储服务

本文介绍如何通过 Azure 机器学习数据存储连接到 Azure 存储服务。 数据存储可安全地连接到 Azure 存储服务，而不会损害你的身份验证凭据以及原始数据源的完整性。 它们会存储连接信息，例如与工作区关联的 [Key Vault](https://azure.microsoft.com/services/key-vault/) 中的订阅 ID 和令牌授权，让你能够安全地访问存储，而无需在脚本中对其进行硬编码。 可以使用 [Azure 机器学习 Python SDK](#python) 或 [Azure 机器学习工作室](how-to-connect-data-ui.md)来创建和注册数据存储。

如果希望使用 Azure 机器学习 VS Code 扩展来创建和管理数据存储，请访问 [VS Code 资源管理操作指南](how-to-manage-resources-vscode.md#datastores)以了解详细信息。

可从[这些 Azure 存储解决方案](#matrix)创建数据存储。 对于不支持的存储解决方案，为了在 ML 试验期间节省数据出口成本，请[将数据移到](#move)支持的 Azure 存储解决方案。  

若要了解在 Azure 机器学习总体数据访问工作流中的哪些位置使用数据存储，请参阅[安全地访问数据](concept-data.md#data-workflow)一文。

## <a name="prerequisites"></a>先决条件

需要：
- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

- 一个使用[支持的存储类型](#matrix)的 Azure 存储帐户。

- [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)或通过 Python SDK 使用现有工作区。 

    导入 `Workspace` 和 `Datastore` 类，并使用函数 `from_config()` 从文件 `config.json` 中加载订阅信息。 默认情况下，这会查找当前目录中的 JSON 文件，但你也可以使用 `from_config(path="your/file/path")` 指定一个路径参数，使之指向该文件。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    创建工作区时，会将 Azure Blob 容器和 Azure 文件共享作为数据存储自动注册到工作区。 它们分别命名为 `workspaceblobstore` 和 `workspacefilestore`。 `workspaceblobstore` 用于存储工作区项目和机器学习试验日志。 它也已设为**默认数据存储**，无法从工作区中删除。 `workspacefilestore` 用于存储通过[计算实例](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)授权的笔记本和 R 脚本。
    
    > [!NOTE]
    > 当你在设计器主页中打开一个示例时，Azure 机器学习设计器将自动创建一个名为 **azureml_globaldatasets** 的数据存储。 此数据存储仅包含示例数据集。 请不要将此数据存储用于任何机密数据访问。

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支持的数据存储服务类型

数据存储目前支持将连接信息存储到下表中列出的存储服务。

| 存储类型 | 身份验证类型 | [Azure 机器学习工作室](https://ml.azure.com/) | [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) |  [Azure 机器学习 CLI](reference-azure-machine-learning-cli.md) | [Azure 机器学习 Rest API](https://docs.microsoft.com/rest/api/azureml/) | VS Code
---|---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓ |✓
[Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| 帐户密钥 <br> SAS 令牌 | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| 服务主体| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 服务主体| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL&nbsp;数据库](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 身份验证 <br>服务主体| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL 身份验证| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Database&nbsp;for&nbsp;MySQL](https://docs.microsoft.com/azure/mysql/overview) | SQL 身份验证|  | ✓* | ✓* |✓*|
[Databricks&nbsp;文件系统](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| 无身份验证 | | ✓** | ✓ ** |✓** |

\* MySQL 仅支持管道 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true)<br />
\*\* Databricks 仅支持管道 [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py&preserve-view=true)


### <a name="storage-guidance"></a>存储指导原则

建议为 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)创建数据存储。 标准和高级存储都可用于 Blob。 尽管高级存储费用更高，但其吞吐速度也更快，可加速训练运行，特别是在针对大型数据集进行训练时。 要了解存储帐户的成本，请参阅 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) 基于 Azure Blob 存储而构建，专为企业大数据分析设计。 Data Lake Storage Gen2 的一个基本部分是向 Blob 存储添加[分层命名空间](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 分层命名空间将对象/文件组织到目录层次结构中，以便进行有效的数据访问。

## <a name="storage-access-and-permissions"></a>存储访问和权限

若要确保安全连接到 Azure 存储服务，Azure 机器学习要求你有权访问相应的数据存储容器。 此访问权限依赖用于注册数据存储的身份验证凭据。 

### <a name="virtual-network"></a>虚拟网络 

如果你的数据存储帐户在虚拟网络中，则需要执行其他配置步骤来确保 Azure 机器学习能够访问你的数据。 请参阅 [在 Azure 虚拟网络中使用 Azure 机器学习 studio](how-to-enable-studio-virtual-network.md) ，以确保在创建和注册数据存储时应用适当的配置步骤。  

### <a name="access-validation"></a>访问验证

在**初始数据存储创建和注册过程**中，Azure 机器学习会自动验证基础存储服务是否存在，用户提供的主体 (用户名、服务主体或 SAS 令牌) 是否有权访问指定的存储。

创建数据存储后，此验证只针对要求访问基础存储容器的方法执行，而不是每次检索数据存储对象时都执行 。 例如，如果要从数据存储中下载文件，则会进行验证，但如果只想更改默认数据存储，则不会进行验证。

若要在访问基础存储服务时进行身份验证，可在要创建的数据存储类型的相应 `register_azure_*()` 方法中提供帐户密钥、共享访问签名 (SAS) 令牌或服务主体。 [存储类型矩阵](#matrix)列出了与各种数据存储类型对应的受支持的身份验证类型。

可在 [Azure 门户](https://portal.azure.com)上查找帐户密钥、SAS 令牌和服务主体信息。

* 如果计划使用帐户密钥或 SAS 令牌进行身份验证，请在左窗格中选择“存储帐户”，然后选择要注册的存储帐户。 
  * “概述”页面提供了帐户名称、容器和文件共享名称等信息。 
      1. 对于帐户密钥，请转到“设置”窗格中的“访问密钥” 。 
      1. 对于 SAS 令牌，请转到“设置”窗格中的“共享访问签名” 。

* 如果计划使用服务主体进行身份验证，请转到“应用注册”，然后选择要使用的应用。 
    * 其对应的“概览”页面将包含租户 ID 和客户端 ID 之类的必需信息。

> [!IMPORTANT]
> 出于安全原因，你可能需要更改 Azure 存储帐户的访问密钥（帐户密钥或 SAS 令牌）。 执行此操作时，请确保将新凭据与工作区及其连接的数据存储进行同步。 了解如何 [同步已更新的凭据](how-to-change-storage-access-key.md)。 

### <a name="permissions"></a>权限

对于 Azure blob 容器和 Azure Data Lake 第2代存储，请确保身份验证凭据具有 **存储 Blob 数据读取器** 访问权限。 详细了解[存储 Blob 数据读取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)。 帐户 SAS 令牌默认为 "无权限"。 对于数据读取访问，你的身份验证凭据必须具有容器和对象的最小列表和读取权限。 对于数据写入访问权限，还需要编写和添加权限。

<a name="python"></a>

## <a name="create-and-register-datastores"></a>创建并注册数据存储

将 Azure 存储解决方案注册为数据存储时，会自动创建数据存储并将其注册到特定的工作区。 查看 [存储访问 & 权限](#storage-access-and-permissions) 部分，了解有关虚拟网络方案的指导，以及在哪里可以找到所需的身份验证凭据。 

本部分中的示例演示了如何通过 Python SDK 为以下存储类型创建和注册数据存储。 这些示例中提供的参数是创建和注册数据存储所必需的。

* [Azure Blob 容器](#azure-blob-container)
* [Azure 文件共享](#azure-file-share)
* [Azure Data Lake Storage Gen2](#azure-data-lake-storage-generation-2)

 若要为其他受支持的存储服务创建数据存储，请参阅[适用的 `register_azure_*` 方法的参考文档](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=truemethods)。

如果你更喜欢使用低代码体验，请参阅 [使用 Azure 机器学习 Studio 连接到数据](how-to-connect-data-ui.md)。

> [!NOTE]
> 数据存储名称应仅包含小写字母、数字和下划线。 

### <a name="azure-blob-container"></a>Azure blob 容器

若要将 Azure blob 容器注册为数据存储，请使用 [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

以下代码会创建 `blob_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户访问密钥访问 `my-account-name` 存储帐户上的 `my-container-name` Blob 容器。 查看 [存储访问 & 权限](#storage-access-and-permissions) 部分，了解有关虚拟网络方案的指导，以及在哪里可以找到所需的身份验证凭据。 

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

### <a name="azure-file-share"></a>Azure 文件共享

若要将 Azure 文件共享注册为数据存储，请使用 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

以下代码会创建 `file_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的帐户访问密钥访问 `my-account-name` 存储帐户上的 `my-fileshare-name` 文件共享。 查看 [存储访问 & 权限](#storage-access-and-permissions) 部分，了解有关虚拟网络方案的指导，以及在哪里可以找到所需的身份验证凭据。 

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen2

对于 Azure Data Lake Storage Gen2 (ADLS Gen 2) 数据存储，请使用 [register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 通过[服务主体权限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)注册连接到 Azure DataLake Gen 2 存储的凭据数据存储。  

若要使用服务主体，需要[注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，并向服务主体授予“存储 Blob 数据读取者”访问权限。 详细了解 [ADLS Gen2 的访问控制设置](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

以下代码会创建 `adlsgen2_datastore_name` 数据存储并将其注册到 `ws` 工作区。 此数据存储使用提供的服务主体凭据访问 `account_name` 存储帐户中的文件系统 `test`。 查看 [存储访问 & 权限](#storage-access-and-permissions) 部分，了解有关虚拟网络方案的指导，以及在哪里可以找到所需的身份验证凭据。 

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

<a name="arm"></a>

## <a name="create-datastores-using-azure-resource-manager"></a>使用 Azure 资源管理器创建数据存储

中有多个 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) 可用于创建数据存储的模板。

有关使用这些模板的信息，请参阅 [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>使用数据存储中的数据

创建数据存储后，请[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)，以便与数据进行交互。 数据集可将数据打包成一个延迟计算的可供机器学习任务（例如训练）使用的对象。 它们还提供从 Azure 存储服务（例如 Azure Blob 存储和 ADLS Gen2）[下载或装载](how-to-train-with-datasets.md#mount-vs-download)任何格式的文件的功能。 你还可以使用它们将表格数据加载到 pandas 或 Spark 数据帧中。

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>从工作区获取数据存储

若要获取在当前工作区中注册的特定数据存储，请在 `Datastore` 类上使用 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-workspace--datastore-name-) 静态方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要获取在给定工作区中注册的数据存储的列表，可对工作区对象使用 [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedatastores) 属性：

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

## <a name="access-data-during-scoring"></a>在评分过程中访问数据

Azure 机器学习提供多种方法来使用模型进行评分。 其中一些方法不提供对数据存储的访问权限。 使用下表了解允许在评分期间访问数据存储的方法：

| 方法 | 数据存储访问 | 说明 |
| ----- | :-----: | ----- |
| [批量预测](how-to-use-parallel-run-step.md) | ✔ | 以异步方式对大量数据进行预测。 |
| [Web 服务](how-to-deploy-and-where.md) | &nbsp; | 将模型部署为 Web 服务。 |
| [Azure IoT Edge 模块](how-to-deploy-and-where.md) | &nbsp; | 将模型部署到 IoT Edge 设备。 |

对于 SDK 不提供对数据存储的访问权限的情况，也许可以通过使用相关 Azure SDK 访问数据以创建自定义代码。 例如，[适用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python) 是可用于访问 Blob 或文件中存储的数据的客户端库。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>将数据移到支持的 Azure 存储解决方案

Azure 机器学习支持从 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 访问数据。 如果你正在使用不受支持的存储，则建议使用 [Azure 数据工厂和这些步骤](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)将数据移动到受支持的 Azure 存储解决方案。 将数据移动到受支持的存储可帮助你在机器学习试验期间节省数据传出成本。 

Azure 数据工厂具有超过 80 个预生成的连接器，可提供高效且可复原的数据传输，无需额外付费。 这些连接器包括 Azure 数据服务、本地数据源、Amazon S3 和 Redshift，以及 Google BigQuery。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [定型模型](how-to-set-up-training-targets.md)
* [部署模型](how-to-deploy-and-where.md)
