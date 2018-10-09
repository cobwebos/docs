---
title: 在 Azure 应用服务中配置内置的 Python 映像
description: 本教程介绍了使用内置 Python 映像在 Azure 应用服务上创作和配置 Python 应用程序的选项。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2018
ms.author: astay;cephalin
ms.custom: mvc
ms.openlocfilehash: 9316805993b81e4d2511e833e0cc8f240807a1f9
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228540"
---
# <a name="configure-built-in-python-image-in-azure-app-service-preview"></a>在 Azure 应用服务中配置内置的 Python 映像（预览版）

本文介绍了如何在 [Linux 上的应用服务](app-service-linux-intro.md)中配置 Python 内置映像来运行 Python 应用程序。

## <a name="python-version"></a>Python 版本

Linux 上的应用服务中的 Python 运行时使用版本 `python-3.7.0`。

## <a name="supported-frameworks"></a>支持的框架

与 `python-3.7` 运行时兼容的符合 Web 服务器网关接口 (WSGI) 标准的 Web 框架的所有版本都受支持。

## <a name="package-management"></a>包管理

在 Git 发布期间，Kudu 引擎会在存储库根目录中查找 [requirements.txt](https://pip.pypa.io/en/stable/user_guide/#requirements-files) 并使用 `pip` 自动在 Azure 中安装程序包。

若要在发布前生成此文件，请导航到存储库根目录并在你的 Python 环境中运行以下命令：

```bash
pip freeze > requirements.txt
```

## <a name="configure-your-python-app"></a>配置 Python 应用

Azure 应用服务中的内置 Python 映像使用 [Gunicorn](http://gunicorn.org/) 服务器运行 Python 应用程序。 Gunicorn 是适用于 UNIX 的 Python WSGI HTTP 服务器。 应用服务自动为 Django 和 Flask 项目配置 Gunicorn。

### <a name="django-app"></a>Django 应用

如果发布包含 `wsgi.py` 模块的 Django 项目，则 Azure 会使用以下命令自动调用 Gunicorn：

```bash
gunicorn <path_to_wsgi>
```

### <a name="flask-app"></a>Flask 应用

如果要发布 Flask 应用，并且入口点在 `application.py` 或 `app.py` 模块中，则 Azure 会分别使用下列命令之一自动调用 Gunicorn：

```bash
gunicorn application:app
```

或 

```bash
gunicorn app:app
```

### <a name="customize-start-up"></a>自定义启动

若要为应用定义自定义入口点，请首先创建一个包含自定义 Gunicorn 命令的 _.txt_ 文件并将其放置在你的项目的根目录中。 例如，若要使用模块 _helloworld.py_ 和变量 `app` 启动服务器，请创建具有以下内容的 _startup.txt_：

```bash
gunicorn helloworld:app
```

在[应用程序设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)页面上，选择 **Python|3.7** 作为**运行时堆栈**，并提供上一步中的**启动文件**的名称。 例如 _startup.txt_。
