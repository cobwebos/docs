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
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: d9410448952438d6b9d437b7ca8823d4f196a2d6
ms.lasthandoff: 04/22/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Linux 上的 Azure 应用服务 Web 应用常见问题解答

随着 Linux 上 Azure 应用服务的发布（目前为预览版），我们正在努力添加功能和改进我们的平台。 以下是过去几个月中我们的客户提出的一些常见问题 (FAQ)。
如果你有疑问，请对本文做出评论，我们会尽快为你解答。

## <a name="built-in-images"></a>内置映像

**问：**我想对平台提供的内置 Docker 容器进行分叉。 在哪里可以找到这些文件？

**答：**可以在 [GitHub](https://github.com/azure-app-service) 上找到所有 Docker 文件。 可以在 [Docker Hub](https://hub.docker.com/u/appsvc/) 上找到所有 Docker 容器。

**问：**配置运行时堆栈时，“启动文件”部分的所需值是什么？

**答：**对于 Node.Js，指定 PM2 配置文件或脚本文件。 对于 .NET Core，指定已编译的 DLL 名称。 对于 Ruby，可以指定要用于初始化应用的 Ruby 脚本。

## <a name="management"></a>管理

**问：**我在 Azure 门户中按了“重启”按钮，但我的 web 应用未重新启动。 为什么？

**答：**我们正努力解决此问题，不久后会启用“重启”按钮。 现在，可以使用以下两个选项：
- 添加或更改虚拟应用程序设置。 这将强制重启 web 应用。
- 停止，然后启动 Web 应用。

**问：**我可以使用安全外壳 (SSH) 连接到应用容器虚拟机 (VM) 吗？

**答：**否。 我们将在以后的版本中提供使用 SSH 连接到应用容器的方法。

## <a name="continuous-integrationdeployment"></a>持续集成/部署

**问：**更新 Docker Hub 上的映像后，我的 Web 应用仍使用旧的 Docker 容器映像。 是否支持自定义容器的持续集成/部署？

**答：**可以通过先停止然后启动 web 应用来刷新容器。 或者，可以更改或添加虚拟应用程序设置，从而强制刷新容器。 我们计划在以后的版本中添加自定义容器的持续集成/部署功能。

## <a name="language-support"></a>语言支持

**问：**是否支持未编译的 .NET Core 应用？

**答：**否。 需要通过所有依赖项部署已编译的 .NET Core 应用。 我们计划在以后的版本中添加完整部署和生成体验。

**问：**是否支持将 Composer 用作 PHP 应用的依赖关系管理器？

**答：**否。 需要使用所有依赖关系部署 PHP 应用。 我们计划在以后的版本中添加完整部署体验。

## <a name="custom-containers"></a>自定义容器

**问：**我使用的是我自己的自定义容器。 我的应用位于 \home\ directory 中，但是当我使用 [SCM 站点](https://github.com/projectkudu/kudu)或 FTP 客户端浏览内容时找不到我的文件。 文件在哪里？

**答：**我们将 SMB 共享装入 \home\ 目录。 这将替代此处的所有内容。

**问：**专用注册服务器 URL 的格式是什么？

**答：**需要输入完整注册 URL（包括“http://”或“https://”）。

**问：**专用注册选项中的映像名称的格式是什么？

**答：**需要添加完整映像名称，包括专用注册 URL（例如， myacr.azurecr.io/dotnet:latest）

**问：**我想在自定义容器映像上公开多个端口。 可以吗？

**答：**目前不支持此操作。

**问：**我可以自带存储吗？

**答：**目前不支持此操作。

**问：**我无法从 SCM 站点浏览自定义容器的文件系统或正在运行的进程。 为什么会这样？

**答：**SCM 站点在单独的容器中运行。 用户无法查看应用容器的文件系统或正在运行的进程。

**问：**我的自定义容器侦听除端口 80 以外的端口。 如何配置我的应用将请求路由到该端口？

**答：**可以指定名为 **PORT** 的应用程序设置，并为其提供所需的端口号值。

**问：**是否需要在自定义容器中实现 HTTPS。

**答：**否，平台会处理共享前端上的 HTTPS 终止。

## <a name="pricing-and-sla"></a>定价和 SLA

**问：**使用公共预览版时的定价是什么？

**答：**根据 Azure 应用服务常规定价，按照应用运行小时数的一半计费。 这意味着你将获得 Azure 应用服务常规定价的 50% 的折扣。

## <a name="other"></a>其他

**问：**应用程序设置名称中支持的字符有哪些？

**答：**应用程序设置只能使用 A-Z、a-z、0-9 和下划线。

**问：**可在何处请求新功能？

**答：**可以在 [Web 应用反馈论坛](https://aka.ms/webapps-uservoice)提交你的建议。 请将“[Linux]”添加到建议的标题中。

## <a name="next-steps"></a>后续步骤
* [什么是 Linux 应用服务？](app-service-linux-intro.md)
* [在 Linux 应用服务中创建 Web 应用](app-service-linux-how-to-create-a-web-app.md)

