---
title: 创建 Ruby 应用并将其部署到 Linux 应用服务 | Microsoft Docs
description: 了解如何使用 Linux 应用服务创建 Ruby 应用。
keywords: azure app service, linux, oss, ruby
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: cfowler
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 67ecb7c253d7968057b27c5b37b5ed4da12c8d05
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441487"
---
# <a name="create-a-ruby-app-in-app-service-on-linux"></a>使用 Linux 应用服务创建 Ruby 应用

[Linux 上的 Azure 应用服务](app-service-linux-intro.md)提供高度可缩放、自修补的 Web 托管服务。 本快速入门介绍如何创建一个基本的 [Ruby on Rails](https://rubyonrails.org/) 应用程序并将其部署到 Azure 上，以作为 Linux 上的 Web 应用。

![Hello-world](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">安装 Ruby 2.3 或更高版本</a>
* <a href="https://git-scm.com/" target="_blank">安装 Git</a>

## <a name="download-the-sample"></a>下载示例

在终端窗口中，运行以下命令，将示例应用存储库克隆到本地计算机：

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>在本地运行应用程序

需要运行 rails 服务器才能使应用程序正常工作。 更改到 `hello-world` 目录，然后使用 `rails server` 命令启动该服务器。

```bash
cd hello-world\bin
rails server
```

使用 Web 浏览器导航到 `http://localhost:3000` 以在本地测试该应用。

![已配置了 Hello World](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

浏览到该站点查看使用内置映像新建的 Web 应用。 将 _&lt;应用名称>_ 替换为 Web 应用名称。

```bash
http://<app_name>.azurewebsites.net
```

新 Web 应用应该如下所示：

![启动页面](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>部署应用程序

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

在部署完成后，通过使用 [`az webapp restart`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-restart) 命令，重新启动 Web 应用以使部署生效，如下所示：

```azurecli-interactive
az webapp restart --name <app name> --resource-group myResourceGroup
```

导航到你的站点并验证结果。

```bash
http://<app name>.azurewebsites.net
```

![更新的 Web 应用](./media/quickstart-ruby/hello-world-updated.png)

> [!NOTE]
> 当应用正在重新启动时，尝试浏览站点会生成 HTTP 状态代码 `Error 503 Server unavailable`。 可能需要花费几分钟时间才能完全重新启动。
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Ruby on Rails 与 MySQL](tutorial-ruby-postgres-app.md)
