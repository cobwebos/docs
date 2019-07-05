---
title: 更改存储帐户访问密钥
titleSuffix: Azure Machine Learning service
description: 了解如何更改你的工作区使用的 Azure 存储帐户的访问密钥。 Azure 机器学习服务使用 Azure 存储帐户来存储数据和模型。 当你重新生成存储帐户的访问密钥时，必须更新 Azure 机器学习服务以使用新密钥。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543828"
---
# <a name="regenerate-storage-account-access-keys"></a>重新生成存储帐户访问密钥

了解如何更改使用的 Azure 机器学习服务的 Azure 存储帐户的访问密钥。 Azure 机器学习可以使用存储帐户来存储数据或训练的模型。

出于安全考虑，可能需要更改 Azure 存储帐户的访问密钥。 当你重新生成访问密钥时，必须更新 Azure 机器学习以使用新的密钥。 Azure 机器学习可能正在使用的存储帐户，对于这两个模型存储，并作为一种数据存储。

## <a name="prerequisites"></a>必备组件

* Azure 机器学习服务工作区。 有关详细信息，请参阅[创建工作区](setup-create-workspace.md)一文。

* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [Azure 机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>需要更新

通过 Azure 机器学习服务工作区 （存储日志、 模型、 快照，等等） 和作为一种数据存储，可以使用存储帐户。 更新工作区的过程是单个 Azure CLI 命令，并更新存储密钥后，即可运行。 更新数据存储的过程更为复杂，需要发现哪些数据存储当前正在使用的存储帐户，并重新注册它们。

> [!IMPORTANT]
> 更新使用 Azure CLI，并使用 Python，同时在数据存储在工作区。 更新只有一个或另一个是不够的并同时更新之前可能会导致错误。

若要发现你的数据存储使用的存储帐户，请使用以下代码：

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

此代码会查找使用 Azure 存储任何已注册数据存储，并列出了以下信息：

* 数据存储名称：注册存储帐户时的数据存储的名称。
* 存储帐户名称：Azure 存储帐户的名称。
* 容器：此注册使用的存储帐户中的容器。

如果要在重新生成访问密钥的存储帐户存在的条目，将保存的数据存储名称、 存储帐户名称和容器名称。

## <a name="update-the-access-key"></a>更新访问密钥

若要更新 Azure 机器学习服务以使用新密钥，请使用以下步骤：

> [!IMPORTANT]
> 执行所有步骤，使用 CLI，并使用 Python 的数据存储在工作区对二者进行更新。 同时更新之前，只有一个或其他更新可能会导致错误。

1. 重新生成密钥。 正在重新生成访问密钥的信息，请参阅[管理存储帐户](/azure/storage/common/storage-account-manage#access-keys)一文。 保存新密钥。

1. 若要更新工作区以使用新密钥，请使用以下步骤：

    1. 若要登录到你的工作区包含通过使用以下 Azure CLI 命令的 Azure 订阅：

        ```azurecli-interactive
        az login
        ```

    1. 若要安装 Azure 机器学习扩展，请使用以下命令：

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. 若要更新工作区以使用新密钥，请使用以下命令。 替换`myworkspace`与 Azure 机器学习工作区名称，并替换`myresourcegroup`包含的工作区的 Azure 资源组的名称。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        此命令会自动同步由工作区使用的 Azure 存储帐户的新密钥。

1. 若要重新注册使用的存储帐户的数据存储，使用中的值[什么需要更新](#whattoupdate)部分，并使用以下代码的步骤 1 中的密钥：

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    由于`overwrite=True`指定，此代码将覆盖现有的注册并更新它以使用新密钥。

## <a name="next-steps"></a>后续步骤

注册数据存储的详细信息，请参阅[ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)类参考。
