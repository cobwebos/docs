---
title: "Linux 应用服务简介 | Microsoft Docs"
description: "了解 Linux 应用服务"
keywords: "azure 应用服务, linux, oss"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: 78fbef5aa26c52f5dd264581fd1525ada763d4c6
ms.openlocfilehash: fa5d210c5e73074803d5cfb874ad7891563aefd0
ms.lasthandoff: 02/22/2017


---
# <a name="introduction-to-app-service-on-linux"></a>Linux 应用服务简介
Linux 上的 Azure 应用服务目前为公共预览版，支持在本地 Linux 上运行 Web 应用。

## <a name="overview"></a>概述
客户可以使用 Linux 应用服务在 Linux 本地针对受支持的应用程序堆栈托管 Web 应用。 以下部分列出了目前受支持的应用程序堆栈。 

## <a name="features"></a>功能
Linux 应用服务目前支持以下应用程序堆栈：

* Node.js
* PHP
* .NET Core
* Ruby

客户可通过以下几种方式部署其应用程序：

* FTP
* 本地 Git
* GitHub

针对应用程序规模进行调整：

* 客户可以通过更改应用服务计划中的层，增加或减少 Web 应用。
* 客户可以扩大应用程序，在其 SKU 范围内运行多个应用实例。

就 Kudu 来说，某些基本功能可用于以下几种情况：

* 环境
* 部署
* 基本控制台

## <a name="limitations"></a>限制
Azure 门户仅显示当前可用于 Linux 的应用服务的功能，隐藏其他功能。 当我们启用更多功能时，将会在门户中看到这些功能。

某些功能（例如虚拟网络集成、Azure Active Directory/第三方身份验证或 Kudu 站点扩展）不完整。 完成这些功能后，我们将更新文档和博客，以反映所做更改。

此公共预览版目前仅在以下区域提供：

* 美国西部
* 欧洲西部 
* 亚洲东南部

Linux Web 应用只能在专用应用服务计划中使用，没有免费层或共享层。 另外，常规 Web 应用和 Linux Web 应用的应用服务计划是互斥的，因此不能在非 Linux 应用服务计划中创建 Linux Web 应用。

在资源组中创建 Linux Web 应用时，该资源组不得在同一区域包含非 Linux Web 应用。

Linux 上的 Web 应用尚不支持从未编译的源部署 . NET Core 应用。 需要先在本地发布/编译 .NET Core 应用，然后将发布的站点位推送到应用。

## <a name="next-steps"></a>后续步骤
请参阅以下链接，开始使用 Linux 应用服务。 如有问题和疑问，请在[我们的论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上发帖。

* [在 Linux 应用服务中创建 Web 应用](app-service-linux-how-to-create-a-web-app.md)
* [如何在 Linux 上使用应用服务自定义 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux Web 应用中使用针对 Node.js 的 PM2 配置](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure 应用服务 Web 应用中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux Azure 应用服务 Web 应用中使用 Ruby](app-service-linux-using-ruby.md)
* [Linux 上的 Azure 应用服务 Web 应用常见问题](app-service-linux-faq.md)
