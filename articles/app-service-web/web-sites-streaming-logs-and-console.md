---
title: "流式处理日志和控制台"
description: "流式处理日志和控制台概述"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7704270d7bba54577f61f3f349dc69dc6ddf08c6


---
# <a name="streaming-logs-and-the-console"></a>流式处理日志和控制台
## <a name="streaming-logs"></a>流式处理日志
**Azure 门户**提供了集成的流式处理日志查看器，可用于实时查看来自**应用服务**应用的跟踪事件。  

设置此功能需要几个简单的步骤：

* 在代码中编写跟踪
* 为应用启用应用程序**诊断日志**
* 从 **Azure 门户**中的内置“流式处理日志”UI 查看流

### <a name="how-to-write-traces-in-your-code"></a>如何在代码中编写跟踪
在代码中编写跟踪很容易。  在 C# 中，可以很简单地编写这样的代码：

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Trace 类驻留在 System.Diagnostics 命名空间中。

在 node.js 应用中，可以编写以下代码来获得相同的效果：

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>如何启用和查看流式处理日志
![][BrowseSitesScreenshot] 诊断是基于每个应用来启用的。 首先浏览到要启用此功能的网站。  

![][DiagnosticsLogs] 从“设置”菜单中，向下滚动到“监视”部分并单击 (1)“诊断日志”。 然后单击 (2)“启用应用程序日志记录(文件系统）”或“应用程序日志记录(Blob)”。“级别”选项允许更改要捕获的跟踪的严重级别。 如果只是尝试熟悉该功能，请将级别设为“详细”，以确保收集所有跟踪语句。

单击边栏选项卡顶部的“保存”，然后就可以开始查看日志了。

> [!NOTE]
> **注意：**“严重级别”越高，日志消耗的资源就越多，但获得的跟踪线索也越多。 对于生产站点或高流量站点，请确保将“严重级别”设置为正确的详细程度。 
> 
> 

![][StreamingLogsScreenshot] 要从 Azure 门户中查看**流式处理日志**，同样是在“设置”菜单的“监视”部分中，单击 (1)“日志流“。 如果应用主动写入跟踪语句，则会以近乎实时的方式在 (2)“流式处理日志”UI 中看到它们。

## <a name="console"></a>控制台
**Azure 门户**提供了对应用的控制台访问。 可以浏览应用的文件系统并运行 powershell/cmd 脚本。 在执行控制台命令时，拥有的权限集与正在运行的应用代码相同。 将无法访问受保护的目录，也无法运行需要提升的权限的脚本。  

![][ConsoleScreenshot] 在“设置”菜单中，向下滚动到“开发工具”部分，并单击 (1)“控制台”，(2) **控制台** UI 将在右侧打开。

若要熟悉**控制台**，请尝试如下一些基本命令：

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png



<!--HONumber=Nov16_HO3-->


