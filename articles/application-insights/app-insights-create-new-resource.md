---
title: "新建 Application Insights 资源| Microsoft 文档"
description: "为新的实时应用程序设置 Application Insights 监视。 基于 Web 的方法。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 63c901529b81c75f46f1b21219054817c148063a
ms.openlocfilehash: 0157b796a09b5685386cd96d6969da04179279dc


---
# <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源
Azure Application Insights 在 Microsoft Azure *资源*中显示有关应用程序的数据。 因此创建新资源属于[设置 Application Insights 来监视新应用程序][启动]的一个环节。 在许多情况下，IDE 可自动完成此任务，这也是建议使用的方法。 但某些情况下，必须手动创建资源。

创建资源后，可以获取其检测密钥，并将其用于设置应用程序中的 SDK。 这会将遥测数据发送到资源。

## <a name="sign-up-to-microsoft-azure"></a>注册 Microsoft Azure
如果没有 [Microsoft 帐户，请立即注册一个](http://live.com)。 （如果你曾经用过 Outlook.com、OneDrive、Windows Phone 或 XBox Live 等服务，则已获得了一个 Microsoft 帐户。）

此外，还需要订阅 [Microsoft Azure](http://azure.com)。 如果团队或组织拥有 Azure 订阅，则所有者可以使用你的 Windows Live ID 将你加入其中。

或者，可以创建新订阅。 使用免费帐户可以试用 Azure 中的所有功能。 试用期过后，你可能会觉得即用即付订阅更适合自己，因为无需为免费的服务付费。 

获取订阅的访问权限后，请在 [http://portal.azure.com](https://portal.azure.com) 中使用 Live ID 登录到 Application Insights。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源
在 [portal.azure.com](https://portal.azure.com) 中，添加 Application Insights 资源：

![依次单击“新建”、“Application Insights”](./media/app-insights-create-new-resource/01-new.png)

* **应用程序类型**会影响在概述边栏选项卡上看到的内容和[指标资源管理器][指标]中的可用属性。 如果未看到你的应用类型，请选择“ASP.NET”。
* **资源组**便于管理访问控制之类的属性。 如果已创建其他 Azure 资源，可以选择将此新资源放在同一个组中。
* **订阅**是 Azure 中的付款帐户。
* **位置**是保留数据的位置。
* **添加到启动板**将资源的快速访问磁贴放在 Azure 主页上。 推荐。

创建应用后，将打开一个新边栏选项卡。 可在此处看到有关应用的性能和使用情况数据。 

若要在下次登录 Azure 时返回该应用，请在开始面板（主屏幕）上查找应用的快速启动磁贴。 或单击“浏览”找到它。

## <a name="copy-the-instrumentation-key"></a>复制检测密钥
检测密钥标识所创建的资源。 需要将它提供给 SDK。

![单击“Essentials”、单击“检测密钥，然后按“CTRL+C”](./media/app-insights-create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>在应用中安装 SDK
在应用中安装 Application Insights SDK。 此步骤在很大程度上依赖于应用程序的类型。 

使用检测密钥来配置[应用程序中安装的 SDK][启动]。

SDK 包含无需编写任何代码即可发送遥测数据的标准模块。 若要跟踪用户操作或更细致地诊断问题，请[使用 API][API] 发送自己的遥测数据。

## <a name="a-namemonitorasee-telemetry-data"></a><a name="monitor"></a>查看遥测数据
关闭快速启动边栏选项卡，返回到 Azure 门户中的应用程序边栏选项卡。

单击“搜索”磁贴查看[诊断搜索][诊断]，其中显示了前几个事件。 

如果需要更多数据，在几秒后单击“刷新”。

## <a name="creating-a-resource-automatically"></a>自动创建资源
可以编写 [PowerShell 脚本](app-insights-powershell-script-create-resource.md)来自动创建资源。

## <a name="next-steps"></a>后续步骤
* [创建仪表板](app-insights-dashboards.md)
* [诊断搜索](app-insights-diagnostic-search.md)
* [探索指标](app-insights-metrics-explorer.md)
* [编写分析查询](app-insights-analytics.md)

<!--Link references-->

[API]: app-insights-api-custom-events-metrics.md
[诊断]: app-insights-diagnostic-search.md
[指标]: app-insights-metrics-explorer.md
[启动]: app-insights-overview.md




<!--HONumber=Nov16_HO3-->


