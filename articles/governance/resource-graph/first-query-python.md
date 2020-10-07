---
title: 快速入门：你的第一个 Python 查询
description: 本快速入门介绍为 Python 启用 Resource Graph 库并运行第一个查询的步骤。
ms.date: 07/15/2020
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: a92eda343e8c0c38b9c9ebebb16d5c102d1b3f19
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87873664"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>快速入门：使用 Python 运行你的第一个 Resource Graph 查询

使用 Azure Resource Graph 的第一步是确保为 Python 安装了所需的库。 本快速入门将指导你完成将该库添加到 Python 安装的过程。

在此过程结束时，你已将该库添加到 Python 安装中，并将运行你的第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>添加 Resource Graph 库

若要使 Python 能够查询 Azure Resource Graph，则必须添加该库。 此库适用于可使用 Python 的任何情况，包括 [Windows 10 上的 bash](/windows/wsl/install-win10) 或本地安装的 bash。

1. 请确保安装最新的 Python（至少为 3.8）。 如果尚未安装，请在 [Python.org](https://www.python.org/downloads/) 下载。

1. 请确保安装最新的 Azure CLI（至少为 2.5.1）。 如果尚未安装，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

   > [!NOTE]
   > 在下面的示例中需要使用 Azure CLI，这样 Python 才能使用基于 CLI 的身份验证。 有关其他选项的信息，请参阅[使用适用于 Python 的 Azure 管理库进行身份验证](/azure/developer/python/azure-sdk-authenticate)。

1. 通过 Azure CLI 进行身份验证。

   ```azurecli
   az login
   ```

1. 在所选的 Python 环境中，安装 Azure Resource Graph 所需的库：

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > 如果已为所有用户安装了 Python，则必须从提升的控制台运行这些命令。

1. 验证是否已安装这些库。 `azure-mgmt-resourcegraph` 应为 2.0.0 或更高版本，`azure-mgmt-resource` 应为 9.0.0 版或更高版本，`azure-cli-core` 应为 2.5.0 或更高版本  。

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

将 Python 库添加到所选环境中后，即可尝试一个简单的 Resource Graph 查询。 该查询返回前五个 Azure 资源，以及每个资源的名称和资源类型 。

1. 使用已安装的库和 `resources` 方法运行第一个 Azure Resource Graph 查询：

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 更新对 `getresources` 的调用，并将查询更改为 `order by` Name 属性：

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 更新对 `getresources` 的调用并将查询更改为先 `order by` Name 属性，然后对前五个结果应用 `limit`：

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

## <a name="clean-up-resources"></a>清理资源

如果希望从 Python 环境中删除已安装的库，可使用以下命令执行此操作：

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将 Resource Graph 库添加到 Python 环境并运行第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)
