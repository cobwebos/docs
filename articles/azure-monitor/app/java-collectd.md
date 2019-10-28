---
title: 监视 Linux 上 Java Web 应用的性能 - Azure | Microsoft Docs
description: 通过 Application Insights 的 CollectD 插件监视 Java 网站的扩展应用程序性能。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 6c74684ac45a040be154a1e6406c1e7a5e0dd253
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817151"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd：Application Insights 中 Linux 性能指标


若要浏览 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 中 Linux 系统性能指标，请安装 [collectd](https://collectd.org/) 及其 Application Insights 插件。 此开放源解决方案收集了各种系统和网络统计信息。

通常，如果已使用[Application Insights 检测了 Java web 服务][java]，则会使用 collectd。 它可提供更多数据，有助于增强应用性能或诊断问题。 

## <a name="get-your-instrumentation-key"></a>获取检测密钥
在 [Microsoft Azure 门户](https://portal.azure.com)中，打开要显示数据的 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 资源。 （或[创建新资源](../../azure-monitor/app/create-new-resource.md )。）

复制可标识资源的检测密钥。

![浏览全部，打开资源，并在“概要”下拉菜单中选择并复制该检测密钥](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>安装 collectd 和插件
在 Linux 服务器计算机上：

1. 安装 [collectd](https://collectd.org/) 版本 5.4.0 或更高版本。
2. 下载 [Application Insights collectd 编写器插件](https://aka.ms/aijavasdk)。 注意版本号。
3. 将插件 JAR 复制到 `/usr/share/collectd/java`。
4. 编辑 `/etc/collectd/collectd.conf`：
   * 确保已启用 [Java 插件](https://collectd.org/wiki/index.php/Plugin:Java)。
   * 更新 java.class.path 的 JVMArg，以包括以下 JAR。 更新版本号，以匹配下载版本：
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * 使用资源中的检测密钥添加此代码段：

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

以下是示例配置文件的一部分：

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

配置其他 [collectd 插件](https://collectd.org/wiki/index.php/Table_of_Plugins)，其可从不同源中收集各种数据。

根据其[手册](https://collectd.org/wiki/index.php/First_steps)重启 collectd。

## <a name="view-the-data-in-application-insights"></a>查看 Application Insights 中的数据
在 Application Insights 资源中，打开[指标并添加图表，并][metrics]从自定义类别中选择要查看的度量值。

默认情况下，会从收集指标的所有主机中聚合指标。 要查看每个主机的指标，在“图表”详细信息边栏选项卡中，打开“分组”，并选择按 CollectD-Host 分组。

## <a name="to-exclude-upload-of-specific-statistics"></a>排除特定统计信息的上传
默认情况下，Application Insights 插件会发送由启用的所有 collectd“读取”插件收集的所有数据。 

排除特定插件或数据源中的数据：

* 编辑配置文件。 
* 在 `<Plugin ApplicationInsightsWriter>` 中，添加如下指令行：

| 指令 | 作用 |
| --- | --- |
| `Exclude disk` |排除由 `disk` 插件收集的所有数据 |
| `Exclude disk:read,write` |排除 `disk` 插件中名为 `read` 和 `write` 的源。 |

使用新行分隔指令。

## <a name="problems"></a>遇到问题？
*在门户中看不到数据*

* 打开[搜索][diagnostic]，查看原始事件是否已到达。 有时需较长时间才能在指标资源管理器中看到数据。
* 可能需要[为传出数据设置防火墙例外](../../azure-monitor/app/ip-addresses.md)
* 在 Application Insights 插件中启用跟踪。 在 `<Plugin ApplicationInsightsWriter>` 中添加此行：
  * `SDKLogger true`
* 打开终端并在详细模式下启动 collectd，查看其报告的任何问题：
  * `sudo collectd -f`

## <a name="known-issue"></a>已知问题

Application Insights 写入插件与某些读取插件不兼容。 Application Insights 插件需要浮点数时，有些插件有时会发送“NaN”。

症状： collectd 日志显示的错误包括 "AI： .。。SyntaxError：意外的标记 N "。

解决方法：排除由问题写入插件收集的数据。 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


