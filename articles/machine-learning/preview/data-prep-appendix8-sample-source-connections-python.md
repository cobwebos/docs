---
title: "可用于 Azure 机器学习数据准备的其他源数据连接示例 | Microsoft Docs"
description: "本文档提供一组可用于 Azure 机器学习数据准备的源数据连接示例"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 66c356d6d42254e7443b645bff3393daca67012b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="sample-of-custom-source-connections-python"></a>自定义源连接示例 (Python) 
阅读本附录前，请先阅读 [Python 扩展性概述](data-prep-python-extensibility-overview.md)。

## <a name="load-data-from-dataworld"></a>从 data.world 加载数据

### <a name="prerequisites"></a>先决条件

#### <a name="register-yourself-at-dataworld"></a>在 data.world 自行注册
需要来自 data.world 网站的 API 令牌。

#### <a name="install-dataworld-library"></a>安装 data.world 库

通过选择“文件” > “打开命令行接口”，打开 Azure Machine Learning Workbench 命令行接口。

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

然后在命令行中运行 `dw configure`，该命令会提示输入令牌。 输入令牌后，主目录中会创建一个 .dw/ 目录，令牌会存储在此处。

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
现在应能够导入 data.world 库。

#### <a name="load-data-into-data-preparation"></a>在数据准备中加载数据

创建“转换数据流(脚本)”转换。 然后使用以下脚本从 data.world 加载数据。

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>作为数据源连接的 Azure Cosmos DB
有关作为数据连接的 Azure Cosmos DB 示例，请阅读[加载作为源数据连接的 Azure Cosmos DB](data-prep-load-azure-cosmos-db.md)
