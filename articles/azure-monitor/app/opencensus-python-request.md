---
title: Azure 应用程序 Insights 中通过 OpenCensus Python 进行传入请求跟踪 |Microsoft Docs
description: 通过 OpenCensus Python 监视对你的 Python 应用的请求调用。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587922"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>通过 OpenCensus Python 跟踪传入请求

传入请求数据是使用 OpenCensus Python 及其各种集成收集的。 跟踪发送到 web 应用程序的传入请求数据，这些数据是在常用 web 框架之上构建的 `django`，`flask` 和 `pyramid`。 然后，将数据发送到 Azure Monitor 下作为 `requests` 遥测 Application Insights。

首先，通过最新的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)检测你的 Python 应用程序。

## <a name="tracking-django-applications"></a>跟踪 Django 应用程序

1. 从[PyPI](https://pypi.org/project/opencensus-ext-django/)下载并安装 `opencensus-ext-django`，并通过 `django` 中间件来检测应用程序。 将跟踪发送到你的 `django` 应用程序的传入请求。

2. 将 `opencensus.ext.django.middleware.OpencensusMiddleware` 包含在 `settings.py` 文件中的 `MIDDLEWARE`下。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 请确保在 `OPENCENSUS`下的 `settings.py` 中正确配置了 AzureExporter。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. 你还可以将 url 添加到不想跟踪的请求 `BLACKLIST_PATHS` 下的 `settings.py`。

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>跟踪 Flask 应用程序

1. 从[PyPI](https://pypi.org/project/opencensus-ext-flask/)下载并安装 `opencensus-ext-flask`，并通过 `flask` 中间件来检测应用程序。 将跟踪发送到你的 `flask` 应用程序的传入请求。

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

2. 您可以直接在代码中配置 `flask` 中间件。 对于不希望跟踪的 url 发出的请求，请将它们添加到 `BLACKLIST_PATHS`。

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>跟踪棱锥应用程序

1. 从[PyPI](https://pypi.org/project/opencensus-ext-pyramid/)下载并安装 `opencensus-ext-django`，并通过 `pyramid` 补间来检测应用程序。 将跟踪发送到你的 `pyramid` 应用程序的传入请求。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 您可以直接在代码中配置 `pyramid` 的补间。 对于不希望跟踪的 url 发出的请求，请将它们添加到 `BLACKLIST_PATHS`。

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>后续步骤

* [应用程序映射](../../azure-monitor/app/app-map.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
* [搜索](../../azure-monitor/app/diagnostic-search.md)
* [日志（分析）查询](../../azure-monitor/log-query/log-query-overview.md)
* [事务诊断](../../azure-monitor/app/transaction-diagnostics.md)
