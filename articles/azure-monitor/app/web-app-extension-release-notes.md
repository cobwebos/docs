---
title: Azure web 应用扩展的发行说明-Application Insights
description: 发布适用于运行时检测的 Azure Web 应用扩展的说明，并提供 Application Insights。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: b9da3b0647d5abe60dd8f6cb21163ff21f07205b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539714"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>适用于 Application Insights 的 Azure Web 应用扩展的发行说明

本文包含适用于运行时检测并带有 Application Insights 的 Azure Web 应用扩展的发行说明。 这仅适用于预安装的扩展。

详细[了解](azure-web-apps.md)适用于 Application Insights 的 Azure Web 应用扩展。

## <a name="frequently-asked-questions"></a>常见问题

- 如何查找当前正在启用的扩展版本？
    - 转到 `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`。 有关详细信息，请参阅[基于扩展/代理的监视的分步故障排除指南](./azure-web-apps.md?tabs=net#troubleshooting)。

- 如果我使用的是专用扩展，该怎么办？
    - 卸载专用站点扩展，因为它不再受支持。

## <a name="release-notes"></a>发行说明

### <a name="2833"></a>2.8.33

- .NET、.NET core、Java 和 Node.js 代理和 Windows Extension：主权云支持。 连接字符串可用于向主权云发送数据。

### <a name="2831"></a>2.8.31

- ASP.NET Core 代理：修复了与一个已更新的 Application Insights SDK 引用相关的问题（请参阅2.8.26 的已知问题）。 如果 `System.Diagnostics.DiagnosticSource.dll` 运行时已加载的版本不正确，则无代码置备扩展现在不会损坏应用程序并退出。 对于受此问题影响的客户，建议 `System.Diagnostics.DiagnosticSource.dll` 从 bin 文件夹中删除，或者通过设置 "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24" 来使用较旧版本的扩展; 否则，将不会启用应用程序监视。

### <a name="2826"></a>2.8.26

- ASP.NET Core 代理：修复了与更新 Application Insights SDK 相关的问题。 `AiHostingStartup`如果 ApplicationInsights.dll 已在 bin 文件夹中，则代理不会尝试加载。 这可通过程序集解决与反射相关的问题 \<AiHostingStartup\> 。Iprovidednamespace.gettypes （）。
- 已知问题： `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` 如果加载了其他版本的 dll，则会引发异常 `DiagnosticSource` 。 例如，如果 `System.Diagnostics.DiagnosticSource.dll` 发布文件夹中存在，则可能会发生这种情况。 作为缓解措施，请通过在应用服务中设置应用设置来使用扩展的以前版本： ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24。

### <a name="2824"></a>2.8.24

- 2.8.21 的重新打包版本。

### <a name="2823"></a>2.8.23

- 添加了 ASP.NET Core 3.0 无代码置备监视支持。
- 已更新 ASP.NET Core SDK 到[2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0)的运行时版本2.1、2.2 和3.0。 面向 .NET Core 2.0 的应用将继续使用 SDK 的2.1.1。

### <a name="2814"></a>2.8.14

- 针对面向 .NET Core 2.1、2.2 的应用，将 ASP.NET Core SDK 版本从2.3.0 更新为最新（2.6.1）。 面向 .NET Core 2.0 的应用将继续使用 SDK 的2.1.1。

### <a name="2812"></a>2.8.12

- 支持 ASP.NET Core 2.2 应用。
- 修复了 ASP.NET Core 扩展中的 bug 引发了 SDK 的注入，即使应用程序已使用 SDK 进行了检测。 对于2.1 和2.2 应用，应用程序文件夹中的 ApplicationInsights.dll 现在会导致扩展停止。 对于2.0 应用程序，仅当通过调用启用了 Applicationinsights.config 时，扩展才会退出 `UseApplicationInsights()` 。

- ASP.NET Core 应用的不完整 HTML 响应的永久性修复。 此修补程序现已扩展为适用于 .NET Core 2.2 应用。

- 添加了对关闭 ASP.NET Core 应用的 JavaScript 注入（）的支持 `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` 。 对于 ASP.NET core，默认情况下，JavaScript 注入处于 "选择退出" 模式，除非显式关闭。 （为了保持当前行为，默认设置为 "已完成"。）

- 修复了导致注入的 ASP.NET Core 扩展 bug，即使 ikey 不存在也是如此。
- 修复了 SDK 版本前缀逻辑中导致遥测中的 SDK 版本不正确的 bug。

- 为 ASP.NET Core 应用添加了 SDK 版本前缀，以确定如何收集遥测数据。
- 修复了 Applicationinsights.config 页面，以正确显示预安装的扩展的版本。

### <a name="2810"></a>2.8.10

- 修复 ASP.NET Core 应用的不完整 HTML 响应。

## <a name="next-steps"></a>后续步骤

- 有关如何为 Azure 应用服务配置监视的详细信息，请访问[Azure App Service 文档](azure-web-apps.md)。 
