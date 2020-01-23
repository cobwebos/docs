---
title: 在 Azure Cosmos DB 中使用内置笔记本命令和功能（预览版）
description: 了解如何使用内置命令和功能来执行使用 Azure Cosmos DB 的内置笔记本的常见操作。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: dech
ms.openlocfilehash: 61d46bbf0ccdeb5cd2e95e36e19f1aa81cfeeb48
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513393"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-preview"></a>在 Azure Cosmos DB 中使用内置笔记本命令和功能（预览版）

Azure Cosmos DB 中的内置 Jupyter 笔记本使你可以从 Azure 门户分析和可视化数据。 本文介绍如何使用内置笔记本命令和功能执行常见操作。

## <a name="install-a-new-package"></a>安装新包
为 Azure Cosmos 帐户启用笔记本支持后，可以打开新的笔记本并安装包。

在新的代码单元中，插入并运行以下代码，将 ``PackageToBeInstalled`` 替换为所需的 Python 包。
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
此包将可从 Azure Cosmos 帐户工作区中的任何笔记本使用。 

> [!TIP]
> 如果笔记本需要自定义包，则建议你在笔记本中添加一个单元格来安装包，因为如果[重置工作区](#reset-notebooks-workspace)，则会删除包。  

## <a name="run-a-sql-query"></a>运行 SQL 查询

可以使用 ``%%sql`` 幻命令针对帐户中的任何容器运行[SQL 查询](sql-query-getting-started.md)。 使用语法：

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- 将 ``{database_id}`` 和 ``{container_id}`` 替换为你的 Cosmos 帐户中的数据库和容器的名称。 如果未提供 ``--database`` 和 ``--container`` 参数，则将在[默认数据库和容器](#set-default-database-for-queries)上执行查询。
- 您可以运行 Azure Cosmos DB 中有效的任何 SQL 查询。 查询文本必须在新行上。

例如： 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
在单元格中运行 ```%%sql?```，查看笔记本中的 sql 魔术命令的帮助文档。

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>运行 SQL 查询并输出到 Pandas 数据帧

可以将 ``%%sql`` 查询的结果输出到[Pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)中。 使用语法： 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- 将 ``{database_id}`` 和 ``{container_id}`` 替换为你的 Cosmos 帐户中的数据库和容器的名称。 如果未提供 ``--database`` 和 ``--container`` 参数，则将在[默认数据库和容器](#set-default-database-for-queries)上执行查询。
- 将 ``{outputDataFrameVar}`` 替换为将包含结果的数据帧变量的名称。
- 您可以运行 Azure Cosmos DB 中有效的任何 SQL 查询。 查询文本必须在新行上。 

例如：

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```
## <a name="upload-json-items-to-a-container"></a>将 JSON 项上载到容器
可以使用 ``%%upload`` 幻命令将数据从 JSON 文件上传到指定的 Azure Cosmos 容器。 使用以下命令上传项目：

```bash
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- 将 ``{database_id}`` 和 ``{container_id}`` 替换为 Azure Cosmos 帐户中的数据库和容器的名称。 如果未提供 ``--database`` 和 ``--container`` 参数，则将在[默认数据库和容器](#set-default-database-for-queries)上执行查询。
- 将 ``{url_location_of_file}`` 替换为 JSON 文件的位置。 该文件必须是有效 JSON 对象的数组，并且应可通过公共 Internet 访问。

例如：

```bash
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```bash
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
使用输出统计信息，可以计算用于上传项的有效 RU/秒。 例如，如果在38秒内消耗了 25000 RU，则有效的 RU/s 为 25000 RU/38 秒 = 658 RU/秒。

## <a name="set-default-database-for-queries"></a>设置查询的默认数据库
你可以设置默认的数据库 ```%%sql``` 命令将用于笔记本。 将 ```{database_id}``` 替换为数据库的名称。

```bash
%database {database_id}
```
在单元格中运行 ```%database?``` 以查看笔记本中的文档。

## <a name="set-default-container-for-queries"></a>设置查询的默认容器
你可以设置默认容器 ```%%sql``` 命令将用于笔记本。 将 ```{container_id}``` 替换为你的容器的名称。

```bash
%container {container_id}
```
在单元格中运行 ```%container?``` 以查看笔记本中的文档。

## <a name="use-built-in-nteract-data-explorer"></a>使用内置的 nteract 数据资源管理器
您可以使用内置的[nteract 数据资源管理](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)器来筛选和可视化数据帧。 若要启用此功能，请将选项 ``pd.options.display.html.table_schema`` 设置为 ``True`` 并 ``pd.options.display.max_rows`` 到所需的值（可以将 ``pd.options.display.max_rows`` 设置为 ``None`` 以显示所有结果）。

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
![nteract 数据资源管理器](media/use-notebook-features-and-commands/nteract-built-in-chart.png)

## <a name="use-the-built-in-python-sdk"></a>使用内置的 Python SDK
安装了适用于[SQL API Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)的版本4，并将其包含在 Azure Cosmos 帐户的笔记本环境中。

使用内置 ``cosmos_client`` 实例运行任何 SDK 操作。 

例如：

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
请参阅[PYTHON SDK 示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)。 

> [!IMPORTANT]
> 内置 Python SDK 仅支持 SQL （核心） API 帐户。 对于其他 Api，你将需要安装对应于 API 的[相关 Python 驱动程序](#install-a-new-package)。 

## <a name="create-a-custom-instance-of-cosmos_client"></a>创建的自定义实例 ``cosmos_client``
为了获得更大的灵活性，你可以创建 ``cosmos_client`` 的自定义实例，以便执行以下操作：

- 自定义[连接策略](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- 针对不同于你所在的 Azure Cosmos 帐户运行操作

可以通过[环境变量](#access-the-account-endpoint-and-primary-key-env-variables)访问当前帐户的连接字符串和主键。 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=os.environ["COSMOS_ENDPOINT"], credential=os.environ["COSMOS_KEY"], connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>访问帐户终结点和主密钥环境变量
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` 和 ``COSMOS_KEY`` 环境变量仅适用于 SQL API。 对于其他 Api，请在 Azure Cosmos 帐户的 "**连接字符串**" 或 "**密钥**" 边栏选项卡中找到终结点和密钥。  

## <a name="reset-notebooks-workspace"></a>重置笔记本工作区
若要将 "笔记本" 工作区重置为默认设置，请在命令栏上选择 "**重置工作区**"。 这将删除任何自定义的已安装包，并重新启动 Jupyter 服务器。 你的笔记本、文件和 Azure Cosmos 资源将不会受到影响。  

![重置笔记本工作区](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>后续步骤

- 了解[Azure Cosmos DB Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)的优点
- 了解[Azure Cosmos DB 适用于 SQL API 的 PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
