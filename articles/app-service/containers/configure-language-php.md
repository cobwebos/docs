---
title: 配置 PHP 应用
description: 了解如何为应用配置预先构建的 PHP 容器。 本文介绍最常见的配置任务。
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e4237f1eecb9f6542aac946525ff4583e478c2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905691"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>为 Azure 应用服务配置 Linux PHP 应用

本指南介绍如何在 Azure 应用服务中为 Web 应用、移动设备后端和 API 应用配置内置 PHP 运行时。

本指南为使用应用服务中内置 Linux 容器的 PHP 开发人员提供了关键概念和说明。 若从未使用过 Azure 应用服务，则首先应按照 [PHP 快速入门](quickstart-php.md)以及[将 PHP 与 MySQL 配合使用教程](tutorial-php-mysql-app.md)进行操作。

## <a name="show-php-version"></a>显示 PHP 版本

要显示当前的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要显示所有受支持的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>设置 PHP 版本

在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，将 PHP 版本设置为 7.2：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>自定义生成自动化

如果在启用生成自动化的情况下使用 Git 或 zip 包部署应用，应用服务生成自动化将按以下顺序完成各个步骤：

1. 运行 `PRE_BUILD_SCRIPT_PATH` 指定的自定义脚本。
1. 运行 `php composer.phar install`。
1. 运行 `POST_BUILD_SCRIPT_PATH` 指定的自定义脚本。

`PRE_BUILD_COMMAND` 和 `POST_BUILD_COMMAND` 是默认为空的环境变量。 若要运行生成前命令，请定义 `PRE_BUILD_COMMAND`。 若要运行生成后命令，请定义 `POST_BUILD_COMMAND`。

以下示例在一系列命令中指定两个以逗号分隔的变量。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关用于自定义生成自动化的其他环境变量，请参阅 [Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有关应用服务如何在 Linux 中运行和构建 PHP 应用的详细信息，请参阅 [Oryx 文档：如何检测和构建 PHP 应用](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)。

## <a name="customize-start-up"></a>自定义启动

默认情况下，内置 PHP 容器运行 Apache 服务器。 启动时，它会运行 `apache2ctl -D FOREGROUND"`。 如有需要，可在启动时运行其他命令，方法是在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，可以使用标准的 [getenv()](https://secure.php.net/manual/function.getenv.php) 模式访问这些设置。 例如，若要访问名为 `DB_HOST` 的应用设置，请使用以下代码：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>更改站点根路径

所选的 Web 框架可能使用子目录作为站点根路径。 例如，[Laravel](https://laravel.com/) 使用 `public/` 子目录作为站点根路径。

应用服务的默认 PHP 映像使用 Apache，不允许为应用自定义站点根路径。 若要避开此限制，请将 .htaccess 文件添加到存储库根路径，并包含以下内容：

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
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

## <a name="customize-phpini-settings"></a>自定义 php.ini 设置

如果需要对 PHP 安装进行更改，则可以按照以下步骤更改任何 [php.ini 指令](https://www.php.net/manual/ini.list.php)。

> [!NOTE]
> 查看 PHP 版本和当前 php.ini 配置的最佳方法是在应用中调用 [phpinfo()](https://php.net/manual/function.phpinfo.php)。
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>自定义非 PHP_INI_SYSTEM 指令

要自定义 PHP_INI_USER、PHP_INI_PERDIR 和 PHP_INI_ALL 指令（请参阅 [php.ini 指令](https://www.php.net/manual/ini.list.php)），请将 .htaccess 文件添加到应用的根目录中。

在 .htaccess 文件中，请使用 `php_value <directive-name> <value>` 语法添加指令。 例如：

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

通过更改重新部署应用，然后重启该应用。 如果使用 Kudu（例如，使用 [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)）部署应用，则该应用会在部署后自动重启。

作为使用 .htaccess 的替代方法，可以在应用中使用 [ini_set()](https://www.php.net/manual/function.ini-set.php) 来自定义这些非 PHP_INI_SYSTEM 指令。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自定义 PHP_INI_SYSTEM 指令

不能使用 .htaccess 方法自定义 PHP_INI_SYSTEM 指令（请参阅 [php.ini 指令](https://www.php.net/manual/ini.list.php)）。 应用服务使用 `PHP_INI_SCAN_DIR` 应用设置提供了一种单独的机制。

首先，在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，以添加名为 `PHP_INI_SCAN_DIR` 的应用设置：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` 是 php.ini 所在的默认目录。 `/home/site/ini` 是自定义目录，你将在其中添加自定义 .ini 文件。 使用 `:` 分隔值。

使用 Linux 容器导航到 Web SSH 会话 (`https://<app-name>.scm.azurewebsites.net/webssh/host`)。

在 `/home/site` 中创建名为 `ini` 的目录，然后使用要自定义的指令在 `/home/site/ini` 目录中创建 .ini 文件（例如 settings.ini） 。 使用将在 php.ini 文件中使用的相同语法。 

> [!TIP]
> 在应用服务中的内置 Linux 容器中，/home 用作持久性共享存储。 
>

例如，要更改 [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) 的值，请运行以下命令：

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

需要重启应用才能使更改生效。

## <a name="enable-php-extensions"></a>启用 PHP 扩展

内置 PHP 安装包含最常用的扩展。 可以按照与[自定义 php.ini 指令](#customize-php_ini_system-directives)相同的方式来启用其他扩展。

> [!NOTE]
> 查看 PHP 版本和当前 php.ini 配置的最佳方法是在应用中调用 [phpinfo()](https://php.net/manual/function.phpinfo.php)。
>

若要启用其他扩展，请执行下列步骤：

在应用的根目录中添加 `bin` 目录，并将 `.so` 扩展文件放入其中（例如 mongodb.so）。 确保扩展与 Azure 中的 PHP 版本兼容，并且与 VC9 和非线程安全 (nts) 兼容。

部署所做的更改。

按照[自定义 PHP_INI_SYSTEM 指令](#customize-php_ini_system-directives)中的步骤操作，使用 [extension](https://www.php.net/manual/ini.core.php#ini.extension) 或 [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) 指令将扩展添加到自定义 .ini 文件中。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

需要重启应用才能使更改生效。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑难解答

如果运行中的 PHP 应用在应用服务中的行为不同或有错误，请尝试执行以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下，在本地测试应用。 应用服务在生产模式下运行你的应用，因此你需要确保你的项目在生产模式下以本地方式按预期方式工作。 例如：
    - 根据 composer.json，可以为生产模式安装不同的包（`require` 与 `require-dev`）。
    - 某些 Web 框架可以在生产模式下通过各种方式部署静态文件。
    - 在生产模式下运行时，某些 Web 框架可能会使用自定义的启动脚本。
- 在调试模式下，在应用服务中运行应用。 例如，在 [Laravel](https://meanjs.org/) 中，可以通过[将 `APP_DEBUG` 应用设置设置为 `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)以将应用配置为在生产环境中输出调试消息。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将 PHP 应用与 MySQL 配合使用](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
