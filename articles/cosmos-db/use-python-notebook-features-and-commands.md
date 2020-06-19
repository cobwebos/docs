---
title: 使用 Azure Cosmos DB Python 笔记本中的内置笔记本命令和功能（预览版）
description: 了解如何使用 Azure Cosmos DB 内置 Python 笔记本中的内置命令和功能来执行常见操作。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: a16b95249562db98d9382f8ca56bf4a27beba2fd
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743492"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>使用 Azure Cosmos DB Python 笔记本中的内置笔记本命令和功能（预览版）

使用 Azure Cosmos DB 中的内置 Jupyter 笔记本，可以从 Azure 门户分析和可视化数据。 本文介绍如何在 Python 笔记本中使用内置笔记本命令和功能执行常见操作。

## <a name="install-a-new-package"></a>安装新包
为 Azure Cosmos 帐户启用笔记本支持后，可以打开新的笔记本并安装一个包。

在新的代码单元中，插入并运行以下代码（请将 ``PackageToBeInstalled`` 替换为所需的 Python 包）。
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
可以从 Azure Cosmos 帐户工作区中的任何笔记本使用此包。 

> [!TIP]
> 如果你的笔记本需要某个自定义包，我们建议在笔记本中添加一个单元来安装该包，因为在[重置工作区](#reset-notebooks-workspace)后会删除包。  

## <a name="run-a-sql-query"></a>运行 SQL 查询

可以使用 ``%%sql`` magic 命令针对帐户中的任何容器运行 [SQL 查询](sql-query-getting-started.md)。 使用语法：

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- 请将 ``{database_id}`` 和 ``{container_id}`` 替换为你的 Cosmos 帐户中的数据库和容器名称。 如果未提供 ``--database`` 和 ``--container`` 参数，则会对[默认的数据库和容器](#set-default-database-for-queries)执行查询。
- 可以运行在 Azure Cosmos DB 中有效的任何 SQL 查询。 查询文本必须在新行中输入。

例如： 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
在单元中运行 ```%%sql?``` 可在笔记本中查看 sql magic 命令的帮助文档。

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>运行 SQL 查询并输出到 Pandas 数据帧

可将 ``%%sql`` 查询的结果输出到 [Pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)。 使用语法： 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- 请将 ``{database_id}`` 和 ``{container_id}`` 替换为你的 Cosmos 帐户中的数据库和容器名称。 如果未提供 ``--database`` 和 ``--container`` 参数，则会对[默认的数据库和容器](#set-default-database-for-queries)执行查询。
- 请将 ``{outputDataFrameVar}`` 替换为将包含结果的数据帧变量的名称。
- 可以运行在 Azure Cosmos DB 中有效的任何 SQL 查询。 查询文本必须在新行中输入。 

例如：

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>设置查询的默认数据库
可以设置 ```%%sql``` 命令要对笔记本使用的默认数据库。 将 ```{database_id}``` 替换为数据库的名称。

```python
%database {database_id}
```
在单元中运行 ```%database?``` 可在笔记本中查看文档。

## <a name="set-default-container-for-queries"></a>设置查询的默认容器
可以设置 ```%%sql``` 命令要对笔记本使用的默认容器。 请将 ```{container_id}``` 替换为你的容器的名称。

```python
%container {container_id}
```
在单元中运行 ```%container?``` 可在笔记本中查看文档。

## <a name="upload-json-items-to-a-container"></a>将 JSON 项上传到容器
可以使用 ``%%upload`` magic 命令将 JSON 文件中的数据上传到指定的 Azure Cosmos 容器。 运行以下命令来上传项：

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- 请将 ``{database_id}`` 和 ``{container_id}`` 替换为你的 Azure Cosmos 帐户中的数据库和容器名称。 如果未提供 ``--database`` 和 ``--container`` 参数，则会对[默认的数据库和容器](#set-default-database-for-queries)执行查询。
- 请将 ``{url_location_of_file}`` 替换为你的 JSON 文件的位置。 该文件必须是有效 JSON 对象的数组，并且应可通过公共 Internet 访问它。

例如：

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
可以使用输出统计信息计算用于上传项的有效 RU/秒。 例如，如果在 38 秒内消耗了 25,000 个 RU，则有效 RU/秒为 25,000 RU / 38 秒 = 658 RU/秒。

## <a name="run-another-notebook-in-current-notebook"></a>在当前笔记本中运行另一个笔记本 
可以使用 ``%%run`` magic 命令从当前笔记本运行工作区中的另一个笔记本。 使用语法：

```python
%%run ./path/to/{notebookName}.ipynb
```
请将 ``{notebookName}`` 替换为要运行的笔记本的名称。 该笔记本必须位于当前的“我的笔记本”工作区中。 

## <a name="use-built-in-nteract-data-explorer"></a>使用内置的 nteract 数据资源管理器
可以使用内置的 [nteract 数据资源管理器](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)来筛选和可视化数据帧。 若要启用此功能，请将选项 ``pd.options.display.html.table_schema`` 设置为 ``True``，并将 ``pd.options.display.max_rows`` 设置为所需的值（可将 ``pd.options.display.max_rows`` 设置为 ``None`` 以显示所有结果）。

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract 数据资源管理器](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>使用内置的 Python SDK
已安装 [Azure Cosmos DB Python SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) 版本 4，并且它已包含在 Azure Cosmos 帐户的笔记本环境中。

使用内置的 ``cosmos_client`` 实例可运行任何 SDK 操作。 

例如：

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
参阅 [Python SDK 示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)。 

> [!IMPORTANT]
> 内置 Python SDK 仅支持 SQL (Core) API 帐户。 对于其他 API，需要[安装对应于 API 的相关 Python 驱动程序](#install-a-new-package)。 

## <a name="create-a-custom-instance-of-cosmos_client"></a>创建 ``cosmos_client`` 的自定义实例
为了提高灵活性，可以创建 ``cosmos_client`` 的自定义实例，以便：

- 自定义[连接策略](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- 针对其他 Azure Cosmos 帐户（而不是你所在的帐户）运行操作

可以通过[环境变量](#access-the-account-endpoint-and-primary-key-env-variables)访问当前帐户的连接字符串和主密钥。 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>通过环境变量访问帐户终结点和主密钥
可以访问你的笔记本所在的帐户的内置终结点和密钥。

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> ``COSMOS.ENDPOINT`` 和 ``COSMOS.KEY`` 变量仅适用于 SQL API。 对于其他 API，可在 Azure Cosmos 帐户的“连接字符串”或“密钥”边栏选项卡中找到该终结点和密钥。   

## <a name="reset-notebooks-workspace"></a>重置笔记本工作区
若要将笔记本工作区重置为默认设置，请在命令栏上选择“重置工作区”。 这会删除所有自定义的已安装包，并重启 Jupyter 服务器。 笔记本、文件和 Azure Cosmos 资源不受影响。  

![重置笔记本工作区](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Cosmos DB Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)的优势
- 了解 [Azure Cosmos DB Python SDK for SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
