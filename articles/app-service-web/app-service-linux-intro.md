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
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 69156ec555b34d066a65bdc202267cfc53de47a0
ms.contentlocale: zh-cn
ms.lasthandoff: 08/17/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Linux 上的 Azure Web 应用简介

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概述
客户可以使用 Linux Web 应用在 Linux 本地为受支持的应用程序堆栈托管 Web 应用。 以下部分列出了目前受支持的应用程序堆栈。 

## <a name="features"></a>功能
Linux Web 应用目前支持以下应用程序堆栈：

* Node.js
    * 4.4
    * 4.5
    * 6.2
    * 6.6
    * 6.9
    * 6.10
* PHP
    * 5.6
    * 7.0
* .NET Core
    * 1.0
    * 1.1
* Ruby
    * 2.3

客户可通过以下几种方式部署其应用程序：

* FTP
* 本地 Git
* GitHub
* Bitbucket

针对应用程序规模进行调整：

* 客户可以通过更改应用服务计划的层，增加或减少 Web 应用
* 客户可以扩大应用程序，在其 SKU 范围内运行多个应用实例

就 Kudu 来说，有一些基本功能：

* 环境
* 部署
* 基本控制台
* SSH

对于 DevOps：

* 过渡环境
* ACR 和 DockerHub CI/CD

## <a name="limitations"></a>限制
Azure 门户仅显示当前可用于 Linux Web 应用的功能，隐藏其他功能。 当我们启用更多功能时，会在门户中看到这些功能。

某些功能（例如虚拟网络集成、Azure Active Directory/第三方身份验证或 Kudu 站点扩展）尚不可用。 这些功能可用后，我们将更新文档和博客，以反映所做更改。

此公共预览版目前仅在以下区域提供：

* 美国西部
* 美国东部
* 欧洲西部
* 欧洲北部
* 美国中南部
* 美国中北部
* 东南亚
* 东亚
* 澳大利亚东部
* 日本东部
* 巴西南部
* 印度南部

Linux Web 应用只能在专用应用服务计划中使用，没有免费层或共享层。 另外，常规 Web 应用和 Linux Web 应用的应用服务计划是互斥的，因此不能在非 Linux 应用服务计划中创建 Linux Web 应用。

在资源组中创建 Linux Web 应用时，该资源组不得在同一区域包含非 Linux Web 应用。

## <a name="next-steps"></a>后续步骤
请参阅以下链接，开始使用 Linux 应用服务。 如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [在 Linux 上的 Azure Web 应用中创建 Web 应用](app-service-linux-how-to-create-web-app.md)
* [如何使用 Linux 上 Azure Web 应用的自定义 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux 上的 Azure Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure 应用服务 Web 应用中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure 应用服务 Web 应用中使用 Ruby](app-service-linux-ruby-get-started.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题解答](app-service-linux-faq.md)
* [SSH 对 Linux 上的 Azure Web 应用的支持](./app-service-linux-ssh-support.md)
* [设置 Azure 应用服务中的过渡环境](./web-sites-staged-publishing.md)
* [使用 Linux 上的 Azure Web 应用进行 Docker 中心持续部署](./app-service-linux-ci-cd.md)


