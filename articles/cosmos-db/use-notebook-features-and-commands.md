---
title: 使用中的内置笔记本命令和功能 Azure Cosmos DB
description: 了解如何使用内置命令和功能来执行使用 Azure Cosmos DB 的内置笔记本的常见操作。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 1eda8271a3b8aa2c9e247252bd755279d23b6e10
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310342"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>使用中的内置笔记本命令和功能 Azure Cosmos DB

Azure Cosmos DB 中的内置 Jupyter 笔记本使你可以从 Azure 门户分析和可视化数据。 本文介绍如何使用内置笔记本命令和功能执行常见操作。

## <a name="install-a-new-package"></a>安装新包
为 Azure Cosmos 帐户启用笔记本支持后，可以打开新的笔记本并安装包。

在新的代码单元中，插入并运行以下代码，并``PackageToBeInstalled``将替换为所需的 Python 包。
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
此包将可从 Azure Cosmos 帐户中的任何笔记本使用。 

## <a name="run-a-sql-query"></a>运行 SQL 查询

可以使用``%%sql``神奇命令针对帐户中的任何容器运行[SQL 查询](sql-query-getting-started.md)。 使用语法：

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- 将``{database_id}`` 和``{container_id}``替换为你的 Cosmos 帐户中的数据库和容器的名称。 如果未提供``--container``和参数，则将对[默认数据库和容器](#set-default-database-for-queries)执行查询。 ``--database``
- 您可以运行 Azure Cosmos DB 中有效的任何 SQL 查询。 查询文本必须在新行上。

例如： 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
在```%%sql?```单元格中运行，查看笔记本中 sql 魔术命令的帮助文档。

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>运行 SQL 查询并输出到 Pandas 数据帧

可以将``%%sql``查询的结果输出到[Pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe)中。 使用语法： 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- 将``{database_id}`` 和``{container_id}``替换为你的 Cosmos 帐户中的数据库和容器的名称。 如果未提供``--container``和参数，则将对[默认数据库和容器](#set-default-database-for-queries)执行查询。 ``--database``
- 替换``{outputDataFrameVar}``为将包含结果的数据帧变量的名称。
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

## <a name="set-default-database-for-queries"></a>设置查询的默认数据库
你可以设置将用于笔记本```%%sql```的默认数据库命令。 将```{database_id}```替换为数据库的名称。

```bash
%database {database_id}
```
在```%database?```单元格中运行，查看笔记本中的文档。

## <a name="set-default-container-for-queries"></a>设置查询的默认容器
你可以设置将用于笔记本```%%sql```的默认容器命令。 将```{container_id}```替换为容器的名称。

```bash
%container {container_id}
```
在```%container?```单元格中运行，查看笔记本中的文档。

## <a name="use-the-built-in-python-sdk"></a>使用内置的 Python SDK
安装了适用于[SQL API Azure Cosmos DB PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)的版本4，并将其包含在 Cosmos 帐户的笔记本环境中。

使用内置``cosmos_client``实例运行任何 SDK 操作。 

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

## <a name="create-a-custom-instance-of-cosmos_client"></a>创建的自定义实例``cosmos_client``
为了获得更大的灵活性，你可以创建的``cosmos_client``自定义实例，以便：

- 自定义[连接策略](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview)
- 针对不同于你所在的 Cosmos 帐户运行操作

可以通过[环境变量](#access-the-account-endpoint-and-primary-key-env-variables)访问当前帐户的连接字符串和主键。 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>访问帐户终结点和主密钥环境变量
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> ``COSMOS_ENDPOINT`` 和``COSMOS_KEY``环境变量仅适用于 SQL API。 对于其他 Api，请在 Cosmos 帐户的 "**连接字符串**" 或 "**密钥**" 边栏选项卡中找到终结点和密钥。  

## <a name="next-steps"></a>后续步骤

- 了解[Azure Cosmos DB Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)的优点
- 了解[Azure Cosmos DB 适用于 SQL API 的 PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
