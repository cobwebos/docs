---
title: Azure 应用程序 Insights 中的依赖关系跟踪与 OpenCensus Python |Microsoft Docs
description: 通过 OpenCensus Python 监视 Python 应用的依赖项调用。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669924"
---
# <a name="track-dependencies-with-opencensus-python"></a>跟踪 OpenCensus Python 的依赖项

依赖项是应用程序调用的外部组件。 使用 OpenCensus Python 及其各种集成收集依赖关系数据。 然后，将数据发送到 Azure Monitor 下作为`dependencies`遥测 Application Insights。

首先，通过最新的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)检测你的 Python 应用程序。

## <a name="in-process-dependencies"></a>进程内依赖项

OpenCensus Python SDK for Azure Monitor 允许发送 "进程内" 依赖项遥测（在应用程序中发生的信息和逻辑）。 进程内依赖项将具有与`type`在分析`INPROC`中相同的字段。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>与 "请求" 集成的依赖项

通过 OpenCensus `requests`集成跟踪传出请求。

从 PyPI 下载`opencensus-ext-requests`并[PyPI](https://pypi.org/project/opencensus-ext-requests/)安装，并将其添加到跟踪集成。 将跟踪使用 Python[请求](https://pypi.org/project/requests/)库发送的请求。

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

## <a name="dependencies-with-httplib-integration"></a>与 "httplib" 集成的依赖项

通过 OpenCensus `httplib`集成跟踪传出请求。

从 PyPI 下载`opencensus-ext-httplib`并[PyPI](https://pypi.org/project/opencensus-ext-httplib/)安装，并将其添加到跟踪集成。 将跟踪使用 http 发送的请求。 Python3 或[httplib](https://docs.python.org/2/library/httplib.html) for Python2 的[客户端](https://docs.python.org/3.7/library/http.client.html)。

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

## <a name="dependencies-with-django-integration"></a>与 "django" 集成的依赖项

通过 OpenCensus `django`集成跟踪传出的 Django 请求。

从 PyPI 下载`opencensus-ext-django`并[PyPI](https://pypi.org/project/opencensus-ext-django/)安装，并将以下行添加到`MIDDLEWARE` Django `settings.py`文件中的部分。

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

可以提供其他配置，读取[自定义项](https://github.com/census-instrumentation/opencensus-python#customization)以获取完整引用。

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

## <a name="dependencies-with-mysql-integration"></a>与 "mysql" 集成的依赖项

通过 OpenCensus `mysql`集成跟踪 MYSQL 依赖项。 此集成支持[mysql 连接器](https://pypi.org/project/mysql-connector-python/)库。

从 PyPI 下载`opencensus-ext-mysql`并[PyPI](https://pypi.org/project/opencensus-ext-mysql/)安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>与 "pymysql" 集成的依赖项

通过 OpenCensus `pymysql`集成跟踪 PyMySQL 依赖项。

从 PyPI 下载`opencensus-ext-pymysql`并[PyPI](https://pypi.org/project/opencensus-ext-pymysql/)安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>与 "postgresql" 集成的依赖项

通过 OpenCensus `postgresql`集成跟踪 PostgreSQL 依赖项。 此集成支持[psycopg2](https://pypi.org/project/psycopg2/)库。

从 PyPI 下载`opencensus-ext-postgresql`并[PyPI](https://pypi.org/project/opencensus-ext-postgresql/)安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>与 "pymongo" 集成的依赖项

通过 OpenCensus `pymongo`集成跟踪 MongoDB 依赖项。 此集成支持[pymongo](https://pypi.org/project/pymongo/)库。

从 PyPI 下载`opencensus-ext-pymongo`并[PyPI](https://pypi.org/project/opencensus-ext-pymongo/)安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>与 "sqlalchemy" 集成的依赖项

使用 OpenCensus `sqlalchemy`集成跟踪使用 SQLAlchemy 的依赖项。 此集成跟踪[sqlalchemy](https://pypi.org/project/SQLAlchemy/)包的使用情况，而与基础数据库无关。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>后续步骤

* [应用程序地图](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [搜索](../../azure-monitor/app/diagnostic-search.md)
* [日志（分析）查询](../../azure-monitor/log-query/log-query-overview.md)
* [事务诊断](../../azure-monitor/app/transaction-diagnostics.md)
