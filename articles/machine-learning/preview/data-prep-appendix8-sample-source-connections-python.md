---
title: "可用于 Azure 机器学习数据准备的其他源数据连接示例 | Microsoft Docs"
description: "本文档提供一组可用于 Azure ML 数据准备的源数据连接示例"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 550cca100314009f63eec2136e8c65426d8bf07f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sample-of-custom-source-connections-python"></a>自定义源连接示例 (Python) 
阅读本附录前，先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)

## <a name="loading-data-from-dataworld"></a>从 data.world 加载数据

### <a name="prerequisites"></a>先决条件

#### <a name="register-yourself-at-dataworld"></a>在 data.world 自行注册
需要从 data.world 网站获取的 API 令牌。

#### <a name="install-dataworld-library"></a>安装 data.world 库

通过“文件”->“打开命令行接口”，打开 Azure Machine Learning Workbench 命令行接口

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

然后在命令行中运行 `dw configure`，该命令会提示输入令牌。 输入令牌后，主目录中会创建一个 .dw/ 目录，令牌会存储在此处。

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
现在应能够导入 data.world 库。

#### <a name="load-data-into-data-preparation"></a>在数据准备中加载数据

创建新的基于脚本的数据流，然后使用以下脚本从 data.world 加载数据

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#load the dataset
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-cosmosdb-data-into-data-preparation"></a>在数据准备中加载 CosmosDB 数据

创建新的基于脚本的数据流，然后使用以下脚本从 CosmosDB 加载数据（需要先安装库，请参阅上文链接的参考文档）

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
