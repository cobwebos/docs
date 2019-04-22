---
title: 配置 Ruby 应用 - Azure 应用服务
description: 本教程介绍为 Linux 上的 Azure 应用服务创作和配置 Ruby 应用时可用的选项。
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
ms.date: 03/28/2019
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 402c85e7902c8c2f612ad6c777d8f6773a4d0ca3
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549550"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>为 Azure 应用服务配置 Linux Ruby 应用

本文介绍 [Azure 应用服务](app-service-linux-intro.md)如何运行 Ruby 应用，以及如何按需自定义应用服务的行为。 必须连同所有必需的 [pip](https://pypi.org/project/pip/) 模块一起部署 Ruby 应用。

对于在应用服务中使用内置 Linux 容器的 Ruby 开发人员，本指南为其提供了关键概念和说明。 若从未使用过 Azure 应用服务，则首先应按照 [Ruby 快速入门](quickstart-ruby.md)以及[将 Ruby 与 PostgreSQL 配合使用教程](tutorial-ruby-postgres-app.md)进行操作。

## <a name="show-ruby-version"></a>显示 Ruby 版本

若要显示当前的 Ruby 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有受支持的 Ruby 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

生成自己的容器映像可以运行不受支持的 Ruby 版本。 有关详细信息，请参阅[使用自定义 Docker 映像](tutorial-custom-docker-image.md)。

## <a name="set-ruby-version"></a>设置 Ruby 版本

在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，将 Ruby 版本设置为 2.3：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> 如果在部署期间看到与以下类似的错误：
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> 或
> ```
> rbenv: version `2.3.1' is not installed
> ```
> 这意味着项目中配置的 Ruby 版本与正在运行的容器中安装的版本不同（在上面的示例中为 `2.3.3`）。 在上面的示例中，检查 Gemfile 和 .ruby-version 并验证是否未设置 Ruby 版本，或者是否设置为正在运行的容器中安装的版本（在上面的示例中为 `2.3.3`）。

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)。 然后，可以使用标准的 [ENV['<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) 模式访问这些设置。 例如，若要访问名为 `WEBSITE_SITE_NAME` 的应用设置，请使用以下代码：

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>自定义部署

部署 [Git 存储库](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)或者部署启用了生成过程的 [Zip 包](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)时，部署引擎 (Kudu) 会默认运行以下后期部署步骤：

1. 检查 Gemfile 是否存在。
1. 运行 `bundle clean`。 
1. 运行 `bundle install --path "vendor/bundle"`。
1. 运行 `bundle package`，将 gems 打包到 vendor/cache 文件夹中。

### <a name="use---without-flag"></a>使用 --without 标志

要使用 [--without](https://bundler.io/man/bundle-install.1.html) 标志运行 `bundle install`，请将 `BUNDLE_WITHOUT` [应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)设置为以逗号分隔的组列表。 例如，使用以下命令将其设置为 `development,test`。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

如果定义了此设置，那么部署引擎将使用 `--without $BUNDLE_WITHOUT` 运行 `bundle install`。

### <a name="precompile-assets"></a>预编译资产

默认情况下，后期部署步骤不会预编译资产。 要启用资产预编译，请将 `ASSETS_PRECOMPILE` [应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)设置为 `true`。 然后在后期部署步骤结束时运行命令 `bundle exec rake --trace assets:precompile`。 例如：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

有关详细信息，请参阅[提供静态资产](#serve-static-assets)。

## <a name="customize-start-up"></a>自定义启动

默认情况下，Ruby 容器按以下顺序启动 Rails 服务器（有关详细信息，请参阅[启动脚本](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh)）：

1. 生成 [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) 值（如果尚不存在）。 应用在生产模式下运行时需要此值。
1. 将 `RAILS_ENV` 环境变量设置为 `production`。
1. 在 tmp / pids 目录中，删除先前运行 Rails 服务器留下的任何 .pid 文件。
1. 检查是否安装了所有依赖项。 如果没有，请尝试从本地 vendor/cache 目录安装 gems。
1. 运行 `rails server -e $RAILS_ENV`。

可按以下方式自定义启动进程：

- [提供静态资产](#serve-static-assets)
- [在非生产模式下运行](#run-in-non-production-mode)
- [手动设置 secret_key_base](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>提供静态资产

默认情况下，Ruby 容器中的 Rails 服务器在生产模式下运行，[假定资产已进行预编译并由 Web 服务器](https://guides.rubyonrails.org/asset_pipeline.html#in-production)提供。 要从 Rails 服务器提供静态资产，需完成两个操作：

- **预编译资产** - [在本地预编译静态资产](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation)并手动部署它们。 或者，让部署引擎替你处理（请参阅[预编译资产](#precompile-assets)）。
- **启用提供静态文件** - 若要从 Ruby 容器提供静态资产，请设置 `RAILS_SERVE_STATIC_FILES`，[设置 `RAILS_SERVE_STATIC_FILES` 应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)，将其设置为 `true`。 例如：

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>在非生产模式下运行

默认情况下，Rails 服务器在生产模式下运行。 例如，要在开发模式下运行，请将 `RAILS_ENV` [应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)设置为 `development`。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

但是，仅此设置会导致 Rails 服务器在开发模式下启动，该模式仅接受 localhost 请求，并且在容器外部无法访问。 要接受远程客户端请求，请将 `APP_COMMAND_LINE` [应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)设置为 `rails server -b 0.0.0.0`。 此应用设置使你可在 Ruby 容器中运行自定义命令。 例如：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secretkeybase-manually"></a>手动设置 secret_key_base

要使用自己的 `secret_key_base` 值而不是让应用服务生成一个值，请使用想要的值设置 `SECRET_KEY_BASE` [应用设置](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。 例如：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 PostgreSQL 的 Rails 应用](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)