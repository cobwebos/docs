---
title: 快速入门：使用 Azure 数据资源管理器 Python 库查询数据
description: 本快速入门中，将了解如何使用 Python 在 Azure 数据资源管理器中查询数据。
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: f3a8dd001f4d1d930c700847f0725e00b7c29ecf
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715101"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>快速入门：使用 Azure 数据资源管理器 Python 库查询数据

Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure 数据资源管理器提供[适用于 Python 的数据客户端库](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)。 该库允许通过代码查询数据。 本快速入门中，你将连接到我们为帮助学习而设置的帮助群集上的表。 然后查询该群集上的表，并返回结果。

本快速入门同时也可用作 [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb)。

## <a name="prerequisites"></a>先决条件

* 组织电子邮件帐户是 Azure Active Directory (AAD) 的成员

* 已在开发计算机上安装了 [Python](https://www.python.org/downloads/)

## <a name="install-the-data-library"></a>安装数据的库

安装 azure-kusto-data。

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>添加导入语句和常量

从库中导入类以及数据分析库 pandas。

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Azure 数据资源管理器使用 AAD 租户 ID，以对应用程序进行身份验证。 要查找租户 ID，请使用以下 URL，并将域替换为 YourDomain。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

例如，如果域名为 contoso.com，则该 URL 将是：[https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)。 单击此 URL 以查看结果；第一行如下所示。

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

在这种情况下，租户 ID 为 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`。 在运行此代码之前为 AAD_TENANT_ID 设置值。

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

现在构造连接字符串。 此示例使用设备身份验证来访问群集。 此外可以使用 [AAD 应用程序证书](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24)、[AAD 应用程序密钥](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)以及 [AAD 用户和密码](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)。

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>连接到Azure 数据资源管理器并执行查询

针对群集执行查询，并将输出存储在数据帧中。 运行此代码时，它会返回如下消息：若要登录，请使用 Web 浏览器打开页面 https://microsoft.com/devicelogin，然后输入代码 F3W4VWZDM 进行身份验证。 按照步骤登录，然后返回以运行下一个代码块。

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>浏览 DataFrame 中的数据

输入登录名后，查询返回结果，并将它们存储在数据帧中。 你可以像处理任何其他数据帧一样处理该结果。

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

应看到 StormEvents 表中的前十个结果。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 数据资源管理器 Python 库引入数据](python-ingest-data.md)
