---
title: "解决使用 Application Insights 的云服务 |Microsoft 文档"
description: "了解如何从 Azure 诊断处理的数据使用 Application Insights 云服务问题进行故障排除。"
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>解决使用 Application Insights 的云服务
与[Azure SDK 2.8](https://azure.microsoft.com/downloads/)和 Azure 诊断扩展 1.5，你可以 Azure 诊断数据的你的云服务将直接发送到 Application Insights。 由 Azure 诊断收集的日志&mdash;包括应用程序日志、 Windows 事件日志、 ETW 日志和性能计数器&mdash;可以发送到 Application Insights。 然后可以实现可视化的 Application Insights 门户 UI 中的此信息。 然后可以使用 Application Insights SDK 来深入指标和来自你的应用程序，以及系统和来自 Azure 诊断的基础结构级数据的日志。

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>配置 Azure 诊断以将数据发送到 Application Insights
请按照下列步骤设置你的云服务项目，以将 Azure 诊断数据发送到 Application Insights。

1. 在 Visual Studio 解决方案资源管理器，右键单击角色并选择**属性**打开角色设计器。

    ![解决方案资源管理器角色属性][1]

2. 在**诊断**一部分角色设计器中，选择**将诊断数据发送到 Application Insights**选项。

    ![在角色设计器将诊断数据发送到 application insights][2]

3. 在弹出对话框中，选择你想要发送到的 Azure 诊断数据的 Application Insights 资源。 对话框允许您从你的订阅中选择一个现有的 Application Insights 资源或手动指定 Application Insights 资源检测密钥。 有关创建 Application Insights 资源的详细信息，请参阅[创建新的 Application Insights 资源](../application-insights/app-insights-create-new-resource.md)。

    ![选择应用程序 insights 资源][3]

    一旦你已添加 Application Insights 资源，为服务配置设置具有名称存储该资源的检测密钥**APPINSIGHTS_INSTRUMENTATIONKEY**。 你可以更改此配置设置为每个服务配置或环境。 为此，请选择从一种配置**服务配置**列出并指定该配置新检测密钥。

    ![选择服务配置][4]

    **APPINSIGHTS_INSTRUMENTATIONKEY** Visual Studio 使用配置设置以在发布过程的相应 Application Insights 资源信息配置诊断扩展。 配置设置为定义的不同服务配置的不同检测密钥的简便方法。 Visual Studio 将转换该设置，并将其插入到在发布过程中的诊断扩展公共配置。 为了简化使用 PowerShell 配置诊断扩展的过程，Visual Studio 的程序包输出还包含相应的 Application Insights 检测密钥的公共配置 XML。 公共配置文件扩展文件夹中创建并遵循模式*PaaSDiagnostics。&lt;RoleName&gt;。PubConfig.xml*。 任何基于 PowerShell 的部署可以使用此模式将每个配置映射到角色。

4) 若要将 Azure 诊断发送其所有性能计数器和 Application Insights 到由 Azure 诊断代理收集的错误级日志的配置，启用**将诊断数据都发送到 Application Insights**选项。 

    如果你想要进一步配置哪些数据发送到 Application Insights，必须手动编辑*diagnostics.wadcfgx*为每个角色的文件。 请参阅[配置 Azure 诊断以将数据发送到 Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights)若要了解有关手动更新配置的详细信息。

当云服务配置为将 Azure 诊断数据发送到 application insights 时，你可以将其部署到 Azure 通常情况下，确保已启用 Azure 诊断扩展。 有关详细信息，请参阅[发布云服务使用 Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md)。  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>在 Application Insights 中查看 Azure 诊断数据
Azure 诊断遥测将显示在配置云服务的 Application Insights 资源中。

Azure 诊断日志类型映射到通过以下方式的 Application Insights 概念：

* 性能计数器显示为 Application Insights 中的自定义指标。
* Windows 事件日志如下所示跟踪和 Application Insights 中的自定义事件。
* 应用程序日志、 ETW 日志和任何诊断基础结构日志如下所示 Application Insights 中的跟踪。

若要查看在 Application Insights 中的 Azure 诊断数据，请执行下列其中一项操作：

* 使用[指标资源管理器](../application-insights/app-insights-metrics-explorer.md)要可视化的任何自定义性能计数器或的 Windows 事件日志事件的不同类型的计数。

    ![在度量值资源管理器的自定义度量值][5]

* 使用[搜索](../application-insights/app-insights-diagnostic-search.md)若要搜索整个 Azure 诊断所发送的跟踪日志。 例如，如果未经处理的异常导致角色崩溃，回收，有关异常的信息显示在*应用程序*通道*Windows 事件日志*。 可以使用搜索以查看 Windows 事件日志错误，并获取来帮助查找问题的原因的异常的完整堆栈跟踪。

    ![搜索跟踪][6]

## <a name="next-steps"></a>后续步骤
* [将 Application Insights SDK 添加到你的云服务](../application-insights/app-insights-cloudservices.md)从你的应用程序发送有关请求、 异常、 依赖关系，以及任何自定义遥测数据。 结合使用时使用 Azure 诊断数据，此信息可以获取应用程序和系统，全部操作都在同一 Application Insight 资源的完整的视图。  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
