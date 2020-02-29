---
title: 配置 PHP 应用
description: 了解如何为应用程序配置预先构建的 PHP 容器。 本文介绍最常见的配置任务。
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: e805487075499bd4e461a21fffb4c44156ce192b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913865"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>为 Azure App Service 配置 Linux PHP 应用

本指南演示如何在 Azure App Service 中配置 web 应用、移动后端和 API 应用的内置 PHP 运行时。

本指南为在应用服务中使用内置 Linux 容器的 PHP 开发人员提供重要的概念和说明。 如果你从未使用过 Azure App Service，请先参阅[php 快速入门](quickstart-php.md)和[php with MySQL 教程](tutorial-php-mysql-app.md)。

## <a name="show-php-version"></a>显示 PHP 版本

若要显示当前 PHP 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

若要显示所有支持的 PHP 版本，请在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>设置 PHP 版本

在[Cloud Shell](https://shell.azure.com)中运行以下命令，将 PHP 版本设置为7.2：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>自定义生成自动化

如果在启用了生成自动化的情况下使用 Git 或 zip 包部署应用，应用服务将通过以下顺序生成自动化步骤：

1. 如果 `PRE_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。
1. 运行 `php composer.phar install`。
1. 如果 `POST_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是默认情况下为空的环境变量。 若要运行预生成命令，请定义 `PRE_BUILD_COMMAND`。 若要运行生成后命令，请定义 `POST_BUILD_COMMAND`。

下面的示例为一系列命令指定了两个变量，用逗号分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关自定义生成自动化的其他环境变量，请参阅[Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

若要详细了解应用服务的运行方式以及如何在 Linux 中生成 PHP 应用，请参阅[Oryx 文档：如何检测和构建 php 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)。

## <a name="customize-start-up"></a>自定义启动

默认情况下，内置 PHP 容器运行 Apache 服务器。 在启动时，它运行 `apache2ctl -D FOREGROUND"`。 如果需要，可以在启动时运行不同的命令，方法是在[Cloud Shell](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，你可以使用标准[getenv （）](https://secure.php.net/manual/function.getenv.php)模式访问它们。 例如，若要访问名为 `DB_HOST` 的应用设置，请使用以下代码：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>更改站点根目录

所选的 web 框架可能使用子目录作为网站根目录。 例如， [Laravel](https://laravel.com/)使用 `public/` 子目录作为网站根目录。

应用服务的默认 PHP 映像使用 Apache，不允许你为应用自定义站点根。 若要解决此限制，请将 *.htaccess*文件添加到存储库根目录中，其中包含以下内容：

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

如果不希望使用 *.htaccess* 重写，可以改用[自定义 Docker 映像](quickstart-docker-go.md)来部署 Laravel 应用程序。

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果应用逻辑需要检查用户请求是否已加密，可以检查 `X-Forwarded-Proto` 标头。

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

使用常用 Web 框架可以访问采用标准应用模式的 `X-Forwarded-*` 信息。 在 [CodeIgniter](https://codeigniter.com/) 中，[is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) 默认检查 `X_FORWARDED_PROTO` 的值。

## <a name="customize-phpini-settings"></a>自定义 php .ini 设置

如果需要更改 PHP 安装，可以通过执行下列步骤来更改任何[php 指令](https://www.php.net/manual/ini.list.php)。

> [!NOTE]
> 查看 PHP 版本和当前*php .ini*配置的最佳方式是在应用程序中调用[phpinfo （）](https://php.net/manual/function.phpinfo.php) 。
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>自定义-非 PHP_INI_SYSTEM 指令

若要自定义 PHP_INI_USER、PHP_INI_PERDIR 和 PHP_INI_ALL 指令（请参阅[PHP 指令](https://www.php.net/manual/ini.list.php)），请将 *.htaccess*文件添加到应用程序的根目录中。

在 *.htaccess*文件中，使用 `php_value <directive-name> <value>` 语法添加指令。 例如：

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

重新部署你的应用并将其重新启动。 如果使用 Kudu 部署它（例如，使用[Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)），则它会在部署后自动重启。

作为使用 *.htaccess*的替代方法，你可以使用应用中的[ini_set （）](https://www.php.net/manual/function.ini-set.php)自定义这些非 PHP_INI_SYSTEM 指令。

### <a name="customize-php_ini_system-directives"></a>自定义 PHP_INI_SYSTEM 指令

若要自定义 PHP_INI_SYSTEM 指令（请参阅[PHP 指令](https://www.php.net/manual/ini.list.php)），则不能使用 *.htaccess*方法。 应用服务使用 `PHP_INI_SCAN_DIR` 应用设置提供一种单独的机制。

首先，在[Cloud Shell](https://shell.azure.com)中运行以下命令，以添加名为 `PHP_INI_SCAN_DIR`的应用设置：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` 是*php*所在的默认目录。 `/home/site/ini` 是要在其中添加自定义 *.ini*文件的自定义目录。 使用 `:`分隔值。

导航到与 Linux 容器的 web SSH 会话（`https://<app-name>.scm.azurewebsites.net/webssh/host`）。

在 `/home/site` 中创建一个名为 `ini`的目录，然后使用要自定义的指令在 `/home/site/ini` 目录（例如，*设置 .ini）* 中创建一个 *.ini*文件。 使用在*php .ini*文件中使用的语法。 

> [!TIP]
> 在应用服务中的内置 Linux 容器中， */home*用作持久性共享存储。 
>

例如，若要更改[expose_php](https://php.net/manual/ini.core.php#ini.expose-php)的值，请运行以下命令：

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

要使更改生效，请重新启动应用。

## <a name="enable-php-extensions"></a>启用 PHP 扩展

内置 PHP 安装包含最常用的扩展。 您可以使用与[自定义 php 指令](#customize-php_ini_system-directives)相同的方式来启用其他扩展。

> [!NOTE]
> 查看 PHP 版本和当前*php .ini*配置的最佳方式是在应用程序中调用[phpinfo （）](https://php.net/manual/function.phpinfo.php) 。
>

若要启用其他扩展，请执行下列步骤：

将 `bin` 目录添加到应用的根目录，并将 `.so` 扩展文件放入其中（例如， *mongodb.so*）。 请确保这些扩展与 Azure 中的 PHP 版本兼容，并且与 VC9 和非线程安全（nts）兼容。

部署所做的更改。

按照[自定义 PHP_INI_SYSTEM 指令](#customize-php_ini_system-directives)中的步骤操作，将扩展添加到具有[扩展名](https://www.php.net/manual/ini.core.php#ini.extension)或[zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension)指令的自定义 *.ini*文件中。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

要使更改生效，请重新启动应用。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>故障排除

如果运行中的 PHP 应用在应用服务中的行为不同或有错误，请尝试以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下本地测试应用。 应用服务在生产模式下运行 node.js 应用，因此，你需要确保你的项目在生产模式下以本地方式按预期方式工作。 例如：
    - 根据你的*作曲*程序，可能会为生产模式安装不同的包（`require` 与 `require-dev`）。
    - 某些 web 框架可以在生产模式下以不同的方式部署静态文件。
    - 在生产模式下运行时，某些 web 框架可能使用自定义的启动脚本。
- 在调试模式下，在应用服务中运行应用。 例如，在[Laravel](https://meanjs.org/)中，可以将应用配置为在生产中输出调试消息，方法是[将 `APP_DEBUG` 应用设置设置为 `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。

### <a name="robots933456"></a>robots933456

你可能会在容器日志中看到以下消息：

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

您可以放心地忽略此消息。 `/robots933456.txt` 是应用服务用于检查容器能否为请求提供服务的虚拟 URL 路径。 404响应只是指示该路径不存在，但它允许应用服务知道容器处于正常状态，并且可以响应请求。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：带 MySQL 的 PHP 应用](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
