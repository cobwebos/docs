---
title: 使用 OpenCensus Python 在 Azure 应用程序洞察中进行传入请求跟踪 |微软文档
description: 通过 OpenCensus Python 监视 Python 应用的请求呼叫。
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669941"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>使用 OpenCensus Python 跟踪传入请求

传入请求数据使用 OpenCensus Python 及其各种集成进行收集。 跟踪发送到在常用 Web 框架`django``flask`和 之上构建的 Web 应用程序的传入请求`pyramid`数据。 然后，数据作为`requests`遥测数据发送到 Azure 监视器下的应用程序见解。

首先，使用最新的[OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md)来检测您的 Python 应用程序。

## <a name="tracking-django-applications"></a>跟踪 Django 应用程序

1. 从[PyPI](https://pypi.org/project/opencensus-ext-django/)下载和安装`django``opencensus-ext-django`，使用中间件检测您的应用程序。 发送到您的`django`应用程序的传入请求将被跟踪。

2. 包括在`opencensus.ext.django.middleware.OpencensusMiddleware`您的`settings.py`文件下`MIDDLEWARE`。

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. 确保 Azure 导出器在 下`settings.py`已`OPENCENSUS`正确配置。

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

4. 您还可以为`settings.py`不想跟踪的请求向下`BLACKLIST_PATHS`添加 URL。

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

## <a name="tracking-flask-applications"></a>跟踪烧瓶应用

1. 从[PyPI](https://pypi.org/project/opencensus-ext-flask/)下载和安装`flask``opencensus-ext-flask`，使用中间件检测您的应用程序。 发送到您的`flask`应用程序的传入请求将被跟踪。

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

2. 您可以直接在代码`flask`中配置中间件。 对于来自不希望跟踪的 URL 的请求，请将它们添加到`BLACKLIST_PATHS`。

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

## <a name="tracking-pyramid-applications"></a>跟踪金字塔应用程序

1. 从[PyPI](https://pypi.org/project/opencensus-ext-pyramid/)下载和安装`pyramid``opencensus-ext-django`，用补间检测您的应用程序。 发送到您的`pyramid`应用程序的传入请求将被跟踪。

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. 可以直接在代码中`pyramid`配置补间。 对于来自不希望跟踪的 URL 的请求，请将它们添加到`BLACKLIST_PATHS`。

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
