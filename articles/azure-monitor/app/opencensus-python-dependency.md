---
title: 使用 OpenCensus Python 在 Azure 应用程序洞察中跟踪依赖项 |微软文档
description: 通过 OpenCensus Python 监视 Python 应用的依赖项调用。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669924"
---
# <a name="track-dependencies-with-opencensus-python"></a>使用 OpenCensus Python 跟踪依赖项

依赖项是应用程序调用的外部组件。 依赖项数据使用 OpenCensus Python 及其各种集成进行收集。 然后，数据作为`dependencies`遥测数据发送到 Azure 监视器下的应用程序见解。

首先，使用最新的[OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md)来检测您的 Python 应用程序。

## <a name="in-process-dependencies"></a>进程内依赖项

Azure 监视器的 OpenCensus Python SDK 允许您发送"进程内"依赖项遥测（应用程序中发生的信息和逻辑）。 进程内依赖项将具有`type`与分析一样`INPROC`的字段。

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>具有"请求"集成的依赖项

使用 OpenCensus`requests`集成跟踪传出请求。

从`opencensus-ext-requests`[PyPI](https://pypi.org/project/opencensus-ext-requests/)下载并安装，并将其添加到跟踪集成中。 将使用 Python 请求库发送[的请求](https://pypi.org/project/requests/)将被跟踪。

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

## <a name="dependencies-with-httplib-integration"></a>具有"httplib"集成的依赖项

通过 OpenCensus`httplib`集成跟踪传出请求。

从`opencensus-ext-httplib`[PyPI](https://pypi.org/project/opencensus-ext-httplib/)下载并安装，并将其添加到跟踪集成中。 将使用[http.client](https://docs.python.org/3.7/library/http.client.html)为 Python3 或[httplib](https://docs.python.org/2/library/httplib.html)发送的 Python2 请求将被跟踪。

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

## <a name="dependencies-with-django-integration"></a>具有"django"集成的依赖关系

使用 OpenCensus`django`集成跟踪传出的 Django 请求。

从[PyPI](https://pypi.org/project/opencensus-ext-django/)下载并安装`MIDDLEWARE``settings.py``opencensus-ext-django`，并将以下行添加到 Django 文件中的部分。

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

可以提供其他配置，读取[自定义以](https://github.com/census-instrumentation/opencensus-python#customization)进行完整参考。

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

## <a name="dependencies-with-mysql-integration"></a>具有"mysql"集成的依赖项

使用 OpenCensus`mysql`集成跟踪 MYSQL 依赖项。 此集成支持[mysql 连接器](https://pypi.org/project/mysql-connector-python/)库。

从`opencensus-ext-mysql`[PyPI](https://pypi.org/project/opencensus-ext-mysql/)下载并安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>具有"pymysql"集成的依赖项

使用 OpenCensus`pymysql`集成跟踪您的 PyMySQL 依赖项。

从`opencensus-ext-pymysql`[PyPI](https://pypi.org/project/opencensus-ext-pymysql/)下载并安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>具有"后格雷sql"集成的依赖项

使用 OpenCensus`postgresql`集成跟踪 PostgreSQL 依赖项。 此集成支持[psycopg2](https://pypi.org/project/psycopg2/)库。

从`opencensus-ext-postgresql`[PyPI](https://pypi.org/project/opencensus-ext-postgresql/)下载并安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>具有"pymongo"集成的依赖关系

使用 OpenCensus`pymongo`集成跟踪您的 MongoDB 依赖项。 此集成支持[pymongo](https://pypi.org/project/pymongo/)库。

从`opencensus-ext-pymongo`[PyPI](https://pypi.org/project/opencensus-ext-pymongo/)下载并安装，并将以下行添加到代码中。

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>具有"sqlchemy"集成的依赖项

使用 OpenCensus`sqlalchemy`集成使用 SQLAlchemy 跟踪您的依赖项。 此集成跟踪[sqlalchemy](https://pypi.org/project/SQLAlchemy/)包的使用情况，而不考虑基础数据库。

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
