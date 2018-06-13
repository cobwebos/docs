---
title: 应用程序服务概述： Azure 堆栈 |Microsoft 文档
description: Azure 堆栈上的应用程序服务概述
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 1884574bcb39a1cbbd95b481adabdd06ebd499a9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386068"
---
# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 上的应用服务概述
*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈上的 azure App Service 是一种可供 Azure 堆栈的 Microsoft Azure 的平台作为-服务 (PaaS) 产品。 此服务，你的客户的内部或外部-创建 web、 API 和 Azure 函数为任何平台或设备的应用程序。 它们可以与本地应用程序集成你的应用程序并自动执行其业务流程。 Azure 堆栈云操作员可以在完全托管的虚拟机 (Vm) 上运行具有共享的 VM 资源或专用的 Vm 的自选客户应用。

Azure 应用程序服务包括用于自动执行业务流程和托管云 Api 的功能。 作为单个集成服务，Azure App Service 允许您编写各种组件 （网站、 RESTful Api 和业务流程） 构成单个解决方案。

## <a name="why-offer-azure-app-service-on-azure-stack"></a>为什么提供 Azure 堆栈上的 Azure App Service？

下面是应用服务的某些主要特性和功能：
- **多个语言和框架**： 应用程序服务具有对 ASP.NET、 Node.js、 Java、 PHP 和 Python 的一流支持。 你还可以在应用程序服务虚拟机上运行 Windows PowerShell 和其他脚本或可执行文件。
- **DevOps 优化**： 设置持续集成和与 GitHub、 本地 Git 或 BitBucket 部署。 将提升通过测试和过渡环境的更新。 通过使用 Azure PowerShell 或跨平台命令行界面 (CLI) 来管理你在 App Service 中的应用。
- **Visual Studio 集成**: Visual Studio 中的专用的工具简化创建和部署应用程序的工作。

## <a name="app-types-in-app-service"></a>应用服务中的应用类型

应用程序服务提供几种应用程序类型，其中每个旨在承载特定工作负荷：

- [Web 应用](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview)用于托管网站和 web 应用程序。
- [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform)托管 RESTful Api。
- 用于托管事件驱动的无服务器工作负荷的 azure 函数。

Word 应用此处是指专用于运行工作负荷的托管资源。 以“Web 应用”为例，用户可能习惯于将 Web 应用视为计算资源和应用程序代码，二者共同向浏览器提供功能。 但在 App Service web 应用程序是 Azure 堆栈提供用于承载应用程序代码的计算资源。

你的应用程序可能包括不同类型的多个 App Service 应用。 例如，如果你的应用程序组成一个 web 前端和 RESTful API 后端，你可以：
- 将该前端和 API 部署到单个 Web 应用
- 将前端代码部署到 Web 应用，将后端代码部署到 API 应用。

   ![](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>什么是应用服务计划?

App Service 资源提供程序使用 Azure App Service 使用相同的代码。 因此，某些基本的概念是值得描述。 在 App Service 应用程序的定价容器称为 App Service 计划。 它表示的一套用来保存你的应用程序的专用虚拟机。 在给定的订阅，你可具有多个 App Service 计划。

在 Azure 中，有共享和专用辅助角色。 共享工作线程支持高密度的多租户应用程序托管，并且没有只有一组共享辅助角色。 专用的服务器都使用只有一个租户，有三种大小： 小型、 中型和大型。 在本地客户需求始终不能使用这些条款的描述。 在 Azure 堆栈上的 App Service，资源提供程序管理员可以定义他们想要提供辅助角色层。 根据托管自己独特需求，可以定义的共享辅助角色的多个集或不同的专用辅助角色集。 通过使用这些辅助角色层定义，它们随后可以定义自己的定价 Sku。

## <a name="portal-features"></a>门户功能

Azure 堆栈上的应用程序服务使用同一 UI Azure App Service 使用，如适用于后端。 某些功能将被禁用，不 Azure 堆栈中的功能。 尚未可用 Azure 堆栈中的特定于 Azure 的预期或这些功能都需要的服务。

## <a name="next-steps"></a>后续步骤


- [之前开始使用 Azure 堆栈上的 App Service](azure-stack-app-service-before-you-get-started.md)
- [安装 App Service 资源提供程序](azure-stack-app-service-deploy.md)

你还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)、 like [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)和[MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)。
