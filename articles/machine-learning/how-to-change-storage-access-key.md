---
title: 更改存储帐户访问密钥
titleSuffix: Azure Machine Learning
description: 了解如何更改工作区使用的 Azure 存储帐户的访问密钥。 Azure 机器学习使用 Azure 存储帐户来存储数据和模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296939"
---
# <a name="regenerate-storage-account-access-keys"></a>重新生成存储帐户访问密钥
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何更改 Azure 机器学习使用的 Azure 存储帐户的访问密钥。 Azure 机器学习可以使用存储帐户来存储数据或训练后的模型。

出于安全考虑，你可能需要更改 Azure 存储帐户的访问密钥。 重新生成访问密钥时，必须更新 Azure 机器学习以使用新密钥。 Azure 机器学习可以将存储帐户同时用作模型存储和数据存储。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建工作区](how-to-manage-workspace.md)一文。

* [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [Azure 机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)。

> [!NOTE]
> 本文档中的代码段使用 Python SDK 的版本 1.0.83 进行了测试。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>需要更新的内容

存储帐户可由 Azure 机器学习工作区使用（用来存储日志、模型、快照等），也可用作数据存储。 更新工作区的过程是单个 Azure CLI 命令，可在更新存储密钥后运行。 更新数据存储的过程更复杂，需要查明当前有哪些数据存储正在使用该存储帐户，然后重新注册它们。

> [!IMPORTANT]
> 使用 Azure CLI 更新工作区，使用 Python 更新数据存储，两项工作同时进行。 仅更新其中一项是不够的，并且可能会导致错误，直至两项全部更新。

若要查明数据存储使用的存储帐户，请使用以下代码：

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

此代码查找使用 Azure 存储的任何已注册数据存储，并列出以下信息：

* 数据存储名称：存储帐户注册下的数据存储的名称。
* 存储帐户名称：Azure 存储帐户的名称。
* 容器：此注册使用的存储帐户中的容器。

它还指明了数据存储是用于 Azure Blob 还是 Azure 文件共享，因为需要采用不同的方法来重新注册每种类型的数据存储。

如果你计划为其重新生成访问密钥的存储帐户存在条目，请保存数据存储名称、存储帐户名称和容器名称。

## <a name="update-the-access-key"></a>更新访问密钥

若要更新 Azure 机器学习以使用新密钥，请执行以下步骤：

> [!IMPORTANT]
> 执行所有步骤，使用 CLI 更新工作区，使用 Python 更新数据存储。 仅更新其中一项可能会导致错误，直至两项全部更新。

1. 重新生成密钥。 有关重新生成访问密钥的信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)。 保存新密钥。

1. 若要更新工作区以使用新密钥，请执行以下步骤：

    1. 使用以下 Azure CLI 命令登录到包含你的工作区的 Azure 订阅：

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. 使用以下命令更新工作区以使用新密钥。 将 `myworkspace` 替换为你的 Azure 机器学习工作区名称，并将 `myresourcegroup` 替换为包含该工作区的 Azure 资源组的名称。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        此命令自动为工作区使用的 Azure 存储帐户同步新密钥。

1. 若要重新注册使用存储帐户的数据存储，请使用以下代码并使用[需要更新的内容](#whattoupdate)部分中的值以及步骤 1 中的密钥：

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    因为指定了 `overwrite=True`，所以此代码将覆盖现有注册，并对其进行更新以使用新密钥。

## <a name="next-steps"></a>后续步骤

有关注册数据存储的详细信息，请参阅[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)类引用。
