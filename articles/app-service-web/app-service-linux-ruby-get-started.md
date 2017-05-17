---
title: "使用 Linux 上的 Azure 应用服务 Web 应用创建 Ruby 应用 | Microsoft Docs"
description: "了解如何使用 Linux 上的 Azure 应用服务 Web 应用创建 Ruby 应用。"
keywords: "azure 应用服务, linux, oss"
services: app-service
documentationcenter: 
author: wesmc7777
manager: erikre
editor: 
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c63bf790a50b894ea1732ae3142e6c1ff9ccbd4c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-ruby-app-with-azure-web-app-on-linux---preview"></a>使用 Linux 上的 Azure Web 应用创建 Ruby 应用 - 预览版

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概述

本教程展示了如何在本地创建一个基本的 ruby on rails 应用程序并将其部署到 Linux 上的 Azure Web 应用。

## <a name="prerequisites"></a>先决条件

* 已在开发计算机上安装了 [Ruby 2.3.3 或更高版本](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller)。
* 已在开发计算机上安装了 [Git](https://git-scm.com/downloads)
* 一个[有效的 Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)
* 本教程是以 Ubuntu 环境为背景编写的。 所有系统命令都是特定于 bash 的。


## <a name="create-a-new-local-rails-application"></a>创建一个新的本地 rails 应用程序

1. 为新应用创建一个目录并切换到该目录。

        mkdir ~/workspace
        cd ~/workspace

2. 使用 `ruby -v` 命令初始化 Ruby 并检查版本。

    ![Ruby 初始化](./media/app-service-linux-ruby-get-started/ruby-version.png)

3. 使用 `gem install rails` 命令安装 rails。

    ![安装 rails](./media/app-service-linux-ruby-get-started/install-rails.png)

4. 使用以下命令创建名为 **hello-world** 的 rails 应用程序。

    如果使用的是 Rails 5.1.0 或更高版本，请包括 `--skip-yarn` 选项，如以下命令中所示：

        rails new hello-world --skip-yarn

    对于 5.1.0 之前的 Rails 版本，请使用以下命令：

        rails new hello-world 

    ![新建 Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world.png)

    ![新建 Hello-world](./media/app-service-linux-ruby-get-started/rails-new-hello-world-2.png)

    如果使用的是 Rails 5.1+，不使用 `--skip-yarn` 选项时会创建 package.json 文件。 我们不希望将其包括在我们的部署中。 作为替代方法，你可以删除 package.json文件，或者如下所示将其添加到目录中的 *.git-ignore* 文件中。 

        # Ignore package.json
        /package.json

5. 切换到 *hello-world* 目录并启动服务器。

        cd hello-world
        rails server

    ![启动 Hello-world](./media/app-service-linux-ruby-get-started/start-hello-world-server.png)
    
6. 使用 Web 浏览器导航到 `http://localhost:3000` 以在本地测试该应用。    

    ![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="prepare-the-app-for-azure"></a>针对 Azure 准备应用

默认情况下，ruby 映像在使用 `-e production` 标志的情况下运行服务器。 此环境要求对 Linux 上的 Azure Web 应用执行某些设置。 容器负责执行该设置的一部分（例如设置 `SECRET_KEY_BASE`）。 必须配置默认路由。 否则，在浏览站点时将会收到 404 错误。

若要配置默认路由，请执行以下操作：

1. 打开 *~/workspace/hello-world/config/routes.rb* 进行编辑。 如屏幕截图中所示添加以下行。 

        root 'application#hello'

    ![routes.rb](./media/app-service-linux-ruby-get-started/routes-rb.png)


2. 打开 *~/workspace/hello-world/app/controllers/application_controller.rb* 进行编辑。 如屏幕截图中所示添加以下行。

        def hello
            render html: "Hello, world from Azure Web App on Linux!"
        end

    ![routes.rb](./media/app-service-linux-ruby-get-started/application-controller-rb.png)


3. 现在已配置了你的应用。 使用 web 浏览器导航到 `http://localhost:3000` 来确认根登陆页面。

    ![已配置了 Hello World](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

## <a name="create-a-ruby-website-on-azure"></a>在 Azure 上创建 ruby 网站

1. 导航到 [Azure 门户](http://portal.azure.com)并使用你的订阅进行登录。 添加一个 **Linux 上的 Web 应用**，如以下屏幕截图中所示：

    ![创建 Linux 上的 Web 应用](./media/app-service-linux-ruby-get-started/top-level-create.png)

2. “创建边栏选项卡”随即打开，如以下屏幕截图中所示：

    ![“创建”边栏选项卡](./media/app-service-linux-ruby-get-started/create-blade.png)


    1. 为 Web 应用提供一个名称。
    2. 选择现有资源组，或创建一个新的资源组。 （请在[限制部分](app-service-linux-intro.md)中查看可用区域。）
    3. 选择现有的 Azure 应用服务计划或创建新的计划。 （请在[限制部分](app-service-linux-intro.md)中查看应用服务计划说明。）
    4. 选择适用于你的容器配置的 **Ruby 2.3** 内置运行时堆栈。
    5. 针对 Web 应用单击“固定到仪表板”。
    6. 单击“创建”。

3. 在创建 Web 应用后，将显示“概述”边栏选项卡。 复制新 Web 应用的 **URL** 并在浏览器中将其打开。 将显示以下启动页面。

    ![启动页面](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>部署应用程序

在本教程中，我们使用 Git 将本地 Ruby 应用程序部署到 Azure。

1. 新的 Azure 网站已配置了一个 Git 部署。 可以通过在插入你的 Web 应用名称后导航到以下 URL 来找到 Git 部署 URL：

        https://{your web app name}.scm.azurewebsites.net/api/scm/info

    Git URL 具有基于你的 Web 应用名称的以下形式：

        https://{your web app name}.scm.azurewebsites.net/{your web app name}.git

2. 运行以下命令将本地应用程序部署到 Azure 网站。

        cd ~/workspace/hello-world
        git init
        git remote add azure <Git deployment URL from above>
        git add -A
        git commit -m "Initial deployment commit"
        git push master

    确认远程部署操作报告了成功消息。

    ![部署 Web 应用](./media/app-service-linux-ruby-get-started/deployment-success.png)

    如果某个错误指出远程已挂起，则说明部署可能仍在进行。 在这种情况下，请在浏览器中导航到以下 URL：

        https://{your web app name}.scm.azurewebsites.net/api/deployments

3. 在部署完成后，重新启动 Web 应用以使部署生效。 在 [Azure 门户](http://portal.azure.com)中，导航到 Web 应用的“概述”边栏选项卡。

    在工具栏上单击“重新启动”。

    ![重新启动 Web 应用](./media/app-service-linux-ruby-get-started/restart-web-app.png)

4. 导航到你的站点并确认你的更新是实时的。 

    当应用正在重新启动时，尝试浏览站点会导致 HTTP 状态代码错误 503（服务器不可用）。 可能需要花费几分钟时间才能完全重新启动。

        http://{your web app name}.azurewebsites.net

    ![更新的 Web 应用](./media/app-service-linux-ruby-get-started/hello-world-updated.png)
    

## <a name="next-steps"></a>后续步骤
有关 Linux 上的 Azure 应用服务 Web 应用的详细信息，请参阅以下链接。 如有问题和疑问，还可以在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [在 Linux 应用服务中创建 Web 应用](app-service-linux-how-to-create-web-app.md)
* [如何在 Linux 上使用应用服务自定义 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure 应用服务 Web 应用中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题](app-service-linux-faq.md)


