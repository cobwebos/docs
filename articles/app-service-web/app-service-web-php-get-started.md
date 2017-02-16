---
title: "创建、配置 PHP Web 应用，并将其部署到 Azure | Microsoft Docs"
description: "演示如何创建在 Azure App Service 中运行的 PHP (Laravel) Web 应用的教程。 了解如何配置 Azure App Service 以满足所选择的 PHP 框架的要求。"
services: app-service\web
documentationcenter: php
author: cephalin
manager: wpickett
editor: 
tags: mysql
ms.assetid: cb73859d-48aa-470a-b486-d984746d6d26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: cddcfcc816a2af74fbdd68e4e1031051c42b45a7


---
# <a name="create-configure-and-deploy-a-php-web-app-to-azure"></a>创建、配置 PHP Web 应用，并将其部署到 Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

本教程演示了如何为 Azure 创建、配置和部署 PHP Web 应用，以及如何配置 Azure App Service 以满足 PHP Web 应用的要求。 在本教程结束时，将在 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)中实时运行一个工作 [Laravel](https://www.laravel.com/) Web 应用。

作为 PHP 开发人员，你可在 Azure 中引入你最喜欢的 PHP 框架。 本教程只是将 Laravel 用作具体的应用示例。 你将学习以下内容： 

* 使用 Git 进行部署
* 设置 PHP 版本
* 使用不在根应用程序目录中的启动文件
* 访问特定于环境的变量
* 在 Azure 中更新应用程序

你可以将在此处了解的知识应用到部署到 Azure 的其他 PHP Web Apps。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](app-service-web-php-get-started-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI
- [Azure CLI 2.0（预览版）](app-service-web-php-get-started.md)- 适用于资源管理部署模型的下一代 CLI

## <a name="prerequisites"></a>先决条件
* [PHP 7.0](http://php.net/downloads.php)
* [编辑器](https://getcomposer.org/download/)
* [Azure CLI 2.0 预览版](/cli/azure/install-az-cli2)
* [Git](http://www.git-scm.com/downloads)
* 一个 Microsoft Azure 帐户。 如果没有帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 查看实际应用中的 Web 应用。 立即[试用应用服务](https://azure.microsoft.com/try/app-service/)，创建短期的入门级应用 — 无需信用卡，也无需做出任何承诺。
> 
> 

## <a name="create-a-php-laravel-app-on-your-dev-machine"></a>在你的开发计算机上创建一个 PHP (Laravel) 应用
1. 打开新的 Windows 命令提示符、PowerShell 窗口、Linux shell 或 OS X 终端。 运行以下命令以验证是否在你的计算机上正确安装了所需工具。 
   
        php --version
        composer --version
        az --version
        git --version
   
    如果尚未安装这些工具，请参阅[先决条件](#Prerequisites)中的下载链接。

2. 安装 Laravel，如下：
   
        composer global require "laravel/installer"

3. 使用 `CD` 命令切换到工作目录并创建新的 Laravel 应用程序，如下：
   
        cd <working_directory>
        laravel new <app_name>
4. 使用 `CD` 命令切换到新创建的 `<app_name>` 目录并测试该应用程序，如下：
   
        cd <app_name>
        php artisan serve
   
    现在可以在浏览器中导航到 http://localhost:8000，查看 Laravel 初始屏幕。
   
    ![在将应用部署到 Azure 之前在本地测试你的 PHP (Laravel) 应用](./media/app-service-web-php-get-started/laravel-splash-screen.png)

1. 初始化 Git 存储库并提交所有代码：

        git init
        git add .
        git commit -m "Hurray! My first commit for my Azure web app!"

到目前为止，我们只介绍了常规的 Laravel 和 Git 工作流，尚未<a href="https://laravel.com/docs/5.3" rel="nofollow">了解 Laravel</a>。 因此，让我们继续以下章节的讲解。

## <a name="create-an-azure-web-app-and-set-up-git-deployment"></a>创建 Azure Web 应用并设置 Git 部署
> [!NOTE]
> “稍等！ 如果我想要使用 FTP 进行部署该怎么做？” 请根据需要参阅 [FTP 教程](web-sites-php-mysql-deploy-use-ftp.md)。 
> 
> 

借助 Azure CLI，可以使用单行命令在 Azure App Service 中创建 Web 应用并针对 Git 部署对其进行设置。 让我们执行此操作。

1. 如下所示登录 Azure ：
   
        az login
   
    按照帮助消息的提示继续此登录过程。
   
3. 设置应用服务的部署用户。 稍后将使用这些凭据部署代码。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 创建新[资源组](../azure-resource-manager/resource-group-overview.md)。 对于此 PHP 教程，实际上并不需要知道什么是资源组。

        az group create --location "<location>" --name my-php-app-group

    若要查看可为 `<location>` 使用的可能值，请使用 `az appservice list-locations` CLI 命令。

3. 创建新的“免费”[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 对于此 PHP 教程，只需知道此计划中的 Web 应用不会产生费用。

        az appservice plan create --name my-php-appservice-plan --resource-group my-php-app-group --sku FREE

4. 在 `<app_name>` 中创建使用唯一名称的新 Web 应用。

        az appservice web create --name <app_name> --resource-group my-php-app-group --plan my-php-appservice-plan

5. 使用以下命令为新的 Web 应用配置本地 Git 部署：

        az appservice web source-control config-local-git --name <app_name> --resource-group my-php-app-group

    将返回类似于下面的 JSON 输出，这表示已配置远程 Git 存储库：

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. 在 JSON 中添加该 URL 作为本地存储库（简称 `azure`）的 Git 远程地址。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
<a name="configure"/>

## <a name="configure-the-azure-web-app"></a>配置 Azure Web 应用
若要在 Azure 中使用 Laravel 应用，你需要注意以下几件事。 你将对你选择的 PHP 框架做此类似的练习。

* 配置 PHP 5.6.4 或更高版本。 请参阅[最新的 Laravel 5.3 服务器要求](https://laravel.com/docs/5.3#server-requirements)，以获取服务器要求的完整列表。 列表的其余部分是 Azure 的 PHP 安装已启用的扩展。 
* 设置你的应用程序所需要的环境变量。 Laravel 使用 `.env` 文件来简单设置环境变量。 但是，由于不建议将该文件提交到源控件（请参阅 [Laravel Environment Configuration](https://laravel.com/docs/5.3/configuration#environment-configuration)（Laravel 环境配置）），将改为设置 Azure Web 应用的应用设置。
* 请确保首先加载 Laravel 应用的入口点 `public/index.php`。 请参阅 [Laravel Lifecycle Overview](https://laravel.com/docs/5.3/lifecycle#lifecycle-overview)（Laravel 生命周期概述）。 换而言之，需要设置指向 `public` 目录的 Web 应用的根 URL。
* 由于已具有 composer.json，因此可以在 Azure 中启用 Composer 扩展。 这样，在使用 `git push` 进行部署时可以让 Composer 负责获取所需的程序包。 这是一个有关便利性的问题。 
  如果未启用 Composer 自动化，只需从 `.gitignore` 文件中删除 `/vendor`，以便在提交和部署代码时 Git 包括（“un-ignores”）`vendor` 目录中的所有内容。

让我们按顺序配置这些任务。

1. 设置 Laravel 应用需要的 PHP 版本。
   
        az appservice web config update --php-version 7.0 --name <app_name> --resource-group my-php-app-group
   
    你完成了 PHP 版本的设置！ 

2. 为 Azure Web 应用生成新的 `APP_KEY`，并针对 Azure Web 应用将其设置为一个应用程序设置。
   
        php artisan key:generate --show
        az appservice web config appsettings update --settings APP_KEY="<output_of_php_artisan_key:generate_--show>" --name <app_name> --resource-group my-php-app-group

3. 此外，启用 Laravel 调试功能以解决任何含义模糊的错误消息 `Whoops, looks like something went wrong.` 页面。
   
        az appservice web config appsettings update --settings APP_DEBUG=true --name <app_name> --resource-group my-php-app-group
   
    你已完成环境变量的设置！
   
   > [!NOTE]
   > 请稍等，让我们停下来解释一下此处 Laravel 和 Azure 进行了哪些操作。 Laravel 使用根目录中的 `.env` 文件向应用提供环境变量，可以在该文件中找到行 `APP_DEBUG=true`（以及 `APP_KEY=...`）。 通过代码 `'debug' => env('APP_DEBUG', false),` 可在 `config/app.php` 中访问该变量。 [env()](https://laravel.com/docs/5.3/helpers#method-env) 是使用底层的 PHP [getenv()](http://php.net/manual/en/function.getenv.php) 函数的 Laravel helper 方法。
   > 
   > 但是，Git 忽略了 `.env` 文件，因为根目录中的 `.gitignore` 文件列出了该文件。 简而言之，本地 Git 存储库中的 `.env` 未和文件的其余部分一起推送到 Azure。 当然，可以从 `.gitignore` 中移除该行，但是我们已经确定了不建议将此文件提交到源控件。 不过，你仍需要一种方法在 Azure 中指定这些环境变量。 
   > 
   > 好消息是 Azure App Service 中的应用设置支持 PHP 中的 [getenv()](http://php.net/manual/en/function.getenv.php)。 因此，当使用 FTP 或其他方法将 `.env` 文件手动上载到 Azure 时，只需将所需变量指定为 Azure 应用程序设置，而无需在 Azure 中使用 `.env` 文件，正如你在前面所做的。 而且，如果变量同时存在于 `.env` 文件和 Azure 应用程序设置中，则 Azure 应用程序设置具有更高优先级。     
   > 
   > 
4. 最后两个任务（设置虚拟目录和启用 Composer）需要使用 [Azure 门户](https://portal.azure.com)，因此请使用 Azure 帐户登录该[门户](https://portal.azure.com)。
5. 在左侧菜单中，单击“应用程序服务” > “&lt;app_name>” > “扩展”。
   
    ![在 Azure 中为 PHP (Laravel) 应用启用 Composer](./media/app-service-web-php-get-started/configure-composer-tools.png)
   
6. 单击“添加”，添加扩展。
7. 在“选择扩展”[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)中选择“Composer”（*边栏选项卡*：水平打开的门户页）。
8. 在“接受法律条款”[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)中单击“确定”。 
9. 在“添加扩展”[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)中单击“确定”。
   
    当 Azure 完成添加扩展后，可以看到角落里友好的弹出消息，以及在“扩展”[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)中列出的“Composer” ****。
   
    ![在 Azure 中为 PHP (Laravel) 应用启用 Composer 后的“扩展”边栏选项卡](./media/app-service-web-php-get-started/configure-composer-end.png)
   
    你已启用 Composer！
10. 返回 Web 应用的[“资源”边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)，单击“应用程序设置”。
    
     ![访问“设置”边栏选项卡以在 Azure 中设置 PHP (Laravel) 应用的虚拟目录](./media/app-service-web-php-get-started/configure-virtual-dir-settings.png)
    
     在“应用程序设置”[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)中，请注意之前设置的 PHP 版本：
    
     ![Azure 中“设置”边栏选项卡中的 PHP (Laravel) 应用的 PHP 版本](./media/app-service-web-php-get-started/configure-virtual-dir-settings-a-cli2.png)
    
     和你添加的应用程序设置：
    
     ![Azure 中“设置”边栏选项卡中的 PHP (Laravel) 应用的应用程序设置](./media/app-service-web-php-get-started/configure-virtual-dir-settings-b.png)
11. 滚动到[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)的底部，将根虚拟目录更改为指向 **site\wwwroot\public** 而不是 **site\wwwroot**。
    
     ![在 Azure 中设置 PHP (Laravel) 应用的虚拟目录](./media/app-service-web-php-get-started/configure-virtual-dir-public.png)
12. 单击[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)顶部的“保存”。
    
     你已完成虚拟目录的设置！ 

## <a name="deploy-your-web-app-with-git-and-setting-environment-variables"></a>使用 Git 部署 Web 应用（并设置环境变量）
你可以开始部署你的代码了。 你将返回到命令提示符或终端来执行该操作。

1. 如同会针对任何 Git 存储库执行的操作一样，将代码推送到 Azure Web 应用：
   
        git push azure master 
   
    出现提示时，使用前面创建的凭据。

2. 通过运行以下命令在浏览器中查看它的运行：
   
        az appservice web browse --name <app_name> --resource-group my-php-app-group
   
    浏览器应显示 Laravel 初始屏幕。
   
    ![将 Web 应用部署到 Azure 后的 Laravel 初始屏幕](./media/app-service-web-php-get-started/laravel-azure-splash-screen.png)
   
    祝贺你，你已经在 Azure 中运行 Laravel Web 应用了！

## <a name="troubleshoot-common-errors"></a>排查常见错误
以下是你学习本教程时可能会遇到的一些错误：

* [Azure CLI 显示“‘站点’不是 azure 命令”](#clierror)
* [Web 应用显示 HTTP 403 错误](#http403)
* [Web 应用显示“糟糕！看起来某些地方出错了。”](#whoops)
* [Web 应用显示“未找到支持的加密器。”](#encryptor)

<a name="clierror"></a>

### <a name="azure-cli-shows-site-is-not-an-azure-command"></a>Azure CLI 显示“‘站点’不是 azure 命令”
在命令行终端运行 `azure site *` 时，会看到错误 `error:   'site' is not an azure command. See 'azure help'.` 

这通常是切换到“ARM”（Azure Resource Manager）模式的结果。 若要解决此问题，请运行 `azure config mode asm` 以切换回到“ASM”（Azure 服务管理）模式。

<a name="http403"></a>

### <a name="web-app-shows-http-403-error"></a>Web 应用显示 HTTP 403 错误
已经成功将 Web 应用部署到 Azure，但当浏览到 Azure Web 应用时，遇到 `HTTP 403` 或 `You do not have permission to view this directory or page.` 错误

这很可能是因为该 Web 应用找不到 Laravel 应用的入口点。 请确保已将根虚拟目录更改为指向 Laravel 的 `index.php` 所在的 `site\wwwroot\public`（请参阅[配置 Azure Web 应用](#configure)）。

<a name="whoops"></a>

### <a name="web-app-shows-whoops-looks-like-something-went-wrong"></a>Web 应用显示“糟糕！看起来某些地方出错了。”
已经成功将 Web 应用部署到 Azure，但当浏览到 Azure Web 应用时，遇到含义模糊的错误消息 `Whoops, looks like something went wrong.`

若要获取更加详细的错误信息，请将 `APP_DEBUG` 环境变量设置为 `true` 以启用 Laravel 调试功能（请参阅[配置 Azure Web 应用](#configure)）。

<a name="encryptor"></a>

### <a name="web-app-shows-no-supported-encryptor-found"></a>Web 应用显示“未找到支持的加密器。”
你已经成功将 Web 应用部署到 Azure，但当你浏览到你的 Azure Web 应用时，将遇到下面的错误消息：

![Azure 中的 PHP (Laravel) 应用缺少 APP_KEY](./media/app-service-web-php-get-started/laravel-error-APP_KEY.png)

这是一个严重错误，但是由于你启用了 Laravel 调试功能，因此至少它不是难懂的。 在 Laravel 论坛上粗略地搜索一下错误字符串，可显示该错误的原因是未在 `.env` 中设置 APP_KEY，或者在 Azure 中根本不存在 `.env` 文件。 可以通过将设置 `APP_KEY` 添加为 Azure 应用程序设置来解决此问题（请参阅[配置 Azure Web 应用](#configure)）。

## <a name="next-steps"></a>后续步骤
通过[在 Azure 中创建 MySQL 数据库](../store-php-create-mysql-database.md)了解如何将数据添加到应用程序。 另外，你可以查看 Azure 中以下更加有用的 PHP 链接：

* [PHP 开发人员中心](/develop/php/)
* [通过 Azure 应用商店创建 Web 应用](app-service-web-create-web-app-from-marketplace.md)
* [在 Azure 应用服务 Web 应用中配置 PHP](web-sites-php-configure.md)
* [在 Azure 应用服务中将 WordPress 转换为 Multisite](web-sites-php-convert-wordpress-multisite.md)
* [Azure 应用服务上的企业级 WordPress](web-sites-php-enterprise-wordpress.md)




<!--HONumber=Feb17_HO3-->


