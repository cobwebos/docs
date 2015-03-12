<properties title="如何在 Azure 网站中配置 PHP" pageTitle="如何在 Azure 网站中配置 PHP" metaKeywords="Azure, Azure Websites, configuration, PHP" description="了解如何配置默认 PHP 安装或在 Azure 网站中添加自定义的 PHP 安装。" services=" Websites" documentationCenter="PHP" authors="" />

# 如何在 Azure 网站中配置 PHP

本指南将向你演示如何执行以下操作：在 Azure 网站中配置内置 PHP 运行时，提供自定义 PHP 运行时以及在 Azure 网站中启用扩展。若要使用 Azure 网站，注册以[免费试用]。若要充分利用本指南，你应先在 Azure 网站中创建一个 PHP 站点（请参阅[PHP 开发中心教程]）。有关在 Azure 网站中配置站点的常规信息，请参阅[如何配置网站]。

## 目录

* [什么是 Azure 网站？](#WhatIs)
* [如何：更改默认 PHP 配置](#ChangeBuiltInPHP)
* [如何：启用内置 PHP 运行时中的扩展](#EnableExtDefaultPHP)
* [如何：使用自定义 PHP 运行时](#UseCustomPHP)
* [后续步骤](#NextSteps)

<h2><a name="WhatIs"></a>什么是 Azure 网站？</h2>
Azure 网站允许您在 Azure 上构建高度可扩展的网站。您可以快速轻松地将网站部署到高度可扩展的云环境，这样你便能首先构建小型网站，然后随着流量的增加来扩展网站。Azure 网站使用你所选的语言和开放源应用程序，并支持使用 Git、FTP 和 TFS 进行部署。可以轻松集成其他服务，如 MySQL、SQL Database、Caching、CDN 和存储。

<h2><a name="ChangeBuiltInPHP"></a>如何：更改内置 PHP 配置</h2>
默认情况下，将安装 PHP 5.4 并且在创建 Azure 网站时立即可用。查看可用发行版、其默认配置以及已启用的扩展的最佳方式是部署调用 [phpinfo()] 函数的脚本。

PHP 5.5 也可用，但它在默认情况下不启用。若要启用它，请执行下列步骤：

1. 浏览到 Azure 门户中的网站的仪表板，单击**"配置"(Configure)**。

	![Configure tab on Websites dashboard][configure]

1. 单击"PHP 5.5"。

	![Select PHP version][select-php-version]

1. 单击页面底部的**"保存"(Save)**。

	![Save configuration settings][save-button]

对于任一内置 PHP 运行时，可通过执下列步骤来更改任何不是仅在系统级别使用的指令的配置选项。（有关仅在系统级别使用的指令的信息，请参阅 [php.ini 指令的列表]。）

1. 将 [.user.ini] 文件添加到根目录。
1. 使用您将在 `php.ini` 文件中使用的语法，将配置设置添加到 `.user.ini` 文件。例如，如果您希望打开 `display_errors`设置，并将 `upload_max_filesize`设置设为 10 分钟，您的 `.user.ini`文件将包含此文本：

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. 部署应用程序。
1. 重新启动您的网站。（需要进行重新启动，因为 PHP 读取 `.user.ini` 文件的频率受 `user_ini.cache_ttl` 设置的约束，该设置是一个系统级别设置且默认值为 300 秒（5 分钟）。重新启动站点会强制 PHP 读取 `.user.ini` 文件中的新设置。）

作为使用 `.user.ini` 文件的替代方法，您可以使用脚本中的[ini_set()] 函数来设置不是系统级别指令的配置选项。

<h2><a name="EnableExtDefaultPHP"></a>如何：启用默认 PHP 运行时中的扩展</h2>
如上一节所述，查看默认 PHP 版本、其默认配置以及已启用的扩展的最佳方式是部署调用 [phpinfo()] 的脚本。若要启用其他扩展，请执行下列步骤。

1. 将 `bin` 目录添加到根目录。
1. 将扩展名为 `.dll` 的文件置于 `bin` 目录中（例如， `php_mongo.dll`）。确保扩展与默认版本的 PHP（撰写本文时为 PHP 5.4）兼容，并且是 VC9 版本且与非线程安全 (nts) 兼容。
1. 部署应用程序。
1. 导航到 Azure 门户中的网站的仪表板，并单击**"配置"(Configure)**。

	![Configure tab on Websites dashboard][configure]

1. 在**"应用程序设置"(app settings)** 部分，创建密钥 **PHP_EXTENSIONS** 和值 **bin\your-ext-file**。若要启用多个扩展，请包括 `.dll` 文件的逗号分隔的列表。

	![Enable extension in app settings][app-settings]

1. 单击页面底部的**"保存"(Save)**。

	![Save configuration settings][save-button]

<h2><a name="UseCustomPHP"></a>如何：使用自定义 PHP 运行时</h2>
Azure 网站可以使用提供的 PHP 运行时（而非默认 PHP 运行时）来执行 PHP 脚本。提供的运行时可由提供的 `php.ini` 文件配置。若要在 Azure 网站中使用自定义 PHP 运行时，请执行下列步骤。

1. 获取非线程安全、VC9 兼容版本的 PHP for Windows。可在此处找到 PHP for Windows 最新版本：[http://windows.php.net/download/]。可在此处的存档中找到旧版本：[http://windows.php.net/downloads/releases/archives/]。
1. 修改运行时的 `php.ini` 文件。请注意，Azure 网站将忽略作为任何仅在系统级别使用的指令的配置设置。（有关仅在系统级别使用的指令的信息，请参阅 [php.ini 指令的列表]。）
1. （可选）将扩展添加到 PHP 运行时并在 `php.ini` 文件中启用这些扩展。
1. 将 `bin` 目录添加到根目录，并将包含 PHP 运行时的目录置于根目录中（例如， `bin\php`）。
1. 部署应用程序。
1. 导航到 Azure 门户中的网站的仪表板，并单击**"配置"(Configure)**。

	![Configure tab on Websites dashboard][configure]

1. 在**处理程序映射**部分中，将`*.php`添加到 EXTENSION，并添加指向 `php-cgi.exe`可执行文件的路径。如果您将 PHP 运行时放在应用程序的根目录中的 `bin` 目录下，路径将为 `D:\home\site\wwwroot\bin\php\php-cgi.exe`。

	![Specify handler in hander mappings][handler-mappings]

1. 单击页面底部的**"保存"(Save)**。

	![Save configuration settings][save-button]

<h2><a name="NextSteps"></a>后续步骤</h2>
现在，你已了解如何在 Azure 网站中配置 PHP，访问下列链接可了解如何执行更多操作。

- [在 Azure 中配置、监视和扩展网站]
- [下载 Azure SDK for PHP]


[免费试用]: /zh-cn/pricing/1rmb-trial/
[PHP 开发中心教程]: /zh-cn/develop/php/
[如何配置网站]: /zh-cn/documentation/articles/web-sites-configure/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Php.ini 指令的列表]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[configure]: ./media/web-sites-php-configure/configure.png
[app-settings]: ./media/web-sites-php-configure/app-settings.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[在 Azure 中配置、监视和扩展网站]: /zh-cn/documentation/services/web-sites
[下载 Azure SDK for PHP]: /zh-cn/downloads/?sdk=php
