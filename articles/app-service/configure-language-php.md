---
title: 配置 PHP 应用
description: 了解如何在原生 Windows 实例、预构建的 PHP 容器或 Azure 应用服务中配置 PHP 应用。 本文介绍最常见的配置任务。
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: afac8273b5729bcf5470be471145214426dc7dab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90055293"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>为 Azure 应用服务配置 PHP 应用

本指南介绍了如何在 Azure 应用服务中配置 PHP Web 应用、移动后端和 API 应用。

本指南为在应用服务中部署应用的 PHP 开发人员提供了重要概念和说明。 若从未使用过 Azure 应用服务，则首先应按照 [PHP 快速入门](quickstart-php.md)以及[将 PHP 与 MySQL 配合使用教程](tutorial-php-mysql-app.md)进行操作。

## <a name="show-php-version"></a>显示 PHP 版本

::: zone pivot="platform-windows"  

要显示当前的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

要显示所有受支持的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

要显示当前的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

要显示所有受支持的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>设置 PHP 版本

::: zone pivot="platform-windows"  

在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，将 PHP 版本设置为7.4：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，将 PHP 版本设置为 7.2：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>运行编辑器

如果你希望应用服务在部署时运行[编辑器](https://getcomposer.org/)，最简单的方式是在你的存储库中包括编辑器。

在本地终端窗口中，将目录更改为你的存储库根目录，并按照[下载编辑器](https://getcomposer.org/download/)中的说明将 composer.phar 下载到目录根目录。

运行以下命令（需要安装 [npm](https://www.npmjs.com/get-npm)）：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

你的存储库根目录中现在有两个额外的文件：.deployment 和 deploy.sh。

打开 deploy.sh 并找到 `Deployment` 节，该节如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

在 `Deployment` 节的末尾添加运行必需工具所需的代码节：

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

提交所有更改，并在启用了生成自动化的情况下使用 Git 或 Zip 部署来部署你的代码。 编辑器现在应作为部署自动化的一部分运行。

## <a name="run-gruntbowergulp"></a>运行 Grunt/Bower/Gulp

如果你希望应用服务在部署时运行常用的自动化工具（例如 Grunt、Bower 或 Gulp），则你需要提供[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。 当你在启用了生成自动化的情况下通过 Git 或 [Zip 部署](deploy-zip.md)进行部署时，应用服务会运行此脚本。 

若要使你的存储库能够运行这些工具，需要将它们添加到 package.json 中的依赖项。 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

在本地终端窗口中，将目录更改到你的存储库根目录，并运行以下命令（你需要安装 [npm](https://www.npmjs.com/get-npm)）：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

你的存储库根目录中现在有两个额外的文件：.deployment 和 deploy.sh。

打开 deploy.sh 并找到 `Deployment` 节，该节如下所示：

```bash
##################################################################################################################################
# Deployment
# ----------
```

该节在末尾处运行 `npm install --production`。 在 `Deployment` 节的末尾添加运行必需工具所需的代码节：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

请参阅 [MEAN.js 示例中的示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，其中的部署脚本也运行自定义 `npm install` 命令。

### <a name="bower"></a>Bower

此代码片段运行 `bower install`。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此代码片段运行 `gulp imagemin`。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此代码片段运行 `grunt`。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

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

::: zone-end

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](configure-common.md#configure-app-settings)。 然后，可以使用标准的 [getenv()](https://secure.php.net/manual/function.getenv.php) 模式访问这些设置。 例如，若要访问名为 `DB_HOST` 的应用设置，请使用以下代码：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>更改站点根路径

::: zone pivot="platform-windows"  

所选的 Web 框架可能使用子目录作为站点根路径。 例如，[Laravel](https://laravel.com/) 使用 public/ 子目录作为站点根路径。

若要自定义站点根路径，请使用 [`az resource update`](/cli/azure/resource#az-resource-update) 命令设置应用的虚拟应用程序路径。 下面的示例将站点根路径设置为存储库中的 public/ 子目录。 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

默认情况下，Azure 应用服务将根虚拟应用程序路径 (/) 指向已部署的应用程序的文件的根目录 (sites\wwwroot)。

::: zone-end

::: zone pivot="platform-linux"

所选的 Web 框架可能使用子目录作为站点根路径。 例如，[Laravel](https://laravel.com/) 使用 `public/` 子目录作为站点根路径。

应用服务的默认 PHP 映像使用 Apache，不允许为应用自定义站点根路径。 若要避开此限制，请将 .htaccess 文件添加到存储库根路径，并包含以下内容：

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

如果不希望使用 *.htaccess* 重写，可以改用[自定义 Docker 映像](quickstart-custom-container.md)来部署 Laravel 应用程序。

::: zone-end

## <a name="detect-https-session"></a>检测 HTTPS 会话

在应用服务中，[SSL 终止](https://wikipedia.org/wiki/TLS_termination_proxy)在网络负载均衡器上发生，因此，所有 HTTPS 请求将以未加密的 HTTP 请求形式访问你的应用。 如果应用逻辑需要检查用户请求是否已加密，可以检查 `X-Forwarded-Proto` 标头。

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
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

::: zone pivot="platform-windows"  

若要自定义 PHP_INI_USER、PHP_INI_PERDIR 和 PHP_INI_ALL 指令（请参阅 [php.ini 指令](https://www.php.net/manual/ini.list.php)），请将 `.user.ini` 文件添加到应用的根目录中。

使用会在 `php.ini` 文件中使用的语法，将配置设置添加到 `.user.ini` 文件。 例如，如果希望启用 `display_errors` 设置，并将 `upload_max_filesize` 设置设为 10 分钟，则 `.user.ini` 文件应包含以下文本：

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

通过更改重新部署应用，然后重启该应用。

除了使用 `.user.ini` 文件之外，还可以在应用中使用 [ini_set()](https://www.php.net/manual/function.ini-set.php) 来自定义这些非 PHP_INI_SYSTEM 指令。

::: zone-end

::: zone pivot="platform-linux"

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

通过更改重新部署应用，然后重启该应用。 如果使用 Kudu（例如，使用 [Git](deploy-local-git.md)）部署应用，则该应用会在部署后自动重启。

作为使用 .htaccess 的替代方法，可以在应用中使用 [ini_set()](https://www.php.net/manual/function.ini-set.php) 来自定义这些非 PHP_INI_SYSTEM 指令。

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自定义 PHP_INI_SYSTEM 指令

::: zone pivot="platform-windows"  

不能使用 .htaccess 方法自定义 PHP_INI_SYSTEM 指令（请参阅 [php.ini 指令](https://www.php.net/manual/ini.list.php)）。 应用服务使用 `PHP_INI_SCAN_DIR` 应用设置提供了一种单独的机制。

首先，在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，以添加名为 `PHP_INI_SCAN_DIR` 的应用设置：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

导航到 Kudu 控制台 (`https://<app-name>.scm.azurewebsites.net/DebugConsole`)，然后导航到 `d:\home\site`。

在 `d:\home\site` 中创建名为 `ini` 的目录，然后使用要自定义的指令在 `d:\home\site\ini` 目录中创建 .ini 文件（例如 settings.ini） 。 使用将在 php.ini 文件中使用的相同语法。 

例如，要更改 [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) 的值，请运行以下命令：

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

需要重启应用才能使更改生效。

::: zone-end

::: zone pivot="platform-linux"

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

::: zone-end

## <a name="enable-php-extensions"></a>启用 PHP 扩展

::: zone pivot="platform-windows"  

内置 PHP 安装包含最常用的扩展。 可以按照与[自定义 php.ini 指令](#customize-php_ini_system-directives)相同的方式来启用其他扩展。

> [!NOTE]
> 查看 PHP 版本和当前 php.ini 配置的最佳方法是在应用中调用 [phpinfo()](https://php.net/manual/function.phpinfo.php)。
>

若要启用其他扩展，请执行下列步骤：

将 `bin` 目录添加到应用的根目录，并将 `.dll` 扩展文件放入 (例如 *mongodb.dll*) 。 确保扩展与 Azure 中的 PHP 版本兼容，并且与 VC9 和非线程安全 (nts) 兼容。

部署所做的更改。

按照[自定义 PHP_INI_SYSTEM 指令](#customize-php_ini_system-directives)中的步骤操作，使用 [extension](https://www.php.net/manual/ini.core.php#ini.extension) 或 [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) 指令将扩展添加到自定义 .ini 文件中。

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

需要重启应用才能使更改生效。

::: zone-end

::: zone pivot="platform-linux"

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

::: zone-end

## <a name="access-diagnostic-logs"></a>访问诊断日志

::: zone pivot="platform-windows"  

使用标准 [error_log()](https://php.net/manual/function.error-log.php) 实用工具使诊断日志显示在 Azure 应用服务中。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>疑难解答

如果运行中的 PHP 应用在应用服务中的行为不同或有错误，请尝试执行以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下，在本地测试应用。 应用服务在生产模式下运行你的应用，因此你需要确保项目在生产模式下按预期在本地运行。 例如：
    - 根据 composer.json，可以为生产模式安装不同的包（`require` 与 `require-dev`）。
    - 某些 Web 框架可以在生产模式下通过各种方式部署静态文件。
    - 在生产模式下运行时，某些 Web 框架可能会使用自定义的启动脚本。
- 在调试模式下，在应用服务中运行应用。 例如，在 [Laravel](https://meanjs.org/) 中，可以通过[将 `APP_DEBUG` 应用设置设置为 `true`](configure-common.md#configure-app-settings)以将应用配置为在生产环境中输出调试消息。

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将 PHP 应用与 MySQL 配合使用](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [应用服务 Linux 常见问题解答](faq-app-service-linux.md)

::: zone-end

