---
title: "使用 Application Insights 排查云服务问题 | Microsoft 文档"
description: "了解如何通过使用 Application Insights 来处理 Azure 诊断发送的数据，以便排查云服务问题。"
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>使用 Application Insights 排查云服务问题
你可以使用 [Azure SDK 2.8](https://azure.microsoft.com/downloads/) 和 Azure 诊断扩展模块 1.5，将云服务的 Azure 诊断数据直接发送到 Application Insights。 可以将由 Azure 诊断收集的日志（&mdash;包括应用程序日志、Windows 事件日志、ETW 日志和性能计数器&mdash;）发送到 Application Insights。 然后，你可以在 Application Insights 门户 UI 中可视化此信息。 接着，你可以使用 Application Insights SDK 深入分析来自应用程序和系统的指标与日志，以及来自 Azure 诊断的基础结构级别的数据。

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>配置 Azure 诊断以将数据发送到 Application Insights
请遵循以下步骤设置云服务项目，以将 Azure 诊断数据发送到 Application Insights。

1. 在 Visual Studio 解决方案资源管理器中，右键单击某个角色，然后选择“属性”以打开角色设计器。

    ![解决方案资源管理器角色属性][1]

2. 在角色设计器的”诊断“部分，选中“将诊断数据发送到 Application Insights”选项。

    ![角色设计器将诊断数据发送到 Application Insights][2]

3. 在弹出的对话框中，选择要向其发送 Azure 诊断数据的 Application Insights 资源。 该对话框允许你从订阅中选择现有 Application Insights 资源，或为 Application Insights 资源手动指定检测密钥。 有关创建 Application Insights 资源的详细信息，请参阅[创建新的 Application Insights 资源](../application-insights/app-insights-create-new-resource.md)。

    ![选择 Application Insights 资源][3]

    添加 Application Insights 资源后，该资源的检测密钥将存储为名为 **APPINSIGHTS_INSTRUMENTATIONKEY** 的服务配置设置。 你可以更改每项服务配置或环境的配置设置。 为此，请从“服务配置”列表中选择不同的配置，并为该配置指定新的检测密钥。

    ![选择服务配置][4]

    在发布期间，Visual Studio 将使用 **APPINSIGHTS_INSTRUMENTATIONKEY** 配置设置来配置诊断扩展模块及相应的 Application Insights 资源信息。 配置设置是为不同服务配置定义不同检测密钥的便利方式。 发布过程中，Visual Studio 将转换该设置，并将其插入诊断扩展模块公共配置。 为了简化使用 PowerShell 配置诊断扩展的过程，Visual Studio 的程序包输出还包含公共配置 XML，以及相应的 Application Insights 检测密钥。 公共配置文件在“扩展”文件夹中创建，并遵循模式 PaaSDiagnostics.&lt;RoleName&gt;.PubConfig.xml。 任何基于 PowerShell 的部署都可以使用此模式将每个配置映射到角色。

4) 要将 Azure 诊断配置为向 Application Insights 发送 Azure 诊断代理收集的所有性能计数器和错误级别日志，请启用“将诊断数据发送到 Application Insights”选项。 

    如果想进一步配置要将哪些数据发送到 Application Insights，则必须手动编辑每个角色的 diagnostics.wadcfgx 文件。 要了解有关手动更新配置的详细信息，请参阅[配置 Azure 诊断以将数据发送到 Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights)。

将云服务配置为向 Application Insights 发送 Azure 诊断数据后，可按常规操作将云服务部署到 Azure，并确保 Azure 诊断扩展已启用。 有关详细信息，请参阅[使用 Visual Studio 发布云服务](../vs-azure-tools-publishing-a-cloud-service.md)。  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>在 Application Insights 中查看 Azure 诊断数据
Azure 诊断遥测数据会显示在为云服务配置的 Application Insights 资源中。

Azure 诊断日志类型以下列方式映射到 Application Insights 概念：

* 性能计数器在 Application Insights 中显示为自定义指标。
* Windows 事件日志在 Application Insights 中显示为跟踪和自定义事件。
* 应用程序日志、ETW 日志和任何诊断基础结构日志在 Application Insights 中显示为跟踪。

若要在 Application Insights 中查看 Azure 诊断数据，请执行以下操作之一：

* 使用[指标资源管理器](../application-insights/app-insights-metrics-explorer.md)可视化任何自定义性能计数器，或不同类型的 Windows 事件日志事件的计数。

    ![指标资源管理器中的自定义指标][5]

* 使用[搜索](../application-insights/app-insights-diagnostic-search.md)在 Azure 诊断发送的跟踪日志中进行搜索。 例如，如果未经处理的异常造成角色崩溃和回收，则有关该异常的信息会显示在“Windows 事件日志”的“应用程序”通道中。 可使用“搜索”功能查看 Windows 事件日志错误，并获取异常的完整堆栈跟踪，以帮助查找问题原因。

    ![搜索跟踪][6]

## <a name="next-steps"></a>后续步骤
* [将 Application Insights SDK 添加到云服务](../application-insights/app-insights-cloudservices.md)，以便从应用程序发送有关请求、异常、依赖性和任何自定义遥测的数据。 与 Azure 诊断数据相结合后，此信息可使你在相同的 Application Insight 资源中获取应用程序和系统的完整视图。  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
