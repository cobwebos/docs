---
title: Linux 应用服务简介 | Microsoft Docs
description: 了解 Linux 上的 Azure 应用服务。
keywords: azure 应用服务, linux, oss
services: app-service
documentationcenter: ''
author: naziml
manager: cfowler
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: c9c4cb42e6bf9f60c883242fad6963f72b772063
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Linux 上的 Azure 应用服务简介

[Web 应用](../app-service-web-overview.md)是一个完全托管的计算平台，非常适合用来托管网站和 Web 应用程序。 客户可以使用 Linux 应用服务在 Linux 本地针对受支持的应用程序堆栈托管 Web 应用。 以下部分列出了目前受支持的应用程序堆栈。

## <a name="languages"></a>语言

Linux 应用服务支持大量内置映像，以便提高开发人员工作效率。 如果内置映像不支持应用程序所需的运行时，请按照[生成自己的 Docker 映像](tutorial-custom-docker-image.md)中的说明将其部署到用于容器的 Web 应用。

| 语言 | 支持的版本 |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 9.4 |
| Java * | 8.0 |
| PHP | 5.6, 7.0, 7.2 |
| .NET Core | 1.0, 1.1, 2.0 |
| Ruby | 2.3 |
| Go | 1.0 |
| Apache Tomcat | 8.5, 9.0 |

如需更多详细信息，请参阅[在基于 Linux 的应用服务中创建 Java Web 应用](https://docs.microsoft.com/azure/app-service/containers/quickstart-java)。

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

Linux 应用服务仅支持[基本和标准](https://azure.microsoft.com/pricing/details/app-service/plans/)应用服务计划，没有[免费或共享](https://azure.microsoft.com/pricing/details/app-service/plans/)层。 不能在已经托管非 Linux Web 应用的应用服务计划中创建用于容器的 Web 应用。

## <a name="troubleshooting"></a>故障排除

如果未能启动应用程序或希望检查应用中的日志记录，请查看 LogFiles 目录中的 Docker 日志。 可通过 SCM 站点或 FTP 访问此目录。
若要从容器记录 `stdout` 和 `stderr`，需要在“诊断记录”下启用“Docker 容器记录”。

![启用日志记录][2]

![使用 Kudu 查看 Docker 日志][1]

可从“开发工具”菜单中的“高级工具”访问 SCM 站点。

## <a name="next-steps"></a>后续步骤

请参阅以下链接，开始使用 Linux 应用服务。 如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [如何对用于容器的 Web 应用使用自定义 Docker 映像](quickstart-docker-go.md)
* [在 Linux 上的 Azure 应用服务中使用 .NET Core](quickstart-dotnetcore.md)
* [在 Linux 上的 Azure 应用服务中使用 Ruby](quickstart-ruby.md)
* [用于容器的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)
* [对 Linux 上的 Azure 应用服务的 SSH 支持](app-service-linux-ssh-support.md)
* [设置 Azure 应用服务中的过渡环境](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [使用用于容器的 Web 应用进行 Docker 中心持续部署](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
