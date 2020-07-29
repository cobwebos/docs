---
title: 使用 OpenCensus Python 在 Azure Application Insights 中跟踪传入请求 | Microsoft Docs
description: 使用 OpenCensus Python 监视 Python 应用的请求调用。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: tracking-python
ms.openlocfilehash: 6eae11e2157904b47582f78a601c8d2900e61247
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324584"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>使用 OpenCensus Python 跟踪传入请求

使用 OpenCensus Python 及其各种集成收集传入请求数据。 跟踪发送到基于常用 Web 框架 `django`、`flask` 和 `pyramid` 构建的 Web 应用程序的传入请求数据。 然后，将数据作为 `requests` 遥测发送到 Azure Monitor 下的 Application Insights。

首先，使用最新版 [OpenCensus Python SDK](./opencensus-python.md) 检测 Python 应用程序。

## <a name="tracking-django-applications"></a>跟踪 Django 应用程序

1. 从 [PyPI](https://pypi.org/project/opencensus-ext-django/) 下载并安装 `opencensus-ext-django`，然后使用 `django` 中间件检测应用程序。 系统会跟踪发送到 `django` 应用程序的传入请求。

2. 将 `opencensus.ext.django.middleware.OpencensusMiddleware` 添加到 `settings.py` 文件中的 `MIDDLEWARE` 下。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 确保 AzureExporter 已在 `settings.py` 中的 `OPENCENSUS` 下正确配置。 对于来自不想跟踪的 URL 的请求，请将其添加到 `BLACKLIST_PATHS` 中。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>跟踪 Flask 应用程序

1. 从 [PyPI](https://pypi.org/project/opencensus-ext-flask/) 下载并安装 `opencensus-ext-flask`，然后使用 `flask` 中间件检测应用程序。 系统会跟踪发送到 `flask` 应用程序的传入请求。

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. 您还可以通过配置您的 `flask` 应用程序 `app.config` 。 对于来自不想跟踪的 URL 的请求，请将其添加到 `BLACKLIST_PATHS` 中。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>跟踪 Pyramid 应用程序

1. 从 [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) 下载并安装 `opencensus-ext-django`，然后使用 `pyramid` 中间件检测应用程序。 系统会跟踪发送到 `pyramid` 应用程序的传入请求。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 可以直接在代码中配置 `pyramid` 中间件。 对于来自不想跟踪的 URL 的请求，请将其添加到 `BLACKLIST_PATHS` 中。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>后续步骤

* [应用程序映射](./app-map.md)
* [可用性](./monitor-web-app-availability.md)
* [搜索](./diagnostic-search.md)
* [日志（分析）查询](../log-query/log-query-overview.md)
* [事务诊断](./transaction-diagnostics.md)

