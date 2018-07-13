---
title: Azure 应用服务诊断概述 | Microsoft Docs
description: 了解如何使用应用服务诊断排查 Web 应用的问题。
keywords: 应用服务, azure 应用服务, 诊断, 支持, web 应用, 故障排除, 自助服务
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: 7ad205c75a02b496abe2cb910c7eb459cdb16c97
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969232"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure 应用服务诊断概述 

运行 Web 应用程序时，我们希望能够对出现的各种问题做好准备，例如，出现 500 错误，或者用户反映站点已关闭。 应用服务诊断是智能的交互式体验，可帮助我们排查 Web 应用的问题，且无需配置。 如果 Web 应用确实出现问题，应用服务诊断会指出问题所在，并引导我们获取适当的信息，以便更轻松快速地排查和解决问题。 
 
尽管此体验在 Web 应用过去 24 小时内出现问题时可发挥最大的作用，但是，我们随时可以使用所有诊断图形进行分析。 在右栏中可以找到其他故障排除工具，以及有用文档和论坛的链接。

应用服务诊断不但适用于 Windows 上的应用，也适用于 [Linux/容器](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)、[应用服务环境](https://docs.microsoft.com/azure/app-service/environment/intro)和 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 上的应用。 

## <a name="open-app-service-diagnostics"></a>打开应用服务诊断

若要访问应用服务诊断，请在 [Azure 门户](https://portal.azure.com)中导航到你的应用服务应用或应用服务环境。 在左侧导航栏中，单击“诊断并解决问题”。 

对于 Azure Functions，请导航到你的函数应用，在顶部的导航栏中，单击“平台功能”并从“监视”部分中选择“诊断并解决问题”。 

![主页](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>运行状况检查

如果不知道 Web 应用发生什么问题，或者不知道从何处着手排查问题，运行状况检查是一个很好的入手点。 运行状况检查将会分析 Web 应用程序，提供简明的交互式概述，指出哪个组件正常、哪个组件出错，并告知要从哪个位置调查问题。 使用其智能交互式界面可逐步完成故障排除的过程。  

![运行状况检查](./media/app-service-diagnostics/HealthCheckup2.png)

如果在过去 24 小时内检测到属于特定问题类别的问题，可以查看完整的诊断报告。应用服务诊断可以通过引导性更强的体验提示查看更多的故障排除建议和后续措施。

![故障排除和后续措施](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>磁贴快捷方式

如果你确切地知道需要查找哪种类型的故障排除信息，磁贴快捷方式会将你直接转到所需问题类别的完整诊断报告。 与运行状况检查相比，磁贴快捷方式更直接，但访问诊断指标时，其引导性较差。 作为磁贴快捷方式的一部分，还可以在此处找到**诊断工具**，它们是更高级的工具，可帮助调查与应用程序代码问题、速度缓慢、连接字符串以及其他内容相关的问题。 

![磁贴快捷方式](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>诊断报告

无论是在运行[运行状况检查](#health-checkup)之后想要查看详细信息，还是单击了某个[磁贴快捷方式](#tile-shortcuts)，完整诊断报告都会显示过去 24 小时内的相关图形化指标。 如果应用出现任何停机，时间线下面将以橙色条形的方式呈现此故障。 可以选择橙色条形之一来选择故障时间以查看与该故障时间相关的观测数据以及建议的故障排除步骤。 

![诊断报告](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>调查应用程序代码问题

因为许多应用问题与应用程序代码中的问题相关，所以应用服务诊断集成了 [Application Insights](https://azure.microsoft.com/services/application-insights/) 来突出显示异常和依赖项问题，以便与所选故障时间进行关联。 Application Insights 无需单独启用。 

若要查看 Application Insights 异常和依赖项，请选择“Web 应用故障”或“Web 应用速度缓慢”磁贴快捷方式。 

![Application insights](./media/app-service-diagnostics/AppInsights6.png)

