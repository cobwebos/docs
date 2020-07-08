---
title: 配置 Windows PHP 应用程序
description: 了解如何在应用服务的本机 Windows 实例中配置 PHP 应用。 本文介绍最常见的配置任务。
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1eb4e9d349fdd0097cbde4e4cef3d5c61a167193
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907869"
---
# <a name="configure-a-windows-php-app-for-azure-app-service"></a>为 Azure App Service 配置 Windows PHP 应用程序

本指南演示如何在 Azure App Service 中配置 PHP web 应用、移动后端和 API 应用。 本指南适用于在 Windows 平台上托管的应用。 有关 linux 应用的信息，请参阅为[Azure App Service 配置 LINUX PHP 应用](containers/configure-language-php.md)。

本指南为向应用服务部署应用的 PHP 开发人员提供重要的概念和说明。 若从未使用过 Azure 应用服务，则首先应按照 [PHP 快速入门](app-service-web-get-started-php.md)以及[将 PHP 与 MySQL 配合使用教程](app-service-web-tutorial-php-mysql.md)进行操作。

## <a name="show-php-version"></a>显示 PHP 版本

要显示当前的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

要显示所有受支持的 PHP 版本，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp list-runtimes | grep php
```

## <a name="set-php-version"></a>设置 PHP 版本

在[Cloud Shell](https://shell.azure.com)中运行以下命令，将 PHP 版本设置为7.4：

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

## <a name="run-composer"></a>运行编辑器

如果希望应用服务在部署时运行[编辑器](https://getcomposer.org/)，最简单的方法是将编辑器包含在存储库中。

在本地终端窗口中，将目录更改为存储库根目录，然后按照[下载书写器](https://getcomposer.org/download/)中的说明将*composer.phar*下载到目录根目录。

运行以下命令（需要安装[npm](https://www.npmjs.com/get-npm) ）：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

存储库根现在包含两个附加文件： *. deployment*和*deploy.sh*。

打开*deploy.sh*并找到如下所 `Deployment` 示的部分：

```bash
##################################################################################################################################
# Deployment
# ----------
```

添加代码部分，需要*在节末尾*运行所需的工具 `Deployment` ：

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

提交所有更改并使用 Git 部署代码，或启用生成自动化的 Zip 部署。 书写器现在应作为部署自动化的一部分运行。

## <a name="run-gruntbowergulp"></a>运行 Grunt/Bower/Gulp

如果希望应用服务在部署时运行常用的自动化工具，如 Grunt、Bower 或 Gulp，则需要提供[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。 当你通过 Git 进行部署或启用了生成自动化的[Zip 部署](deploy-zip.md)时，应用服务将运行此脚本。 

若要使存储库能够运行这些工具，需要将它们添加到*package.js上*的依赖项。 例如：

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

在本地终端窗口中，将目录更改为存储库根，并运行以下命令（需要安装[npm](https://www.npmjs.com/get-npm) ）：

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

存储库根现在包含两个附加文件： *. deployment*和*deploy.sh*。

打开*deploy.sh*并找到如下所 `Deployment` 示的部分：

```bash
##################################################################################################################################
# Deployment
# ----------
```

此部分结束于运行 `npm install --production` 。 添加代码部分，需要*在节末尾*运行所需的工具 `Deployment` ：

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

请参阅[MEAN.js 示例中的示例](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)，在该示例中，部署脚本也运行自定义 `npm install` 命令。

### <a name="bower"></a>Bower

此代码段将运行 `bower install` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

此代码段将运行 `gulp imagemin` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

此代码段将运行 `grunt` 。

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="access-environment-variables"></a>访问环境变量

在应用服务中，可以在应用代码外部[设置应用设置](configure-common.md#configure-app-settings)。 然后，可以使用标准的 [getenv()](https://secure.php.net/manual/function.getenv.php) 模式访问这些设置。 例如，若要访问名为 `DB_HOST` 的应用设置，请使用以下代码：

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>更改站点根路径

所选的 Web 框架可能使用子目录作为站点根路径。 例如， [Laravel](https://laravel.com/)使用*公共/* 子目录作为网站根目录。

若要自定义站点根，请使用命令设置应用的虚拟应用程序路径 [`az resource update`](/cli/azure/resource#az-resource-update) 。 下面的示例将站点根目录设置为存储库中的*公共/* 子目录。 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

默认情况下，Azure 应用服务将根虚拟应用程序路径 (/) 指向已部署的应用程序的文件的根目录 (sites\wwwroot)。

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

若要自定义 PHP_INI_USER、PHP_INI_PERDIR 和 PHP_INI_ALL 指令（请参阅[php.ini 指令](https://www.php.net/manual/ini.list.php)），请将 `.user.ini` 文件添加到应用程序的根目录中。

使用会在 `php.ini` 文件中使用的语法，将配置设置添加到 `.user.ini` 文件。 例如，如果希望启用 `display_errors` 设置，并将 `upload_max_filesize` 设置设为 10 分钟，则 `.user.ini` 文件应包含以下文本：

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

通过更改重新部署应用，然后重启该应用。

作为使用文件的替代方法 `.user.ini` ，你可以使用应用中的[ini_set （）](https://www.php.net/manual/function.ini-set.php)自定义这些非 PHP_INI_SYSTEM 指令。

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>自定义 PHP_INI_SYSTEM 指令

不能使用 .htaccess 方法自定义 PHP_INI_SYSTEM 指令（请参阅 [php.ini 指令](https://www.php.net/manual/ini.list.php)）。 应用服务使用 `PHP_INI_SCAN_DIR` 应用设置提供了一种单独的机制。

首先，在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，以添加名为 `PHP_INI_SCAN_DIR` 的应用设置：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

导航到 Kudu 控制台（ `https://<app-name>.scm.azurewebsites.net/DebugConsole` ），并导航到 `d:\home\site` 。

在 `d:\home\site` 中创建名为 `ini` 的目录，然后使用要自定义的指令在 `d:\home\site\ini` 目录中创建 .ini 文件（例如 settings.ini） 。 使用将在 php.ini 文件中使用的相同语法。 

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

```
extension=d:\home\site\wwwroot\bin\mongodb.so
zend_extension=d:\home\site\wwwroot\bin\xdebug.so
```

需要重启应用才能使更改生效。

## <a name="access-diagnostic-logs"></a>访问诊断日志

使用标准[error_log （）](https://php.net/manual/function.error-log.php)实用工具使诊断日志显示在 Azure App Service 中。

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>疑难解答

如果运行中的 PHP 应用在应用服务中的行为不同或有错误，请尝试执行以下操作：

- [访问日志流](#access-diagnostic-logs)。
- 在生产模式下，在本地测试应用。 应用服务在生产模式下运行你的应用，因此你需要确保你的项目在生产模式下以本地方式按预期方式工作。 例如：
    - 某些 Web 框架可以在生产模式下通过各种方式部署静态文件。
    - 在生产模式下运行时，某些 Web 框架可能会使用自定义的启动脚本。
- 在调试模式下，在应用服务中运行应用。 例如，在 [Laravel](https://meanjs.org/) 中，可以通过[将 `APP_DEBUG` 应用设置设置为 `true`](configure-common.md#configure-app-settings)以将应用配置为在生产环境中输出调试消息。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将 PHP 应用与 MySQL 配合使用](app-service-web-tutorial-php-mysql.md)
