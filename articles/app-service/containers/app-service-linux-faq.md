---
title: 运行内置容器常见问题解答
description: 查找有关 Azure 应用服务中内置 Linux 容器的常见问题的解答。
keywords: Azure 应用服务, Web 应用, 常见问题解答, Linux, oss, 用于容器的 Web 应用, 多容器, 多容器
author: msangapu-msft
ms.topic: article
ms.date: 10/30/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f0a8b1758571a9473402d11a4d5141a11f76504d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245814"
---
# <a name="azure-app-service-on-linux-faq"></a>Linux 上的 Azure 应用服务常见问题解答

随着 Linux 应用服务的发布，我们正在努力添加功能和改进我们的平台。 本文提供客户最近提出的问题的解答。

如果你有问题，请对本文发表评论。

## <a name="built-in-images"></a>内置映像

**我想分叉平台提供的内置 Docker 容器。我在哪里可以找到这些文件？**

可以在 [GitHub](https://github.com/azure-app-service) 上找到所有 Docker 文件。 可以在 [Docker Hub](https://hub.docker.com/u/appsvc/) 上找到所有 Docker 容器。

<a id="#startup-file"></a>

**配置运行时堆栈时，“启动文件”部分的所需值是什么？**

| 堆栈           | 预期值                                                                         |
|-----------------|----------------------------------------------------------------------------------------|
| Java SE         | 启动 JAR 应用的命令（例如， `java -jar /home/site/wwwroot/app.jar --server.port=80` |
| Tomcat          | 用于执行任何必要配置的脚本的位置（例如， `/home/site/deployments/tools/startup_script.sh`          |
| Node.js         | PM2 配置文件或脚本文件                                |
| .NET Core       | 编译的 DLL 名称为`dotnet <myapp>.dll`                                 |
| Ruby            | 要使用                     |

这些命令或脚本是在启动内置 Docker 容器后，但在应用程序代码启动之前执行的。

## <a name="management"></a>管理

**在 Azure 门户中按下“重启”按钮时，会发生什么情况？**

此操作等同于 Docker 重启。

**可以使用安全外壳 (SSH) 连接到应用容器虚拟机 (VM) 吗？**

是的，可以通过源代码管理 (SCM) 站点实现此操作。

> [!NOTE]
> 还可以使用 SSH、SFTP 或 Visual Studio Code（用于实时调试 Node.js 应用）直接从本地开发计算机连接到应用容器。 有关详细信息，请参阅[远程调试和通过 SSH 登录到 Linux 上的应用服务](https://azure.github.io/AppService/2018/05/07/New-SSH-Experience-and-Remote-Debugging-for-Linux-Web-Apps.html)。
>

**如何通过 SDK 或 Azure 资源管理器模板创建 Linux 应用服务计划？**

将应用服务的**保留**字段设置为*true*。

## <a name="continuous-integration-and-deployment"></a>持续集成和部署

**在 Docker Hub 上更新映像后，我的 Web 应用仍使用旧的 Docker 容器映像。您是否支持自定义容器的连续集成和部署？**

支持，若要设置 Azure 容器注册表或 DockerHub 的持续集成/部署，请查阅以下文章：[使用用于容器的 Web 应用进行持续部署](./app-service-linux-ci-cd.md)。 对于专用注册表，可以通过先停止然后启动 Web 应用来刷新容器。 或者，可以更改或添加虚拟应用程序设置，从而强制刷新容器。

**是否支持过渡环境？**

是的。

**是否可以使用 *WebDeploy/MSDeploy* 来部署 Web 应用？**

可以，需要将名为 `WEBSITE_WEBDEPLOY_USE_SCM` 的应用设置设置为 *false*。

**使用 Linux Web 应用时，应用程序的 Git 部署失败。如何解决此问题？**

如果 Linux Web 应用的 Git 部署失败，可选择以下选项之一部署应用程序代码：

- 使用连续传递（预览）功能：您可以将应用的源代码存储在 Azure DevOps Git 存储库或 GitHub 存储库中，以使用 Azure 连续传递。 有关详细信息，请参阅[如何为 Linux Web 应用配置持续交付](https://blogs.msdn.microsoft.com/devops/2017/05/10/use-azure-portal-to-setup-continuous-delivery-for-web-app-on-linux/)。

- 使用 [ZIP 部署 API](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)：若要使用此 API，请[通过 SSH 连接到 Web 应用](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-ssh-support)，并转到要在其中部署代码的文件夹。 运行以下代码：

   ```bash
   curl -X POST -u <user> --data-binary @<zipfile> https://{your-sitename}.scm.azurewebsites.net/api/zipdeploy
   ```

   如果有错误指出找不到 `curl` 命令，请确保在运行前一条 `curl` 命令之前使用 `apt-get install curl` 安装 curl。

## <a name="language-support"></a>语言支持

**我想要在 Node.js 应用程序中使用 Web 套接字，要设置什么特殊设置或配置吗？**

需要，请在服务器端 Node.js 代码中禁用 `perMessageDeflate`。 例如，如果要使用 socket.io，请使用以下代码：

```nodejs
const io = require('socket.io')(server,{
  perMessageDeflate :false
});
```

**是否支持未编译的 .NET Core 应用？**

是的。

**是否支持将 Composer 用作 PHP 应用的依赖关系管理器？**

支持，在 Git 部署过程中，Kudu 应检测到正在部署 PHP 应用程序（这得益于 composer.lock 文件的存在），然后触发 composer 安装。

## <a name="custom-containers"></a>自定义容器

**我使用自己的自定义容器。我希望平台将 SMB 共享装载到`/home/`目录中。**

如果`WEBSITES_ENABLE_APP_SERVICE_STORAGE`设置**未指定**或设置为*true，* 则`/home/`目录将在缩放实例之间**共享**，写入的文件将在重新启动期间**保留**。 显式设置为`WEBSITES_ENABLE_APP_SERVICE_STORAGE` *false*将禁用装载。

**自定义容器需要很长时间才能启动，并且平台在它完成启动之前便重启了容器。**

可以配置该平台在重启容器之前的等待时间。 为此，可将 `WEBSITES_CONTAINER_START_TIME_LIMIT` 应用设置设为所需的值。 默认值为 230 秒，最大值为 1800 秒。

**专用注册表服务器 URL 的格式是什么？**

提供完整注册表 URL，包括 `http://` 或 `https://`。

**专用注册表选项中的映像名称的格式是什么？**

添加完整映像名称，包括专用注册表 URL（例如 myacr.azurecr.io/dotnet:latest）。 使用自定义端口的映像名称[无法通过门户输入](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650)。 要设置`docker-custom-image-name`，请使用[`az`命令行工具](https://docs.microsoft.com/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set)。

**是否可以在自定义容器映像上公开多个端口？**

我们不支持公开多个端口。

**可以自带存储吗？**

可以，[自带存储](https://docs.microsoft.com/azure/app-service/containers/how-to-serve-content-from-azure-storage)以预览版形式提供。

**为何无法从 SCM 站点浏览自定义容器的文件系统或正在运行的进程？**

SCM 站点在单独的容器中运行。 用户无法查看应用容器的文件系统或正在运行的进程。

**我的自定义容器侦听端口 80 以外的端口。如何配置应用以将请求路由到该端口？**

我们提供自动端口检测。 也可以指定名为 *WEBSITES_PORT* 的应用设置，并为其提供所需的端口号值。 以前，平台使用 *PORT* 应用设置。 我们计划弃用此应用设置，改为独占使用 *WEBSITES_PORT*。

**是否需要在自定义容器中实现 HTTPS？**

不需要，平台会处理共享前端上的 HTTPS 终止。

## <a name="multi-container-with-docker-compose"></a>带 Docker 组合的多容器

**如何将 Azure 容器注册表 (ACR) 配置为用于多容器？ **

若要将 ACR 用于多容器，**所有容器映像**都必须托管在同一台 ACR 注册表服务器上。 一旦他们在同一注册表服务器上，您将需要创建应用程序设置，然后更新 Docker Compose 配置文件以包括 ACR 映像名称。

创建以下应用程序设置：

- DOCKER_REGISTRY_SERVER_USERNAME
- DOCKER_REGISTRY_SERVER_URL（完整 URL，前`https://<server-name>.azurecr.io`： ）
- DOCKER_REGISTRY_SERVER_PASSWORD（在 ACR 设置中启用管理员访问权限）

在配置文件内引用 ACR 映像，如下例所示：

```yaml
image: <server-name>.azurecr.io/<image-name>:<tag>
```

**怎么知道哪个容器可以访问 Internet？**

- 只能打开一个容器进行访问
- 只能访问端口 80 和 8080（公开的端口）

以下规则用于确定哪个容器可供访问 — 按优先顺序排列：

- 设置为容器名称的应用程序设置 `WEBSITES_WEB_CONTAINER_NAME`
- 第一个定义端口 80 或 8080 的容器
- 如果以上规则均不适用，则文件中定义的第一个容器将可供访问（公开）


## <a name="web-sockets"></a>Web 套接字

Linux 应用程序支持 Web 套接字。

> [!IMPORTANT]
> 免费应用服务计划上的 Linux 应用当前不支持 Web 套接字。 我们正在努力消除此限制，并计划在免费应用服务计划中支持多达 5 个 Web 套接字连接。

## <a name="pricing-and-sla"></a>定价和 SLA

**服务正式版的定价是多少？**

定价因 SKU 和地区而异，但您可以在我们的定价页面：[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/linux/)上看到更多详细信息。

## <a name="other-questions"></a>其他问题

**"请求的功能在资源组中不可用"是什么意思？**

使用 Azure 资源管理器 （ARM） 创建 Web 应用时，可能会看到此消息。 根据当前限制，对于同一资源组，不能在同一区域中混合 Windows 和 Linux 应用。

**应用程序设置名称中支持的字符有哪些？**

应用程序设置只能使用字母（A-Z、a-z）、数字 (0-9) 和下划线字符 (_)。

**可在何处请求新功能？**

可以在 [Web 应用反馈论坛](https://aka.ms/webapps-uservoice)提交看法。 请将“[Linux]”添加到建议的标题中。

## <a name="next-steps"></a>后续步骤

- [什么是 Linux 上的 Azure 应用服务？](app-service-linux-intro.md)
- [设置 Azure 应用服务中的过渡环境](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [使用用于容器的 Web 应用进行持续部署](./app-service-linux-ci-cd.md)
