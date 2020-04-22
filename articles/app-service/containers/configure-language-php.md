---
title: 配置 PHP 应用
description: 了解如何为应用配置预构建的 PHP 容器。 本文介绍最常见的配置任务。
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758876"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>为 Azure 应用服务配置 Linux PHP 应用

本指南介绍如何在 Azure 应用服务中为 Web 应用、移动后端和 API 应用配置内置 PHP 运行时。

本指南为在应用服务中使用内置 Linux 容器的 PHP 开发人员提供了关键概念和说明。 如果您从未使用过 Azure 应用服务，请先使用 MySQL 教程，请先按照[PHP 快速入门](quickstart-php.md)和 PHP 进行[MySQL 教程](tutorial-php-mysql-app.md)。

## <a name="show-php-version"></a>显示 PHP 版本

要显示当前的 PHP 版本，在[云壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要显示所有受支持的 PHP 版本，可在[云外壳](https://shell.azure.com)中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>设置 PHP 版本

在[云壳](https://shell.azure.com)中运行以下命令，将 PHP 版本设置为 7.2：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>自定义构建自动化

如果使用打开的 Git 或 zip 包部署应用，应用服务将构建自动化步骤，执行以下顺序：

1. 如果由`PRE_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。
1. 运行 `php composer.phar install`。
1. 如果由`POST_BUILD_SCRIPT_PATH`指定，则运行自定义脚本。

`PRE_BUILD_COMMAND`和`POST_BUILD_COMMAND`是默认情况下为空的环境变量。 要运行预生成命令，请定义`PRE_BUILD_COMMAND`。 要运行生成后命令，请定义`POST_BUILD_COMMAND`。

下面的示例指定一系列命令的两个变量，这些命令用逗号分隔。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

有关自定义生成自动化的其他环境变量，请参阅[Oryx 配置](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)。

有关应用服务如何在 Linux 中运行和构建 PHP 应用程序的详细信息，请参阅[Oryx 文档：如何检测和构建 PHP 应用程序](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)。

## <a name="customize-start-up"></a>自定义启动

默认情况下，内置 PHP 容器运行 Apache 服务器。 在启动时，它运行`apache2ctl -D FOREGROUND"`。 如果您愿意，可以通过在[云壳](https://shell.azure.com)中运行以下命令，在启动时运行不同的命令：

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)。 然后，您可以使用标准[getenv（）](https://secure.php.net/manual/function.getenv.php)模式访问它们。 例如，若要访问名为 `DB_HOST` 的应用设置，请使用以下代码：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>更改站点根

您选择的 Web 框架可能使用子目录作为站点根目录。 例如[，Laravel](https://laravel.com/)使用`public/`子目录作为站点根目录。

应用服务的默认 PHP 映像使用 Apache，它不允许您自定义应用的网站根。 要解决此限制，请向存储库根添加包含以下内容的 *.htaccess*文件：

```
<IfModule mod_rewrite.c>
    RewriteEngine on

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

如果需要更改 PHP 安装，可以通过执行以下步骤更改任何[php.ini 指令](https://www.php.net/manual/ini.list.php)。

> [!NOTE]
> 查看 PHP 版本和当前*php.ini*配置的最佳方法是在应用程序中调用[phpinfo（）。](https://php.net/manual/function.phpinfo.php)
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>自定义非PHP_INI_SYSTEM指令

要自定义PHP_INI_USER、PHP_INI_PERDIR和PHP_INI_ALL指令（请参阅[php.ini 指令](https://www.php.net/manual/ini.list.php)），请向应用的根目录添加 *.htaccess*文件。

在 *.htaccess*文件中，使用`php_value <directive-name> <value>`语法添加指令。 例如：

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

使用更改重新部署应用并重新启动它。 如果使用 Kudu 部署它（例如，使用[Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)），则部署后会自动重新启动它。

作为使用 *.htaccess*的替代方法，您可以在应用中使用[ini_set（）](https://www.php.net/manual/function.ini-set.php)来自定义这些非PHP_INI_SYSTEM指令。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自定义PHP_INI_SYSTEM指令

要自定义PHP_INI_SYSTEM指令（请参阅[php.ini 指令](https://www.php.net/manual/ini.list.php)），不能使用 *.htaccess*方法。 应用服务使用`PHP_INI_SCAN_DIR`应用设置提供单独的机制。

首先，在[云壳](https://shell.azure.com)中运行以下命令以添加名为`PHP_INI_SCAN_DIR`的应用设置：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`是*存在 php.ini 的*默认目录。 `/home/site/ini`是在其中添加自定义 *.ini*文件的自定义目录。 使用 分隔值`:`。

使用 Linux 容器 （导航`https://<app-name>.scm.azurewebsites.net/webssh/host`到 Web SSH 会话）。

在`/home/site`中`ini`创建目录，然后在`/home/site/ini`目录中创建 *.ini*文件（例如，*设置.ini），* 该目录包含要自定义的指令。 使用在*php.ini*文件中使用的语法。 

> [!TIP]
> 在应用服务中的内置 Linux 容器中 *，/home*用作持久共享存储。 
>

例如，要更改[expose_php](https://php.net/manual/ini.core.php#ini.expose-php)运行以下命令的值：

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

要使更改生效，请重新启动应用。

## <a name="enable-php-extensions"></a>启用 PHP 扩展

内置 PHP 安装包含最常用的扩展。 您可以启用其他扩展的方式与[自定义 php.ini 指令](#customize-php_ini_system-directives)相同。

> [!NOTE]
> 查看 PHP 版本和当前*php.ini*配置的最佳方法是在应用程序中调用[phpinfo（）。](https://php.net/manual/function.phpinfo.php)
>

若要启用其他扩展，请执行下列步骤：

将`bin`目录添加到应用的根目录，并将`.so`扩展文件放入其中（例如 *，mongodb.so*）。 确保扩展与 Azure 中的 PHP 版本兼容，并且与 VC9 和非线程安全 （nts） 兼容。

部署更改。

按照[自定义PHP_INI_SYSTEM指令](#customize-php_ini_system-directives)中的步骤，将扩展添加到自定义 *.ini*文件中，并[带有扩展名](https://www.php.net/manual/ini.core.php#ini.extension)或[zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension)指令。

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

要使更改生效，请重新启动应用。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>在浏览器中打开 SSH 会话

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>疑难解答

当工作中的 PHP 应用在应用服务中的行为不同或有错误时，请尝试以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下本地测试应用。 应用服务在生产模式下运行 Node.js 应用，因此您需要确保项目在本地生产模式下按预期工作。 例如：
    - 根据您的*作曲家.json，* 不同的软件包可能安装生产模式（`require`与 ）。 `require-dev`
    - 某些 Web 框架在生产模式下可能以不同的方式部署静态文件。
    - 某些 Web 框架在生产模式下运行时可能会使用自定义启动脚本。
- 在调试模式下在应用服务中运行应用。 例如，在[Laravel](https://meanjs.org/)中，可以通过[将`APP_DEBUG``true`应用设置设置为](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)（） 来配置应用以在生产中输出调试消息。

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将 PHP 应用与 MySQL 配合使用](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](app-service-linux-faq.md)
