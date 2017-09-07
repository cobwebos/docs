---
title: "Linux 上的 Azure Web 应用简介 | Microsoft Docs"
description: "了解 Linux 上的 Azure Web 应用。"
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
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: bbe2e0bafe48b39f12c5a8a46511e9275926d4b6
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Linux 上的 Azure Web 应用简介

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概述
[Web 应用](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview)是一个完全托管的计算平台，非常适合用来托管网站和 Web 应用程序。 客户可以使用 Linux Web 应用在 Linux 本地为受支持的应用程序堆栈托管 Web 应用。 以下部分列出了目前受支持的应用程序堆栈。 

### <a name="languages"></a>语言

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

### <a name="deployments"></a>部署

* FTP
* 本地 Git
* GitHub
* Bitbucket

### <a name="devops"></a>DevOps

* 过渡环境
* [Azure 容器注册表](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-intro)和 DockerHub CI/CD

### <a name="console-publishing-and-debugging"></a>控制台、发布和调试

* 环境
* 部署
* 基本控制台
* SSH

### <a name="scaling"></a>扩展

* 客户可以通过更改[应用服务计划](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)的层，增加或减少 Web 应用

## <a name="limitations"></a>限制
Azure 门户仅显示当前可用于 Linux Web 应用的功能。 当我们启用更多功能时，会在门户中看到这些功能。

某些功能（例如虚拟网络集成、Azure Active Directory/第三方身份验证或 Kudu 站点扩展）尚不可用。 这些功能可用后，我们将更新文档和博客，以反映所做更改。

此公共预览版目前仅在以下区域提供：

|美洲|亚太区|欧洲|
|:----------------:|:--------------:|:------------:|
|巴西南部|澳大利亚东部|欧洲北部|
|美国东部|东亚|欧洲西部|
|美国中北部|日本东部||
|美国中南部|印度南部||
|美国西部|东南亚||

检查 [Azure 状态仪表板](https://azure.microsoft.com/status)，以查看其他可用区域。

Linux Web 应用仅支持[基本、标准和高级](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/)应用服务计划，没有[免费或共享](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/)层。 以下也是有关 Linux Web 应用的重要限制：

* 不能在已经托管非 Linux Web 应用的应用服务计划中创建 Linux Web 应用。
* 在包含非 Linux Web 应用的资源组中创建 Linux Web 应用时，必须在不同于现有应用服务计划的区域中创建应用服务计划。

## <a name="troubleshooting"></a>故障排除 ##

如果未能启动应用程序或希望检查应用中的日志记录，请查看 LogFiles 目录中的 Docker 日志。 可通过 SCM 站点或 FTP 访问此目录。
若要从容器记录 `stdout` 和 `stderr`，需要在“诊断记录”下启用“Docker 容器记录”。

![启用日志记录][2]

![使用 Kudu 查看 Docker 日志][1]

可从“开发工具”菜单中的“高级工具”访问 SCM 站点。

## <a name="next-steps"></a>后续步骤
请参阅以下链接，开始使用 Linux 应用服务。 如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [如何使用 Linux 上 Azure Web 应用的自定义 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure 应用服务 Web 应用中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure 应用服务 Web 应用中使用 Ruby](app-service-linux-ruby-get-started.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)
* [SSH 对 Linux 上的 Azure Web 应用的支持](./app-service-linux-ssh-support.md)
* [设置 Azure 应用服务中的过渡环境](./web-sites-staged-publishing.md)
* [使用 Linux 上的 Azure Web 应用进行 Docker 中心持续部署](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

