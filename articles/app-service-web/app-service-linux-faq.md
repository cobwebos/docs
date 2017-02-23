---
title: "Linux 上的 Azure 应用服务 Web 应用常见问题解答 | Microsoft Docs"
description: "Linux 上的 Azure 应用服务 Web 应用常见问题解答。"
keywords: "Azure 应用服务, Web 应用, 常见问题解答, Linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
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
ms.sourcegitcommit: d4a5260dde1994bbf4ef4467eddd2fb80a2199b0
ms.openlocfilehash: 22c6c22ba3123555d279fac087a6be2f99bde8d7


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>Linux 上的 Azure 应用服务 Web 应用常见问题解答 #

随着 Linux 上 Azure 应用服务的发布（目前为预览版），我们正在努力添加功能和改进我们的平台。 以下是过去几个月中我们的客户提出的一些常见问题。
如果你有疑问，请对本文做出评论，我们会尽快为你解答。

## <a name="built-in-images"></a>内置映像 ##

**问：**我想对平台提供的内置 Docker 容器进行分叉。 在哪里可以找到这些文件？

**答：**此处可以找到所有 Docker 文件：https://github.com/azure-app-service。 此处可以找到所有 Docker 容器：https://hub.docker.com/u/appsvc/。

## <a name="management"></a>管理 ##

**问：**在门户中按重启按钮后，我的 Web 应用未启动，这是为什么？

**答：**我们正在努力尽快启用重置按钮，目前你有两个选项。
1. 添加或更改虚拟应用程序设置，这将强制 Web 应用重启。 
2. 停止，然后启动 Web 应用。

**问：**是否可以通过 SSH 连接到 VM？

**答：**不能，我们将在近期提供通过 SSH 连接到应用容器的方法。

## <a name="continous-integration--deployment"></a>持续集成/部署 ##

**问：**更新 DockerHub 上的映像后，我的 Web 应用是否仍使用旧的 Docker 容器映像？ 是否支持自定义容器的持续集成/部署？

**答：**可以通过停止然后启动 Web 应用或更改/添加虚拟应用程序设置来强制刷新容器，我们将在近期为自定义容器提供 CI/CD 功能。

## <a name="language-support"></a>语言支持 ##

**问：**是否支持未编译的 .NET Core 应用？

**答：**不支持，需要使用所有依赖项部署已编译的 .NET Core 应用，我们将在近期提供完整的部署和生成体验。

## <a name="custom-containers"></a>自定义容器 ##

**问：**我正在使用我自己的自定义容器。 我的应用位于 \home\ directory 中，但是当我使用 SCM 站点或 ftp 客户端浏览内容时找不到我的文件。 文件在哪里？

**答：**我们将 SMB 共享装载到 \home\ directory；因此会替代此处的任何内容。

**问：**我想在自定义容器映像上公开多个端口。 可以吗？

**答：**目前不支持此功能。

**问：**我可以自带存储吗？

**答：**目前不支持，但计划在近期提供此支持。

**问：**我无法从 SCM 站点浏览自定义容器的文件系统或正在运行的进程。 为什么会这样？

**答：**SCM 站点在单独的容器中运行，你无法查看应用容器的文件系统或正在运行的进程。

## <a name="pricing-and-sla"></a>定价和 SLA ##

**问：**公共预览版的定价是多少？

**答：**采用 Azure 应用服务的常规定价，但会将应用运行小时数减半后进行计费；即按 Azure 应用服务常规定价的 50% 计费。

## <a name="other"></a>其他 ##

**问：**应用程序设置名称中支持的字符有哪些？

**答：**应用程序设置只能使用 A-Z、a-z、0-9 和下划线。

**问：**可在何处请求新功能？

**答：**可在此处提交你的建议：https://aka.ms/webapps-uservoice。 请将 [Linux] 添加到建议的标题中。

## <a name="next-steps"></a>后续步骤
* [Linux 应用服务简介](./app-service-linux-intro.md) 
* [什么是 Linux 应用服务？](app-service-linux-intro.md)
* [在 Linux 应用服务中创建 Web 应用](./app-service-linux-how-to-create-a-web-app.md)



<!--HONumber=Feb17_HO3-->


