---
title: 应用服务概述：Azure Stack | Microsoft Docs
description: Azure Stack 上的应用服务概述
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
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 42258a352b05d97341f20c13bf837e1398dd8979
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099956"
---
# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 上的应用服务概述

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈上的 azure App Service 是一种可供 Azure 堆栈的 Microsoft Azure 的平台作为-服务 (PaaS) 产品。 此服务允许你的客户（内部或外部）为任何平台或设备创建 Web 应用、API 应用和 Azure Functions 应用程序。 他们可以将你的应用与本地应用程序集成，并可实现业务流程的自动化。 Azure Stack 云操作员可以在完全托管的虚拟机 (VM) 上，使用他们选择的共享 VM 资源或专用 VM 运行客户的应用。

Azure App Service，可自动执行业务流程和宿主云 Api。 作为单个集成服务，Azure App Service 允许您组合各种组件 （网站、 RESTful Api 和业务流程） 构成单个解决方案。

## <a name="why-offer-azure-app-service-on-azure-stack"></a>为什么在 Azure Stack 上提供 Azure 应用服务？

下面是应用服务的某些主要特性和功能：

- **多种语言和框架**：应用服务为 ASP.NET、Node.js、Java、PHP 和 Python 提供一流支持。 也可以在应用服务 VM 上运行 Windows PowerShell 和其他脚本或可执行文件。
- **DevOps 优化**：使用 GitHub、本地 Git 或 BitBucket 设置持续集成和部署。 通过测试和过渡环境提升更新。 在应用服务中，使用 Azure PowerShell 或跨平台命令行接口 (CLI) 来管理应用。
- **Visual Studio 集成**：Visual Studio 中的专用工具可简化创建和部署应用程序的工作。

## <a name="app-types-in-app-service"></a>应用服务中的应用类型

应用服务提供多种应用类型，每种类型负责托管特定的工作负荷：

- [Web 应用](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview)用于托管网站和 Web 应用程序。
- [API 应用](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform)用于托管 RESTful API。
- Azure Functions 用于托管事件驱动的无服务器工作负荷。

此处的“应用”一词是指专用于运行工作负荷的托管资源。 以“Web 应用”为例，用户可能习惯于将 Web 应用视为计算资源和应用程序代码，二者共同向浏览器提供功能。 但在应用服务中，Web 应用是 Azure Stack 提供的用于托管应用程序代码的计算资源。

应用程序可以由多个不同类型的应用服务应用组成。 例如，如果应用程序由 Web 前端和 RESTful API 后端组成，则可以：

- 将该前端和 API 部署到单个 Web 应用
- 将前端代码部署到 Web 应用，将后端代码部署到 API 应用。

   ![使用监视数据的应用程序服务概述](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>什么是应用服务计划?

应用服务资源提供程序使用 Azure 应用服务使用的相同代码。 因此，某些常见概念有必要进行说明。 在应用服务中，应用程序的定价容器称为应用服务计划。 它表示一组用来保存应用的专用虚拟机。 在一个给定订阅中，你可以有多个应用服务计划。

在 Azure 中，有共享辅助角色和专用辅助角色。 共享工作线程支持高密度的多租户应用程序托管，并且没有只有一组共享辅助角色。 专用服务器只由一个租户使用，有三种大小：小型、中型和大型。 使用这些术语并非总是能够描述本地客户需求。 在 Azure Stack 上的应用服务中，资源提供程序管理员可以定义他们想要提供的辅助角色层。 根据自己的独特托管需求，你可以定义多组共享辅助角色或不同组的专用辅助角色。 使用这些辅助角色层定义，他们随后可以定义自己的定价 SKU。

## <a name="portal-features"></a>门户功能

Azure 堆栈上的应用程序服务使用 Azure App Service 使用同一 UI，情况也是如此是后端。 但是，某些功能处于禁用状态，并且不是 Azure 堆栈中的功能。 特定于 Azure 的期望或这些功能都需要的服务当前不可用 Azure 堆栈中。

## <a name="next-steps"></a>后续步骤

- [开始使用 Azure Stack 上的应用服务之前](azure-stack-app-service-before-you-get-started.md)
- [安装应用服务资源提供程序](azure-stack-app-service-deploy.md)

你还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)，如[SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)和[MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)。
