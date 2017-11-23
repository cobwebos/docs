---
title: "Azure Web 应用的开放源代码技术常见问题解答 | Microsoft Docs"
description: "获取有关 Azure 应用服务的 Web 应用功能的开放源代码技术常见问题解答。"
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 4fb443691e216169dd1322b96d77139ffde752d4
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Azure 中的 Web 应用的开放源代码技术常见问题解答

本文包含有关针对 [Azure 应用服务的 Web 应用功能](https://azure.microsoft.com/services/app-service/web/)的开放源代码技术问题的常见问题 (FAQ) 解答。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-cleardb-database-is-down-how-do-i-resolve-this"></a>我的 ClearDB 数据库关闭。 如何解决此问题？

有关数据库相关问题，请联系 [ClearDB 支持人员](https://www.cleardb.com/developers/help/support)。 

有关 ClearDB 常见问题解答，请参阅 [ClearDB 常见问题解答](https://docs.microsoft.com/azure/store-cleardb-faq/)。

## <a name="why-wasnt-my-cleardb-database-migrated-during-my-subscription-migration"></a>为何我的 ClearDB 数据库未在订阅迁移过程中进行迁移？

在订阅间迁移资源时，存在某些限制。 ClearDB MySQL 数据库是第三方服务，因而在 Azure 订阅迁移过程中不会进行迁移。

如果在迁移 Azure 资源之前未管理 MySQL 数据库的迁移，则 ClearDB MySQL 数据库可能不可用。 若要避免此问题，请先手动迁移 ClearDB 数据库，然后迁移 Web 应用的 Azure 订阅。

有关详细信息，请参阅 [ClearDB MySQL 数据库与 Azure 应用服务搭配使用时的常见问题解答](https://docs.microsoft.com/azure/store-cleardb-faq/)。

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>如何打开 PHP 日志记录以对 PHP 问题进行故障排除？

打开 PHP 日志记录：

1. 登录到 [Kudu 网站](https://*yourwebsitename*.scm.azurewebsites.net)。
2. 在顶部菜单中，选择“调试控制台” > “CMD”。
3. 选择“站点”文件夹。
4. 选择“wwwroot”文件夹。
5. 选择 **+** 图标，然后选择“新建文件”。
6. 将文件名设置为 **.user.ini**。
7. 选择 **.user.ini** 旁的铅笔图标。
8. 在文件中，添加以下代码：`log_errors=on`
9. 选择“保存”。
10. 选择 **wp-config.php** 旁的铅笔图标。
11. 将文本更改为以下代码：
   ```
   //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
   //Supress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Supress PHP errors to screenini_set('display_errors', 0);
   ```
12. 在 Azure 门户中的 web 应用菜单中，重新启动你的 Web 应用。

有关详细信息，请参阅[启用 WordPress 错误日志](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)。

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>如何在应用服务中承载的应用中记录 Python 应用程序错误？

捕获 Python 应用程序错误：

1. 在 Azure 门户中，在你的 Web 应用中选择“设置”。
2. 在“设置”选项卡上，选择“应用程序设置”。
3. 在“应用设置”下，输入以下键/值对：
    * 键：WSGI_LOG
    * 值：D:\home\site\wwwroot\logs.txt（输入所选文件名）

你现在应在 wwwroot 文件夹中的 logs.txt 文件中看到错误。

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>如何更改应用服务中承载的 Node.js 应用程序的版本？

若要更改 Node.js 应用程序的版本，可以使用以下选项之一：

*   在 Azure 门户中，使用“应用设置”。
    1. 在 Azure 门户中，转到你的 Web 应用。
    2. 在“设置”边栏选项卡中，选择“应用程序设置”。
    3. 在“应用设置”中，可以包含 WEBSITE_NODE_DEFAULT_VERSION 作为键，并将所需 Node.js 版本作为值。
    4. 转到 [Kudu 控制台](https://*yourwebsitename*.scm.azurewebsites.net)。
    5. 若要检查 Node.js 版本，请输入以下命令：  
   ```
   node -v
   ```
*   修改 iisnode.yml 文件。 在 iisnode.yml 文件中更改 Node.js 版本只会设置 iisnode 使用的运行时环境。 Kudu cmd 和其他对象仍使用在 Azure 门户的“应用设置”中设置的 Node.js 版本。

    若要手动设置 iisnode.yml，请在应用根文件夹中创建 iisnode.yml 文件。 在该文件中，包含以下行：
   ```
   nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
   ```
   
*   在源控件部署过程中使用 package.json 设置 iisnode.yml 文件。
    Azure 源控件部署过程涉及以下步骤：
    1. 将内容移动到 Azure Web 应用。
    2. 如果在 Web 应用根文件夹中没有默认部署脚本，则创建一个（deploy.cmd、.deployment 文件）。
    3. 运行部署脚本，如果在 package.json 文件 > 引擎中涉及到 Node.js 版本，则它会创建 iisnode.yml 文件 `"engines": {"node": "5.9.1","npm": "3.7.3"}`
    4. iisnode.yml 文件具有以下代码行：
        ```
        nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
        ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>我在应用服务中承载的 WordPress 应用中看到消息“建立数据库连接时出错”。 如何对此错误进行故障排除？

如果在 Azure WordPress 应用中看到此错误，则若要启用 php_errors.log 和 debug.log，请完成[启用 WordPress 错误日志](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)中详细介绍的步骤。

启用日志后，重现错误，然后检查日志以了解是否耗尽连接：
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

如果在 debug.log 或 php_errors.log 文件中看到此错误，则应用即将超过连接数。 如果在 ClearDB 上承载，请验证[服务计划](https://www.cleardb.com/pricing.view)中可用的连接数。

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>如何调试应用服务中承载的 Node.js 应用？

1.  转到 [Kudu 控制台](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole)。
2.  转到应用程序日志文件夹 (D:\home\LogFiles\Application)。
3.  在 logging_errors.txt 文件中，检查是否存在内容。

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>如何在应用服务 Web 应用或 API 应用中安装本机 Python 模块？

某些程序包可能不在 Azure 中使用 pip 安装。 程序包可能在 Python 程序包索引中不可用，或可能需要编译器（编译器在运行应用服务中的 Web 应用的计算机上不可用）。 有关在应用服务 Web 应用和 API 应用中安装本机模块的信息，请参阅[在应用服务生中安装 Python 模块](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/)。

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>如何使用 Git 和新版本的 Python 将 Django 应用部署到应用服务？

有关安装 Django 的信息，请参阅[将 Django 应用部署到应用服务](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/)。

## <a name="where-are-the-tomcat-log-files-located"></a>Tomcat 日志文件位于何处？

对于 Azure Marketplace 和自定义部署：

* 文件夹位置：D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* 相关文件：
    * catalina.yyyy-mm-dd.log
    * host-manager.yyyy-mm-dd.log
    * localhost.yyyy-mm-dd.log
    * manager.yyyy-mm-dd.log
    * site_access_log.yyyy-mm-dd.log


对于门户“应用设置”部署：

* 文件夹位置：D:\home\LogFiles
* 相关文件：
    * catalina.yyyy-mm-dd.log
    * host-manager.yyyy-mm-dd.log
    * localhost.yyyy-mm-dd.log
    * manager.yyyy-mm-dd.log
    * site_access_log.yyyy-mm-dd.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>如何对 JDBC 驱动程序连接错误进行故障排除？

可能会在 Tomcat 日志中看到以下消息：

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

解决该错误：

1. 从 app/lib 文件夹中删除 sqljdbc*.jar 文件。
2. 如果使用自定义 Tomcat 或 Azure Marketplace Tomcat Web 服务器，请将此 .jar 文件复制到 Tomcat lib 文件夹中。
3. 如果从 Azure 门户启用 Java（选择“Java 1.8” > “Tomcat 服务器”），则在与应用平行的文件夹中复制 sqljdbc.* jar 文件。 然后，将以下类路径设置添加到 web.config 文件：

    ```
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>为何我在尝试复制实时日志文件时看到错误？

如果尝试复制 Java 应用（例如，Tomcat）的实时日志文件，则可能会看到此 FTP 错误：

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

错误消息可能因 FTP 客户端而异。

所有 Java 应用都具有此锁定问题。 只有 Kudu 才支持在应用运行期间下载此文件。

停止应用会使 FTP 可以访问这些文件。

另一个解决方法是编写按计划运行并将这些文件复制到不同目录的 Web 作业。 有关示例项目，请参阅 [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) 项目。

## <a name="where-do-i-find-the-log-files-for-jetty"></a>可在何处找到 Jetty 的日志文件？

对于 Marketplace 和自定义部署，日志文件位于 D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs 文件夹中。 请注意，文件夹位置取决于所使用的 Jetty 版本。 例如，此处提供的路径适用于 Jetty 9.1.2。 查找 jetty_YYYY_MM_DD.stderrout.log。

对于门户应用设置部署，日志文件位于 D:\home\LogFiles 中。 查找 jetty_YYYY_MM_DD.stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>是否可以从 Azure Web 应用发送电子邮件？

应用服务没有内置电子邮件功能。 有关从应用发送电子邮件的一些良好替代方案，请参阅此 [Stack Overflow 讨论](http://stackoverflow.com/questions/17666161/sending-email-from-azure)。

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>为何我的 WordPress 站点会重定向到另一个 URL？

如果最近迁移到 Azure，则 WordPress 可能会重定向到旧的域 URL。 这是由于 MySQL 数据库中的某个设置所导致的。

WordPress Buddy+ 是一个 Azure 站点扩展，可以用于直接在数据库中更新重定向 URL。 有关使用 WordPress Buddy+ 的详细信息，请参阅 [WordPress 工具以及使用 WordPress Buddy+ 进行 MySQL 迁移](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)。

或者，如果希望使用 SQL 查询或 PHPMyAdmin 手动更新重定向 URL，请参阅 [WordPress：重定向到错误 URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/)。

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>如何更改 WordPress 登录密码？

如果忘记了 WordPress 登录密码，则可以使用 WordPress Buddy+ 更新它。 若要重置密码，请安装 WordPress Buddy+ Azure 站点扩展，然后完成 [WordPress 工具以及使用 WordPress Buddy+ 进行 MySQL 迁移](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)中介绍的步骤。

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>我无法登录 WordPress。 如何解决此问题？

如果你发现自己在最近安装某个插件之后被锁定在 WordPress 外部，则可能发生插件故障。 WordPress Buddy+ 是一个 Azure 站点扩展，可以帮助在 WordPress 中禁用插件。 有关使用详细信息，请参阅 [WordPress 工具以及使用 WordPress Buddy+ 进行 MySQL 迁移](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)。

## <a name="how-do-i-migrate-my-wordpress-database"></a>如何迁移 WordPress 数据库？

有多个选项可用于迁移连接到 WordPress 网站的 MySQL 数据库：

* 开发人员：使用[命令提示符或 PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* 非开发人员：使用 [WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>如何帮助使 WordPress 更安全？

若要了解 WordPress 的安全最佳做法，请参阅 [Azure 中的 WordPress 安全最佳做法](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/)。

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>我在尝试使用 PHPMyAdmin，但看到消息“拒绝访问”。 如何解决此问题？

如果 MySQL 应用内功能尚未在此应用服务实例中运行，则你可能会遇到此问题。 若要解决该问题，尝试访问你的网站。 这会启动所需进程，包括 MySQL 应用内进程。 若要验证 MySQL 应用内是否在运行，请在进程资源管理器中，确保 mysqld.exe 在进程中列出。

确保 MySQL 应用内正在运行之后，尝试使用 PHPMyAdmin。

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>我在尝试使用 PHPMyadmin 导入或导出 MySQL 应用内数据库时遇到 HTTP 403 错误。 如何解决此问题？

如果使用较旧版本的 Chrome，则可能会遇到一个已知 bug。 若要解决该问题，请升级到较新版本的 Chrome。 另请尝试使用不会出现该问题的不同浏览器，如 Internet Explorer 或 Microsoft Edge。
