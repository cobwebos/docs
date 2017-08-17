---
title: "Linux 上的 Azure 应用服务 Web 应用常见问题解答 | Microsoft Docs"
description: "Linux 上的 Azure 应用服务 Web 应用常见问题解答。"
keywords: "Azure 应用服务, Web 应用, 常见问题解答, Linux, oss"
services: app-service
documentationCenter: 
authors: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: ff4f4ecd12bc26fcc44a20a193d73f952ed56f1a
ms.contentlocale: zh-cn
ms.lasthandoff: 08/07/2017

---

# <a name="azure-app-service-web-app-on-linux-faq"></a>Linux 上的 Azure 应用服务 Web 应用常见问题解答

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


随着 Linux 上 Web 应用的发布，我们正在努力添加功能和改进我们的平台。 以下是过去几个月中我们的客户提出的一些常见问题 (FAQ)。
如果有疑问，请对本文发表评论，我们会尽快解答。

## <a name="built-in-images"></a>内置映像

**问：**我想对平台提供的内置 Docker 容器进行分叉。 在哪里可以找到这些文件？

**答：**可以在 [GitHub](https://github.com/azure-app-service) 上找到所有 Docker 文件。 可以在 [Docker Hub](https://hub.docker.com/u/appsvc/) 上找到所有 Docker 容器。

**问：**配置运行时堆栈时，“启动文件”部分的所需值是什么？

**答：**对于 Node.Js，指定 PM2 配置文件或脚本文件。 对于 .NET Core，指定已编译的 DLL 名称。 对于 Ruby，可以指定要用于初始化应用的 Ruby 脚本。

## <a name="management"></a>管理

**问：**在 Azure 门户中按下“重新启动”按钮时，会发生什么情况？

**答：**这等同于 Docker 重新启动。

**问：**我可以使用安全外壳 (SSH) 连接到应用容器虚拟机 (VM) 吗？

**答：**可以，可以通过 SCM 站点执行该操作，请查阅以下文章了解详细信息：[SSH 对 Linux 上 Web 应用的支持](./app-service-linux-ssh-support.md)

## <a name="continuous-integrationdeployment"></a>持续集成/部署

**问：**更新 Docker Hub 上的映像后，我的 Web 应用仍使用旧的 Docker 容器映像。 是否支持自定义容器的持续集成/部署？

**答：**若要设置 Azure 容器注册表或 DockerHub 映像的持续集成/部署，请查阅以下文章：[使用 Linux 上的 Azure Web 应用进行持续部署](./app-service-linux-ci-cd.md)。 对于专用注册表，可以通过先停止然后启动 Web 应用来刷新容器。 或者，可以更改或添加虚拟应用程序设置，从而强制刷新容器。

**问：**是否支持过渡环境？

**答：**可以。

**问：**是否可以使用 **Web 部署**来部署我的 Web 应用？

**答：**可以，需要将名为 `WEBSITE_WEBDEPLOY_USE_SCM` 的应用设置设置为 `false`。

## <a name="language-support"></a>语言支持

**问：**是否支持未编译的 .NET Core 应用？

**答：**可以。

**问：**是否支持将 Composer 用作 PHP 应用的依赖关系管理器？

**答：**可以。 在 Git 部署过程中，Kudu 应检测到你正在部署 PHP 应用程序（得益于 composer.json 文件的存在），并且会为你触发 composer 安装。

## <a name="custom-containers"></a>自定义容器

**问：**我使用的是我自己的自定义容器。 我的应用位于 `\home\` 目录中，但是当我使用 [SCM 站点](https://github.com/projectkudu/kudu)或 FTP 客户端浏览内容时找不到我的文件。 文件在哪里？

**答：**我们将 SMB 共享装入 `\home\` 目录。 这会替代此处的所有内容。

**问：**专用注册服务器 URL 的格式是什么？

**答：**需要提供完整注册 URL，包括 `http://` 或 `https://`。

**问：**专用注册选项中的映像名称的格式是什么？

**答：**需要添加完整映像名称，包括专用注册 URL（例如， myacr.azurecr.io/dotnet:latest）

**问：**我想在自定义容器映像上公开多个端口。 可以吗？

**答：**目前不支持此操作。

**问：**我可以自带存储吗？

**答：**目前不支持此操作。

**问：**我无法从 SCM 站点浏览自定义容器的文件系统或正在运行的进程。 为什么会这样？

**答：**SCM 站点在单独的容器中运行。 用户无法查看应用容器的文件系统或正在运行的进程。

**问：**我的自定义容器侦听除端口 80 以外的端口。 如何配置我的应用将请求路由到该端口？

**答：**我们进行自动端口检测，而你还可以指定名为 WEBSITES_PORT 的应用程序设置，并为其提供所需的端口号值。 平台之前使用的是 `PORT` 应用设置，我们计划弃用此应用设置，改为以独占方式使用 `WEBSITES_PORT`。

**问：**是否需要在自定义容器中实现 HTTPS。

**答：**否，平台会处理共享前端上的 HTTPS 终止。

## <a name="pricing-and-sla"></a>定价和 SLA

**问：**使用公共预览版时的定价是什么？

**答：**根据 Azure 应用服务常规定价，按照应用运行小时数的一半计费。 这意味着可以获得 Azure 应用服务常规定价的 50% 的折扣。

## <a name="other"></a>其他

**问：**应用程序设置名称中支持的字符有哪些？

**答：**应用程序设置只能使用 A-Z、a-z、0-9 和下划线。

**问：**可在何处请求新功能？


            **答：**可以在 [Web 应用反馈论坛](https://aka.ms/webapps-uservoice)提交建议。 请将“[Linux]”添加到建议的标题中。

## <a name="next-steps"></a>后续步骤
* [什么是 Linux 上的 Azure Web 应用？](app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 应用中创建 Web 应用](app-service-linux-how-to-create-web-app.md)
* [SSH 对 Linux 上的 Azure Web 应用的支持](./app-service-linux-ssh-support.md)
* [设置 Azure 应用服务中的过渡环境](./web-sites-staged-publishing.md)
* [使用 Linux 上的 Azure Web 应用进行持续部署](./app-service-linux-ci-cd.md)

