---
title: Azure Stack 概述上的应用服务 |Microsoft Docs
description: Azure Stack 上的应用服务概述
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: 3015755d8046f72c03cd1a1485d90a7f720a8909
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353010"
---
# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 上的应用服务概述

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 上的 azure 应用服务是一种可用于 Azure Stack 的 Microsoft Azure 的平台-作为-服务 (PaaS) 产品。 该服务，能够在内部或外部客户，若要创建 web、 API 和 Azure Functions 为任何平台或设备的应用程序。 他们可以将你的应用与本地应用程序集成，并可实现业务流程的自动化。 Azure Stack 云操作员可以在完全托管的虚拟机 (VM) 上，使用他们选择的共享 VM 资源或专用 VM 运行客户的应用。

Azure 应用服务，可自动执行业务流程和托管云 Api。 作为单个集成服务，Azure 应用服务可让你组合各种组件，例如网站、 REST Api 和业务流程，构成单个解决方案。

## <a name="why-offer-azure-app-service-on-azure-stack"></a>为什么在 Azure Stack 上提供 Azure 应用服务？

下面是应用服务的某些主要特性和功能：

- **多种语言和框架**：应用服务为 ASP.NET、Node.js、Java、PHP 和 Python 提供一流支持。 也可以在应用服务 VM 上运行 Windows PowerShell 和其他脚本或可执行文件。
- **DevOps 优化**：使用 GitHub、本地 Git 或 BitBucket 设置持续集成和部署。 可以提升更新通过测试和过渡环境，并使用 Azure PowerShell 或跨平台命令行接口 (CLI) 管理应用服务中的应用。
- **Visual Studio 集成**：Visual Studio 中的专用工具可简化创建和部署应用程序的工作。

## <a name="app-types-in-app-service"></a>应用服务中的应用类型

应用服务提供多种应用类型，每种类型负责托管特定的工作负荷：

- [Web 应用](../app-service/app-service-web-overview.md)用于托管网站和 Web 应用程序。
- [API 应用](../app-service/app-service-web-overview.md)托管 REST Api。
- Azure Functions 用于托管事件驱动的无服务器工作负荷。

单词*应用*指的是专用于运行工作负荷的托管资源。 采取*web 应用*作为示例，你可能习惯于认为 web 应用的计算资源和应用程序代码的浏览器该组合在一起交付的功能。 在应用服务中 web 应用是 Azure Stack 提供的用于托管应用程序代码的计算资源。

应用程序可以由多个不同类型的应用服务应用组成。 例如，如果你的应用程序组成的 web 前端和 REST API 后端，您可以：

- 将该前端和 API 部署到单个 Web 应用
- 将前端代码部署到 Web 应用，将后端代码部署到 API 应用。

   [ ![使用监视数据的应用服务概述](media/azure-stack-app-service-overview/image01.png "使用监视数据的应用服务概述") ](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>什么是应用服务计划?

应用服务资源提供程序使用相同的代码的 Azure 应用服务使用，并因此共享某些常见概念。 在应用服务应用程序的定价容器称为*应用服务计划*。 它表示一组用来保存应用的专用虚拟机。 在一个给定订阅中，你可以有多个应用服务计划。

在 Azure 中，有共享辅助角色和专用辅助角色。 共享辅助角色支持高密度的多租户应用托管，并且没有只有一组共享辅助角色。 专用服务器只由一个租户使用，有三种大小：小型、中型和大型。 使用这些术语并非总是能够描述本地客户的需求。 在 Azure Stack 上的应用服务中，资源提供程序管理员可以定义他们想要提供的辅助角色层。 根据自己的独特托管需求，你可以定义多组共享辅助角色或不同组的专用辅助角色。 使用这些辅助角色层定义，他们随后可以定义自己的定价 SKU。

## <a name="portal-features"></a>门户功能

Azure Stack 上的应用服务使用 Azure 应用服务使用相同的用户界面。 这同样适用与后端。 但是，在 Azure Stack 中将禁用某些功能。 特定于 Azure 的预期或这些功能需要的服务当前不可在 Azure Stack 中。

## <a name="next-steps"></a>后续步骤

- [开始使用 Azure Stack 上的应用服务之前](azure-stack-app-service-before-you-get-started.md)
- [安装应用服务资源提供程序](azure-stack-app-service-deploy.md)

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-tools-paas-services.md)，如 [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)和 [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)。
