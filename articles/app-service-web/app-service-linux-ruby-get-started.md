---
title: "使用 Linux 上的 Web 应用创建 Ruby 应用 | Microsoft 文档"
description: "了解如何使用 Linux 上的 Web 应用创建 Ruby 应用。"
keywords: azure app service, linux, oss, ruby
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
ms.date: 08/15/2017
ms.author: wesmc;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 17f3f1a2122c508501134a0c43ab6abce412fb44
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---
# <a name="create-a-ruby-app-with-web-apps-on-linux"></a>使用 Linux 上的 Web 应用创建 Ruby 应用 

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

[Azure Web 应用](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview)提供高度可缩放、自修补的 Web 托管服务。 本快速入门将介绍如何创建一个基本的 Ruby on Rails 应用程序并将其部署到 Azure 上，以作为 Linux 上的一个 Web 应用程序。

![Hello-world](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

## <a name="prerequisites"></a>先决条件

* [Ruby 2.4.1 或更高版本](https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller)。
* [Git](https://git-scm.com/downloads)。
* 一个[有效的 Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>下载示例

在终端窗口中，运行以下命令，将示例应用存储库克隆到本地计算机：

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="run-the-application-locally"></a>在本地运行应用程序

运行 rails 服务器以使应用程序正常运作。 切换到“hello-world”目录，`rails server` 命令将启动服务器。

```bash
cd hello-world\bin
rails server
```
    
使用 Web 浏览器导航到 `http://localhost:3000` 以在本地测试该应用。    

![Hello-world](./media/app-service-linux-ruby-get-started/hello-world.png)

## <a name="modify-app-to-display-welcome-message"></a>修改应用程序以显示欢迎消息

修改应用程序以使其显示欢迎消息。 更改应用程序控制器，以使其将消息以 HTML 的形式返回到浏览器。 

打开 *~/workspace/hello-world/app/controllers/application_controller.rb* 进行编辑。 修改 `ApplicationController` 类以使其类似下面的代码示例：

  ```ruby
  class ApplicationController > ActionController :: base
    protect_from_forgery with: :exception 
    def hello
      render html: "Hello, world from Azure Web App on Linux!"
    end
  end
  ```

现在已配置了应用。 使用 web 浏览器导航到 `http://localhost:3000` 来确认根登陆页面。

![已配置了 Hello World](./media/app-service-linux-ruby-get-started/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ruby-web-app-on-azure"></a>在 Azure 上创建 Ruby Web 应用

使用 [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) 命令创建 Web 应用的应用服务计划。 
 
```azurecli-interactive
  az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --is-linux
```

接下来，发出 [az webapp create](https://docs.microsoft.com/cli/azure/webapp) 命令，以创建使用新创建的服务计划的 Web 应用。 请注意，运行时设置为 `ruby|2.3`。 不要忘记将 `<app name>` 替换为唯一的应用名称。

```azurecli-interactive
  az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --runtime "ruby|2.3" --deployment-local-git
```

创建 Web 应用后，即可查看“概述”页。 导航到此页。 将显示以下启动页面：

![启动页面](./media/app-service-linux-ruby-get-started/splash-page.png)


## <a name="deploy-your-application"></a>部署应用程序

使用 Git 将 Ruby 应用程序部署到 Azure。 Web 应用已配置了 Git 部署。 可以通过发出 [az webapp deployment](https://docs.microsoft.com/cli/azure/webapp/deployment) 命令检索部署 URL。  

```bash
az webapp deployment source show --name <app name> --resource-group myResourceGroup
```

请注意，Git URL 具有基于 Web 应用名称的以下形式：

```bash
https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
```

[!INCLUDE [Clean-up section](../../includes/configure-deployment-user-no-h.md)]

运行以下命令，以将本地应用程序部署到 Azure 网站：

```bash
git remote add azure <Git deployment URL from above>
git add -A
git commit -m "Initial deployment commit"
git push azure master
```

确认远程部署操作报告了成功消息。 命令生成的输出类似于以下文本：

```bash
remote: Using sass-rails 5.0.6
remote: Updating files in vendor/cache
remote: Bundle gems are installed into ./vendor/bundle
remote: Updating files in vendor/cache
remote: ~site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<your web app name>.scm.azurewebsites.net/<your web app name>.git
  579ccb....2ca5f31  master -> master
myuser@ubuntu1234:~workspace/<app name>$
```

在部署完成后，通过使用 [az webapp restart](https://docs.microsoft.com/cli/azure/webapp#restart) 命令，重新启动 Web 应用以使部署生效，如下所示：

```azurecli-interactive 
az webapp restart --name <app name> --resource-group myResourceGroup
```

导航到你的站点并验证结果。

```bash
http://<your web app name>.azurewebsites.net
```
![更新的 Web 应用](./media/app-service-linux-ruby-get-started/hello-world-updated.png)

> [!NOTE]
> 当应用正在重新启动时，尝试浏览站点会生成 HTTP 状态代码 `Error 503 Server unavailable`。 可能需要花费几分钟时间才能完全重新启动。
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]


## <a name="next-steps"></a>后续步骤

[Linux 上的 Azure 应用服务 Web 应用常见问题解答](https://docs.microsoft.com/azure/app-service-web/app-service-linux-faq.md)
