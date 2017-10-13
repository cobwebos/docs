---
title: "用于容器的 Azure Web 应用简介 | Microsoft Docs"
description: "了解用于容器的 Azure Web 应用。"
keywords: "azure 应用服务, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: bdfaf0cd7d4bb44b877a9f14891e540fd789b6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-web-app-for-containers"></a>用于容器的 Azure Web 应用简介

[Web 应用](../app-service-web-overview.md)是一个完全托管的计算平台，非常适合用来托管网站和 Web 应用程序。 客户可以使用用于容器的 Web 应用在 Linux 本地为受支持的应用程序堆栈托管 Web 应用。 以下部分列出了目前受支持的应用程序堆栈。

## <a name="languages"></a>语言

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>部署

* FTP
* 本地 Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* 过渡环境
* [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/container-registry-intro)和 DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>控制台、发布和调试

* 环境
* 部署
* 基本控制台
* SSH

## <a name="scaling"></a>扩展

* 客户可以通过更改[应用服务计划](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)的层，增加或减少 Web 应用

## <a name="locations"></a>位置

查看 [Azure 状态仪表板](https://azure.microsoft.com/status)。

## <a name="limitations"></a>限制

Azure 门户仅显示当前对用于容器的 Web 应用有效的功能。 当我们启用更多功能时，会在门户中看到这些功能。

某些功能（例如虚拟网络集成、Azure Active Directory/第三方身份验证或 Kudu 站点扩展）尚不可用。 这些功能可用后，我们将更新文档和博客，以反映所做更改。

用于容器的 Web 应用仅支持[基本和标准](https://azure.microsoft.com/pricing/details/app-service/plans/)应用服务计划，没有[免费或共享](https://azure.microsoft.com/pricing/details/app-service/plans/)层。 以下也是有关用于容器的 Web 应用的重要限制：

* 不能在已经托管非 Linux Web 应用的应用服务计划中创建用于容器的 Web 应用。
* 在包含非 Linux Web 应用的资源组中创建用于容器的 Web 应用时，必须在不同于现有应用服务计划的区域中创建应用服务计划。

## <a name="troubleshooting"></a>故障排除

如果未能启动应用程序或希望检查应用中的日志记录，请查看 LogFiles 目录中的 Docker 日志。 可通过 SCM 站点或 FTP 访问此目录。
若要从容器记录 `stdout` 和 `stderr`，需要在“诊断记录”下启用“Docker 容器记录”。

![启用日志记录][2]

![使用 Kudu 查看 Docker 日志][1]

可从“开发工具”菜单中的“高级工具”访问 SCM 站点。

## <a name="next-steps"></a>后续步骤

请参阅以下链接，开始使用 Linux 应用服务。 如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [如何对用于容器的 Azure Web 应用使用自定义 Docker 映像](quickstart-custom-docker-image.md)
* [在用于容器的 Azure 应用服务 Web 应用中使用 .NET Core](quickstart-dotnetcore.md)
* [在用于容器的 Azure 应用服务 Web 应用中使用 Ruby](quickstart-ruby.md)
* [用于容器的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)
* [对用于容器的 Azure Web 应用的 SSH 支持](app-service-linux-ssh-support.md)
* [设置 Azure 应用服务中的过渡环境](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [使用用于容器的 Azure Web 应用进行 Docker 中心持续部署](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
