---
title: "Azure 应用服务的工作方式"
description: "了解应用服务的工作方式"
keywords: "应用服务, azure 应用服务, 缩放, 可缩放, 应用服务计划, 应用服务成本"
services: app-service
documentationcenter: 
author: yochay
manager: erikre
editor: 
ms.assetid: ae74fc32-969e-4580-8d61-02c922f1f184
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/23/2017
ms.author: yochayk
ms.translationtype: Human Translation
ms.sourcegitcommit: edb3325414adf876548181243ddfa2d515aeb0b8
ms.openlocfilehash: 2d830963d3d2adba71a6ca99f79eac0fc8cbfb12
ms.contentlocale: zh-cn
ms.lasthandoff: 02/24/2017


---
# <a name="how-app-service-works"></a>应用服务的工作方式
Azure 应用服务是一项云服务，旨在解决工程师目前面临的实际问题。
应用服务主要考虑如何大幅提高开发人员的工作效率，同时又不影响云级别的应用程序交付需求。 

应用服务还提供创建企业业务线应用程序所需的功能和框架。 凭借应用服务，可使用最常用的开发语言（包括 Java、PHP、Node.js、Python 和 Microsoft.NET 语言）开发应用。 通过应用服务，可以：

* 构建高度可缩放的 Web 应用。
* 通过一组易用的移动功能（例如数据后端、用户身份验证和推送通知），快速构建移动应用后端。
* 通过 API 应用实现、部署和发布 API。
* 通过逻辑应用将业务应用程序组合到工作流中并转换数据。

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

所有应用类型都依赖于可缩放的、灵活的 Web 应用平台，开发人员可利用该平台优化从应用设计到应用维护的完整生命周期体验。 生命周期功能实现以下功能：

* **快速创建应用**。 从头开始创建，或者从 Azure Marketplace 中选取运营支持系统 (OSS) 包。
* **连续部署**。 从常用源代码管理解决方案（例如 TFS、GitHub 和 Bitbucket）自动部署新代码，通过在线存储服务（例如 OneDrive 和 Dropbox）同步内容。
* **在生产中测试**。 顺利创建预生产环境，管理流向其中的流量。 根据需要在云中进行调试，发现问题时回退即可。
* **运行异步任务和批处理作业**。 在后台进程中执行代码，或根据事件（例如在 Azure 存储队列中排队的消息）和计划的时间 (CRON) 激活代码。
* **缩放应用**。 使用多个选项之一，根据流量和资源使用情况自动对服务进行水平缩放和垂直缩放。 配置专用环境，专用于用户的应用。   
* **维护应用**。 使用多个调试和诊断功能来预测问题，并通过自动修复和实时调试等功能来实时、有效地解决它们，或者在事后通过分析日志和内存转储予以解决。

总而言之，应用服务功能可以让开发人员专注于代码，并快速达到一个稳定且高度可扩展的生产状态。 利用 API 应用和逻辑应用功能，开发人员可以构建实际的企业应用程序，顺利实现业务解决方案从本地到云的集成。 

## <a name="videos"></a>视频
* [Azure App Service 体系结构](https://azure.microsoft.com/documentation/videos/why-azure-web-sites-plus-architecture/)

## <a name="next-steps"></a>后续步骤

若要了解有关应用服务的详细信息，请参阅以下主题：

* [什么是 Azure App Service？](app-service-value-prop-what-is.md)
  * [Web 应用](../app-service-web/app-service-web-overview.md)
  * [移动应用](../app-service-mobile/app-service-mobile-value-prop.md)
  * [API 应用](../app-service-api/app-service-api-apps-why-best-platform.md)
* [Azure App Service 体系结构（演示文稿）](http://www.slideshare.net/maartenba/windows-azure-web-sites-things-they-dont-teach-kids-in-school-comunity-day-2013)
* [Azure App Service、云服务和虚拟机比较](../app-service-web/choose-web-site-cloud-service-vm.md)
* [了解应用服务计划](azure-web-sites-web-hosting-plans-in-depth-overview.md)
* [App Service 环境简介](../app-service-web/app-service-app-service-environment-intro.md)
  * [练习：创建应用服务环境](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Azure App Service 开发堆栈支持](https://azure.microsoft.com/blog/windows-azure-websites-development-stacks-support/)




