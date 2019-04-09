---
title: Linux 应用服务简介 - Azure | Microsoft Docs
description: 了解 Linux 上的 Azure 应用服务。
keywords: azure 应用服务, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 22c4096711bbc1d47ff6684e38ac829d77681a9f
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793435"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Linux 上的 Azure 应用服务简介

[Azure 应用服务](../overview.md)是一个完全托管的计算平台，非常适用于托管网站和 Web 应用程序。 客户可以使用 Linux 应用服务在 Linux 本地针对受支持的应用程序堆栈托管 Web 应用。 [语言](#languages)部分列出了目前受支持的应用程序堆栈。

## <a name="languages"></a>Languages

Linux 应用服务支持大量内置映像，以便提高开发人员工作效率。 如果内置映像不支持应用程序所需的运行时，请按照[生成自己的 Docker 映像](tutorial-custom-docker-image.md)中的说明将其部署到用于容器的 Web 应用。

| 语言 | 支持的版本 |
|---|---|
| Node.js | 4.4、4.5、4.8、6.2、6.6、6.9、6.10、6.11、8.0、8.1、8.2、8.8、8.9、8.11、9.4、10.1、10.10 |
| Java * | Tomcat 8.5、9.0，Java SE，WildFly 14（全都运行 JRE 8） |
| PHP | 5.6, 7.0, 7.2 |
| Python（预览版） | 2.7、3.6、3.7 |
| .NET Core | 1.0、1.1、2.0、2.1 |
| Ruby | 2.3 |

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

* 客户可以通过更改[应用服务计划](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)的层，增加或减少 Web 应用

## <a name="locations"></a>位置

查看 [Azure 状态仪表板](https://azure.microsoft.com/status)。

## <a name="limitations"></a>限制

Azure 门户仅显示当前对用于容器的 Web 应用有效的功能。 当我们启用更多功能时，会在门户中看到这些功能。

Linux 上的应用服务仅支持[基本、标准和高级](https://azure.microsoft.com/pricing/details/app-service/plans/)应用服务计划，没有[免费或共享](https://azure.microsoft.com/pricing/details/app-service/plans/)层。 不能在已经托管非 Linux Web 应用的应用服务计划中创建用于容器的 Web 应用。  

根据当前的限制，请勿在同一资源组中混合使用 Windows 和 Linux 应用。

## <a name="troubleshooting"></a>故障排除

如果未能启动应用程序或希望检查应用中的日志记录，请查看 LogFiles 目录中的 Docker 日志。 可通过 SCM 站点或 FTP 访问此目录。
若要从容器记录 `stdout` 和 `stderr`，需要在“诊断记录”下启用“Docker 容器记录”。

![启用日志记录][2]

设置立即生效。 应用服务会检测设置更改，并为你自动重启容器。

可从“开发工具”菜单中的“高级工具”访问 SCM 站点。

![使用 Kudu 查看 Docker 日志][1]

## <a name="next-steps"></a>后续步骤

以下文章将通过使用各种语言编写的 Web 应用让你开始使用 Linux 上的应用服务：

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [多容器应用](quickstart-multi-container.md)

有关 Linux 上的应用服务的详细信息，请参阅：

* [适用于 Linux 的应用服务常见问题解答](app-service-linux-faq.md)
* [对 Linux 上的应用服务的 SSH 支持](app-service-linux-ssh-support.md)
* [设置应用服务中的过渡环境](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker 中心持续部署](app-service-linux-ci-cd.md)

如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
