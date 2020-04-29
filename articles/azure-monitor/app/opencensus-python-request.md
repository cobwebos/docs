---
title: Azure 应用程序 Insights 中通过 OpenCensus Python 进行传入请求跟踪 |Microsoft Docs
description: 通过 OpenCensus Python 监视对你的 Python 应用的请求调用。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77669941"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>通过 OpenCensus Python 跟踪传入请求

传入请求数据是使用 OpenCensus Python 及其各种集成收集的。 跟踪发送到 web 应用程序的传入请求数据， `django` `flask`这些应用程序是在常用 web 框架`pyramid`和上构建的。 然后，将数据发送到 Azure Monitor 下作为`requests`遥测 Application Insights。

首先，通过最新的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)检测你的 Python 应用程序。

## <a name="tracking-django-applications"></a>跟踪 Django 应用程序

1. 从 PyPI 下载`opencensus-ext-django`并[PyPI](https://pypi.org/project/opencensus-ext-django/)安装并通过`django`中间件来检测应用程序。 将跟踪发送到你`django`的应用程序的传入请求。

2. 将`opencensus.ext.django.middleware.OpencensusMiddleware` `settings.py`包含在文件中`MIDDLEWARE`的下。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 请确保在`settings.py`下`OPENCENSUS`的中正确配置 AzureExporter。

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

4. 你还可以为你不`settings.py`希望`BLACKLIST_PATHS`跟踪的请求将 url 添加到下面。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>跟踪 Flask 应用程序

1. 从 PyPI 下载`opencensus-ext-flask`并[PyPI](https://pypi.org/project/opencensus-ext-flask/)安装并通过`flask`中间件来检测应用程序。 将跟踪发送到你`flask`的应用程序的传入请求。

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

2. 您可以直接在`flask`代码中配置中间件。 对于不希望跟踪的 url 发出的请求，请将其添加到`BLACKLIST_PATHS`中。

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

## <a name="tracking-pyramid-applications"></a>跟踪棱锥应用程序

1. 从 PyPI 下载`opencensus-ext-django`并[PyPI](https://pypi.org/project/opencensus-ext-pyramid/)安装并通过`pyramid`补间检测应用程序。 将跟踪发送到你`pyramid`的应用程序的传入请求。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 您可以直接在`pyramid`代码中配置补间。 对于不希望跟踪的 url 发出的请求，请将其添加到`BLACKLIST_PATHS`中。

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

* [应用程序地图](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [搜索](../../azure-monitor/app/diagnostic-search.md)
* [日志（分析）查询](../../azure-monitor/log-query/log-query-overview.md)
* [事务诊断](../../azure-monitor/app/transaction-diagnostics.md)
