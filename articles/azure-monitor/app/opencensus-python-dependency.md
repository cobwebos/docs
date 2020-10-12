---
title: 使用 OpenCensus Python 在 Azure Application Insights 中跟踪依赖项 | Microsoft Docs
description: 通过 OpenCensus Python 监视 Python 应用的依赖项调用。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: abfd5e104bd4854781a0d3c9d08544506279518a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87850077"
---
# <a name="track-dependencies-with-opencensus-python"></a>使用 OpenCensus Python 跟踪依赖项

依赖项是由应用程序调用的外部组件。 可以使用 OpenCensus Python 及其各种集成收集依赖项数据。 然后，将数据作为 `dependencies` 遥测发送到 Azure Monitor 下的 Application Insights。

首先，使用最新版 [OpenCensus Python SDK](./opencensus-python.md) 检测 Python 应用程序。

## <a name="in-process-dependencies"></a>进程内依赖项

OpenCensus Python SDK for Azure Monitor 允许发送“进程内”依赖项遥测（应用程序中出现的信息和逻辑）。 在分析中，进程内依赖项的 `type` 字段将为 `INPROC`。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>使用“requests”集成跟踪的依赖项

通过 OpenCensus `requests` 集成来跟踪传出请求。

从 [PyPI](https://pypi.org/project/opencensus-ext-requests/) 下载并安装 `opencensus-ext-requests`，然后将其添加到跟踪集成。 将跟踪使用 Python [requests](https://pypi.org/project/requests/) 库发送的请求。

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>使用“httplib”集成跟踪的依赖项

通过 OpenCensus `httplib` 集成跟踪传出请求。

从 [PyPI](https://pypi.org/project/opencensus-ext-httplib/) 下载并安装 `opencensus-ext-httplib`，然后将其添加到跟踪集成。 将跟踪使用 Python3 的 [http.client](https://docs.python.org/3.7/library/http.client.html) 或 Python2 的 [httplib](https://docs.python.org/2/library/httplib.html) 发送的请求。

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>使用“django”集成跟踪的依赖项

通过 OpenCensus `django` 集成跟踪传出 Django 请求。

> [!NOTE]
> 跟踪的唯一传出 Django 请求是对数据库的调用。 有关对 Django 应用程序发出的请求，请参阅[传入请求](./opencensus-python-request.md#tracking-django-applications)。

从 [PyPI](https://pypi.org/project/opencensus-ext-django/) 下载并安装 `opencensus-ext-django`，然后将以下行添加到 Django `settings.py` 文件中的 `MIDDLEWARE` 节。

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

可以提供其他配置。如需完整的参考内容，请阅读[自定义](https://github.com/census-instrumentation/opencensus-python#customization)。

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>使用“mysql”集成跟踪的依赖项

通过 OpenCensus `mysql` 集成跟踪 MYSQL 依赖项。 此集成支持 [mysql-connector](https://pypi.org/project/mysql-connector-python/) 库。

从 [PyPI](https://pypi.org/project/opencensus-ext-mysql/) 下载并安装 `opencensus-ext-mysql`，然后将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>使用“pymysql”集成跟踪的依赖项

通过 OpenCensus `pymysql` 集成跟踪 PyMySQL 依赖项。

从 [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) 下载并安装 `opencensus-ext-pymysql`，然后将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>使用“postgresql”集成跟踪的依赖项

通过 OpenCensus `postgresql` 集成跟踪 PostgreSQL 依赖项。 此集成支持 [psycopg2](https://pypi.org/project/psycopg2/) 库。

从 [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) 下载并安装 `opencensus-ext-postgresql`，然后将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>使用“pymongo”集成跟踪的依赖项

通过 OpenCensus `pymongo` 集成跟踪 MongoDB 依赖项。 此集成支持 [pymongo](https://pypi.org/project/pymongo/) 库。

从 [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) 下载并安装 `opencensus-ext-pymongo`，然后将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>使用“sqlalchemy”集成跟踪的依赖项

通过 OpenCensus `sqlalchemy` 集成使用 SQLAlchemy 跟踪依赖项。 此集成跟踪 [sqlalchemy](https://pypi.org/project/SQLAlchemy/) 包的使用情况，而与基础数据库无关。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>后续步骤

* [应用程序映射](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [搜索](./diagnostic-search.md)
* [日志（分析）查询](../log-query/log-query-overview.md)
* [事务诊断](./transaction-diagnostics.md)

