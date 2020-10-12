---
title: 获取已启用 Arc 的连接终结点和窗体连接字符串 PostgreSQL 超大规模服务器组
titleSuffix: Azure Arc enabled data services
description: 获取已启用 Arc 的连接终结点和窗体连接字符串 PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4087d618209ab4db46f89ef4e6db7ac87ca4cf57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331006"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>获取已启用 Arc 的连接终结点和窗体连接字符串 PostgreSQL 超大规模服务器组

本文介绍如何检索服务器组的连接终结点，以及如何形成将用于应用程序和/或工具的连接字符串。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>获取连接终结点：

### <a name="from-cli-with-azdata"></a>通过 azdata 的 CLI
#### <a name="1-connect-to-your-arc-data-controller"></a>1. 连接到 Arc 数据控制器：
- 如果已在 Arc 数据控制器的主机上打开会话，请运行以下命令：
```console
azdata login
```

- 如果在 Arc 数据控制器的主机上未打开会话，请运行以下命令： 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. 显示连接端点
运行以下命令：
```console
azdata arc postgres endpoint list -n <server group name>
```
它返回如下所示的输出：
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```
使用以下终结点：
- 建立连接字符串并连接到客户端工具或应用程序
- 从浏览器访问 Grafana 和 Kibana 仪表板

例如，您可以使用名为 _PostgreSQL 实例_ 的终结点连接到您的服务器组的 psql。 例如：
```console
psql postgresql://postgres:MyPassworkd@12.345.123.456:1234
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - 在名为 "_PostgreSQL Instance_" 的终结点中指示的_postgres_用户的密码是在部署服务器组时选择的密码。
> - 关于 azdata：与连接关联的租约持续大约10个小时。 完成后，需要重新连接。 如果你的租约已过期，当你尝试执行命令时，如果你尝试执行的命令的 azdata (不是 azdata login) ： _error： (401) _ 
>  _原因：未经授权_的 
>  _HTTP 响应标头： HTTPHeaderDict ( {"Date"： "Sun，06： 2020 16:58:38 GMT"，"content-type-长度"： "0"，"WWW-身份验证"： "基本_ 
>  _领域 =" 需要登录凭据_"，持有者错误 =" invalid_token "，error_description =" 令牌已过期 ") _

## <a name="from-cli-with-kubectl"></a>通过 kubectl 的 CLI
- 如果你的服务器组是 Postgres 版本 12 (默认) ，则以下命令：
```console
kubectl get postgresql-12/<server group name>
```
- 如果服务器组的版本为 Postgres，则以下命令为：
```console
kubectl get postgresql-11/<server group name>
```

这些命令将产生如下所示的输出。 您可以使用该信息来形成您的连接字符串：
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>窗体连接字符串：
对于服务器组，请使用下面的连接字符串模板表。 然后，可以根据需要进行复制/粘贴和自定义：

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C + + (libpq) 

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

### <a name="web-app"></a>Web 应用

```webapp
Database=postgres; Data Source=192.168.1.121; User Id=postgres; Password={your_password_here}
```

## <a name="next-steps"></a>后续步骤
- 阅读[ (向) 服务器组添加辅助角色节点](scale-out-postgresql-hyperscale-server-group.md)
- 阅读有关[扩展或缩减 (增加/减少服务器组) 的内存/vcore](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)


