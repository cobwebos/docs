---
title: Azure Web 应用上节点应用程序的最佳做法和故障排除指南
description: 了解 Azure Web 应用上节点应用程序的最佳做法和故障排除步骤。
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.openlocfilehash: 860874ed49056e6b4695c060b06bf061820c390e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31789667"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Azure Web 应用上节点应用程序的最佳做法和故障排除指南

本文介绍 Azure Web 应用上运行的 [node 应用程序](app-service-web-get-started-nodejs.md)的最佳实践和故障排除步骤（使用 [iisnode](https://github.com/azure/iisnode)）。

> [!WARNING]
> 在生产站点上使用故障排除步骤时，请格外小心。 建议在非生产安装（例如过渡槽）上排查应用问题，当问题修复后，请交换过渡槽与生产槽。
>

## <a name="iisnode-configuration"></a>IISNODE 配置

此[架构文件](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml)显示可针对 iisnode 配置的所有设置。 适用于应用程序的一些设置如下：

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

此设置控制每个 IIS 应用程序启动的节点进程数目。 默认值为 1。 可将此值设置为 0，启动与 VM vCPU 计数一样多的 node.exe。 大多数应用程序的建议值为 0，以便利用计算机上的所有 vCPU。 Node.exe 占用单线程，因此，一个 node.exe 最多消耗用 1 个 vCPU。 要让 node 应用程序发挥最大性能，可以利用所有 vCPU。

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

此设置控制 node.exe 的路径。 可以设置此值以指向 node.exe 版本。

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

此设置控制 iisnode 发送给每个 node.exe 的并发请求数目上限。 在 Azure Web 应用中，默认值为“无限”。 在 Azure Web 应用外部，默认值为 1024。 可以根据应用程序收到的请求数目以及应用程序处理每个请求的速度来配置此值。

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

此设置控制 iisnode 在命名管道上重试连接，以将请求发送到 node.exe 的次数上限。 此设置与 namedPipeConnectionRetryDelay 结合使用，可确定 iisnode 内每个请求的总超时。 在 Azure Web 应用中，默认值为 200。 总超时（秒）= (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

此设置控制 iisnode 在每次重试通过命名管道将请求发送到 node.exe 之间所要等待的时间（毫秒）。 默认值为 250 毫秒。
总超时（秒）= (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

在 Azure Web 应用中，iisnode 的总超时默认为 200 \* 250 毫秒 = 50 秒。

### <a name="logdirectory"></a>logDirectory

此设置控制 iisnode 用于记录 stdout/stderr 的目录。 默认值是相对于主要脚本目录（主要 server.js 所在的目录）的 iisnode

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

此设置控制 iisnode 在调试 node 应用程序时使用的节点检查器版本。 iisnode-inspector-0.7.3.dll 和 iisnode-inspector.dll 目前是此设置仅有的两个有效值。 默认值为 iisnode-inspector-0.7.3.dll。 iisnode-inspector-0.7.3.dll 版本使用 node-inspector-0.7.3 并使用 Web 套接字。 在 Azure Web 应用中启用 Web 套接字以使用此版本。 有关如何将 iisnode 配置为使用新 node-inspector 的更多详细信息，请参阅 <http://ranjithblogs.azurewebsites.net/?p=98>。

### <a name="flushresponse"></a>flushResponse

IIS 的默认行为是在刷新之前或直到响应结束时（以较早出现者为准），缓冲最多 4 MB 的响应数据。 iisnode 会提供配置设置来替代此行为：要在 iisnode 从 node.exe 收到响应实体主体片段时立刻刷新该片段，需在 web.config 中将 iisnode/@flushResponse 属性设置为“true”：

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

启用每个响应实体主体片段的刷新会增大性能开销，但会降低约 5% 的系统吞吐量（从 v0.1.13 起）。 最好将此设置的范围限定于需要响应流式处理的终结点（例如，在 web.config 中使用 `<location>` 元素）

除此之外，对于流式处理应用程序，必须将 iisnode 处理程序的 responseBufferLimit 设置为 0。

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

一个以分号分隔的文件列表，系统将监视其更改。 任何文件更改会导致应用程序回收。 每个条目都包含可选目录名称，以及相对于主要应用程序入口点所在目录的必要文件名。 只有文件名部分可以使用通配符。 默认值为 `*.js;web.config`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

默认值为 false。 如果启用，节点应用程序可以连接到命名管道（环境变量 IISNODE\_CONTROL\_PIPE）并发送“回收”消息。 可以通过此方式正常回收 w3wp。

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

默认值为 0，表示已禁用此功能。 如果设置为大于 0 的值，iisnode 会每隔“idlePageOutTimePeriod”毫秒将其所有子进程移出分页。 若要了解移出分页的含义，请参考[文档](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx)。 对于消耗大量内存并且偶尔想要将内存页出至磁盘以释放 RAM 的应用程序，此设置很有用。

> [!WARNING]
> 在生产应用程序上启用以下配置设置时，请格外小心。 建议不要在实际生产应用程序上启用它们。
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

默认值为 false。 如果设置为 true，iisnode 会将 HTTP 响应标头 `iisnode-debug` 添加到它所发送的每个 HTTP 响应，`iisnode-debug` 标头值是一个 URL。 查看 URL 片段即可收集各项诊断信息，但在浏览器中打开该 URL 可达到更好的视觉效果。

### <a name="loggingenabled"></a>loggingEnabled

此设置控制 iisnode 记录 stdout 和 stderr 的功能。 Iisnode 从它所启动的节点进程捕获 stdout/stderr，并写入“logDirectory”设置中指定的目录。 一旦启用，应用程序会将日志写入文件系统，这可能会影响性能，但具体要视应用程序完成的日志记录量而定。

### <a name="deverrorsenabled"></a>devErrorsEnabled

默认值为 false。 如果设置为 true，iisnode 会在浏览器上显示 HTTP 状态代码和 Win32 错误代码。 在调试特定类型的问题时，win32 代码很有用。

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled（请勿在实际生产站点上启用）

此设置控制调试功能。 Iisnode 与节点检查器集成。 通过启用此设置，可启用节点应用程序的调试功能。 启用此设置后，iisnode 会在对 Node 应用程序发出第一个调试请求时，在“debuggerVirtualDir”目录中创建 node-inspector 文件。 可将请求发送到 http://yoursite/server.js/debug，以加载 node-inspector。 可以使用“debuggerPathSegment”设置来控制调试 URL 段。 默认情况下，debuggerPathSegment=’debug’。 可将 `debuggerPathSegment` 设置为 GUID 之类的值，这样，其他人就更难发现。

有关调试的详细信息，请参阅[在 Windows 上调试 node.js 应用程序](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html)。

## <a name="scenarios-and-recommendationstroubleshooting"></a>方案和建议/故障排除

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>Node 应用程序发出的出站调用过多

许多应用程序想要在其定期操作中进行出站连接。 例如，当请求传入时，节点应用程序会想连接别处的 REST API，并获取一些信息来处理请求。 建议在进行 http 或 https 调用时使用保持连接代理。 可在进行这些出站调用时，使用 agentkeepalive 模块作为保持连接代理。

agentkeepalive 模块确保在 Azure Web 应用 VM 上重复使用套接字。 在每个出站请求中创建新套接字会增大应用程序的开销。 让应用程序对出站请求重复使用套接字可确保应用程序不会超过为每个 VM 分配的 maxSockets。 对于 Azure Web 应用的建议是将 agentKeepAlive maxSockets 值设置为每个 VM 总共 160 个套接字（4 个 node.exe 实例 \* 每个实例 40 个 maxSockets）。

[agentKeepALive ](https://www.npmjs.com/package/agentkeepalive) 配置示例：

```nodejs
var keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> 此示例假设有 4 个 node.exe 在 VM 上运行。 如果有不同数目的 node.exe 在 VM 上运行，则必须相应地修改 maxSockets 设置。
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>节点应用程序消耗过多的 CPU

门户上可能会显示 Azure Web 应用针对高 CPU 消耗量提供的建议。 也可将监视器设置为监视某些[指标](web-sites-monitor.md)。 在 [Azure 门户仪表板](../application-insights/app-insights-web-monitor-performance.md)上检查 CPU 使用率时，请检查 CPU 的最大值，这样才不会错过峰值。
如果你认为应用程序消耗了过多的 CPU，但又无法做出解释，可以分析 Node 应用程序来找出原因。

#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>在 Azure Web 应用中使用 V8 探查器分析 node 应用程序

例如，假设需要分析如下所示的 hello world 应用：

```nodejs
var http = require('http');
function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

转到调试控制台站点 https://yoursite.scm.azurewebsites.net/DebugConsole。

进入 site/wwwroot 目录。 将会看到一个命令提示符，如以下示例所示：

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

运行命令 `npm install v8-profiler`。

此命令在 node\_modules 目录及其所有依赖项之下安装 v8-profiler。
现在，编辑 server.js 以分析应用程序。

```nodejs
var http = require('http');
var profiler = require('v8-profiler');
var fs = require('fs');

function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

上述代码将会分析 WriteConsoleLog 函数，然后将配置文件输出写入站点 wwwroot 下的“profile.cpuprofile”文件。 将请求发送到应用程序。 站点 wwwroot 下会显示所创建的“profile.cpuprofile”文件。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

请下载此文件，并使用 Chrome F12 工具将其打开。 在 Chrome 中按 F12，并选择“配置文件”选项卡。单击“加载”按钮。 选择下载的 profile.cpuprofile 文件。 单击刚加载的配置文件。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

可以看到，WriteConsoleLog 函数已耗用 95% 的时间。 输出中还会显示造成此问题的具体行号和源文件。

### <a name="my-node-application-is-consuming-too-much-memory"></a>node 应用程序消耗过多的内存

如果应用程序消耗过多的内存，门户中会显示 Azure Web 应用针对高内存消耗量提供的建议。 可将监视器设置为监视某些[指标](web-sites-monitor.md)。 在 [Azure 门户仪表板](../application-insights/app-insights-web-monitor-performance.md)上检查内存使用率时，请务必检查内存的最大值，这样才不会错过峰值。

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>node.js 的泄漏检测和堆区分

可以使用 [node-memwatch](https://github.com/lloyd/node-memwatch) 帮助识别内存泄漏。
可以像安装 v8-profiler 一样安装 `memwatch`，并编辑代码来捕获和区分堆，以找出应用程序中的内存泄漏。

### <a name="my-nodeexes-are-getting-killed-randomly"></a>node.exe 随机终止

node.exe 随机关闭的原因有多种：

1. 应用程序正在引发未捕获的异常 – 请检查 d:\\home\\LogFiles\\Application\\logging-errors.txt 文件，了解有关所引发异常的详细信息。 此文件提供堆栈跟踪以帮助调试和修复应用程序。
2. 应用程序消耗过多的内存，导致其他进程无法启动。 如果 VM 内存总量接近 100%，则进程管理器会终止 node.exe。 进程管理器终止某些进程后，其他进程便有机会执行一些工作。 若要解决此问题，请探查应用程序中的内存泄漏问题。 如果应用程序需要大量的内存，请纵向扩展为更大的 VM（增加 VM 的可用 RAM）。

### <a name="my-node-application-does-not-start"></a>节点应用程序未启动

如果应用程序在启动时返回 500 错误，可能有几个原因：

1. Node.exe 未出现在正确的位置。 检查 nodeProcessCommandLine 设置。
2. 主要脚本文件未出现在正确的位置。 检查 web.config，并确保处理程序节中的主要脚本文件名与主要脚本文件匹配。
3. Web.config 配置不正确 – 检查设置名称/值。
4. 冷启动 – 应用程序的启动时间太长。 如果应用程序所花的时间超过 (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 秒，则 iisnode 会返回 500 错误。 增加这些设置的值，以便与应用程序启动时间匹配，从而防止 iisnode 超时并返回 500 错误。

### <a name="my-node-application-crashed"></a>节点应用程序崩溃

应用程序正在引发未捕获的异常 – 请检查 `d:\\home\\LogFiles\\Application\\logging-errors.txt` 文件，了解有关所引发异常的详细信息。 此文件提供堆栈跟踪以帮助诊断和修复应用程序。

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>node 应用程序的启动时间太长（冷启动）

应用程序启动时间过长的最常见原因是 node\_modules 中有大量文件。 应用程序在启动时会尝试加载其中的大多数文件。 默认情况下，由于文件存储在 Azure Web 应用的网络共享上，因此加载过多的文件可能需要一段时间。
加速此过程的某些解决方法包括：

1. 使用 npm3 来安装模块，确保采用平面依赖关系结构，并且没有重复的依赖项。
2. 尝试延迟加载 node\_modules，而不要在应用程序启动时加载所有模块。 若要延迟加载模块，应在首次执行模块代码之前，在函数中真正需要该模块时调用 require('module')。
3. Azure Web 应用提供一项称为本地缓存的功能。 此功能会将内容从网络共享复制到 VM 上的本地磁盘。 由于文件位于本地，因此，node\_modules 的加载时间快很多。

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http 状态和子状态

`cnodeconstants` [源文件](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h)列出了 iisnode 可在发生错误时返回的所有可能的状态/子状态组合。

为应用程序启用 FREB 以查看 win32 错误代码（出于性能方面的原因，请确保只在非生产站点上启用 FREB）。

| Http 状态 | Http 子状态 | 可能的原因？ |
| --- | --- | --- |
| 500 |1000 |将请求分派到 IISNODE 时发生某种问题 – 检查 node.exe 是否已启动。 Node.exe 可能在启动时已崩溃。 检查 web.config 配置是否有错误。 |
| 500 |1001 |- Win32Error 0x2 - 应用未响应 URL。 检查 URL 重写规则，或检查是否为 Express 应用定义了正确的路由。 - Win32Error 0x6d – 命名管道繁忙 – Node.exe 不接受请求，因为管道繁忙。 检查 CPU 使用率是否偏高。 - 其他错误 — 检查 node.exe 是否已崩溃。 |
| 500 |1002 |Node.exe 崩溃 – 检查 d:\\home\\LogFiles\\logging-errors.txt 中的堆栈跟踪。 |
| 500 |1003 |管道配置问题 – 命名管道配置不正确。 |
| 500 |1004-1018 |发送请求或处理 node.exe 的相关响应时发生某个错误。 检查 node.exe 是否已崩溃。 检查 d:\\home\\LogFiles\\logging-errors.txt 中的堆栈跟踪。 |
| 503 |1000 |内存不足，无法分配更多命名管道连接。 检查应用为何耗用这么多内存。 检查 maxConcurrentRequestsPerProcess 设置值。 如果此值并非无限，而且有许多请求，请增大此值来防止此错误。 |
| 503 |1001 |无法将请求分派至 node.exe，因为应用程序正在回收。 应用程序回收之后，应该会正常处理请求。 |
| 503 |1002 |检查 win32 错误代码的实际原因 – 无法将请求分派到 node.exe。 |
| 503 |1003 |命名管道太忙 – 验证 node.exe 是否正在消耗过多的 CPU |

NODE.exe 具有名为 `NODE_PENDING_PIPE_INSTANCES` 的设置。 默认情况下，当未部署在 Azure Web 应用上时，此值为 4。 这意味着 node.exe 在命名管道上一次只能接受四个请求。 在 Azure Web 应用中，此值设置为 5000。 此值应足以满足 Azure Web 应用中运行的大多数 node 应用程序。 Azure Web 应用中不应出现 503.1003，因为 `NODE_PENDING_PIPE_INSTANCES` 的值较高。

## <a name="more-resources"></a>更多资源

请访问以下链接，了解有关 Azure 应用服务上的 node.js 应用程序的详细信息。

* [Azure 应用服务中的 Node.js Web 应用入门](app-service-web-get-started-nodejs.md)
* [如何在 Azure 应用服务中调试 Node.js Web 应用](app-service-web-tutorial-nodejs-mongodb-app.md)
* [将 Node.js 模块与 Azure 应用程序一起使用](../nodejs-use-node-modules-azure-apps.md)
* [Azure 应用服务 Web 应用：Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js 开发人员中心](../nodejs-use-node-modules-azure-apps.md)
* [Kudu 调试控制台探秘](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)