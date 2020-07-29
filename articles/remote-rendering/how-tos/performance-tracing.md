---
title: 创建客户端性能跟踪
description: 使用 WPF 进行客户端性能分析的最佳做法
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 2a10558e76a6e9af7c7571dc4ba3d063ce3e2286
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84021154"
---
# <a name="create-client-side-performance-traces"></a>创建客户端性能跟踪

出于许多原因，Azure 远程呈现的性能可能并不符合需要。 除了云服务器上的纯呈现性能之外，尤其是网络连接的质量对体验有重大影响。 若要分析服务器的性能，请参阅[服务器端性能查询](../overview/features/performance-queries.md)章节。

本章重点介绍如何通过识别潜在的客户端瓶颈 *:::no-loc text="performance traces":::* 。

## <a name="getting-started"></a>入门

如果您不熟悉 Windows :::no-loc text="performance tracing"::: 功能，本部分会提到最基本的术语和应用程序。

### <a name="installation"></a>安装

使用 ARR 进行跟踪的应用程序是可用于所有 Windows 开发的常规用途的工具。 它们是通过[Windows 性能工具包](https://docs.microsoft.com/windows-hardware/test/wpt/)提供的。 若要获取此工具包，请下载[Windows 评估和部署工具包](https://docs.microsoft.com/windows-hardware/get-started/adk-install)。

### <a name="terminology"></a>术语

搜索性能跟踪的相关信息时，可避免出现一系列术语。 最重要的是：

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW**代表[ **W**windows 的**E**通风管**T**比赛](https://docs.microsoft.com/windows/win32/etw/about-event-tracing)。 它只是 Windows 中内置的高效内核级跟踪工具的顶级名称。 它被称为*事件*跟踪，因为支持 ETW 的应用程序会将事件发送到日志操作，这些操作有助于跟踪性能问题。 默认情况下，操作系统已发出事件，如磁盘访问、任务开关等。 像 ARR 这样的应用程序还会发出自定义事件，例如有关删除的帧、网络延迟等。

**ETL**代表**E**通风管**T**种族**L**ogging。 它只是表示已收集（记录）跟踪，因此它通常用作存储跟踪数据的文件的文件扩展名。 因此，当您执行跟踪时，通常会有一个 \* .etl 文件。

**"** 代表[ **W**windows **P**性能**R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) ，是启动和停止事件跟踪记录的应用程序的名称。 "使用配置文件（ \* . wprp）来配置要记录的确切事件。 此类 `wprp` 文件随 ARR SDK 一起提供。 在台式计算机上进行跟踪时，可以直接启动 "。 在 HoloLens 上进行跟踪时，通常会通过 web 界面。

**WPA**代表[ **W**windows **P**性能**A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) ，是用于打开 \* .etl 文件和通过数据进行浏览以识别性能问题的 GUI 应用程序的名称。 WPA 允许你按不同的条件对数据进行排序，以多种方式显示数据，深入了解详细信息并关联信息。

尽管可以在任何 Windows 设备（本地电脑、HoloLens、云服务器等）上创建 ETL 跟踪，但通常会将其保存到磁盘，并在台式计算机上使用 WPA 进行分析。 可以向其他开发人员发送 ETL 文件，使其具有外观。 但请注意，可在 ETL 跟踪中捕获敏感信息（如文件路径和 IP 地址）。 可以通过两种方式使用 ETW：记录跟踪或分析跟踪。 录制跟踪是直接的，需要最少的设置。 另一方面，分析跟踪需要对 WPA 工具和正在调查的问题有很好的了解。 下面给出了用于学习 WPA 的一般资料，以及如何解释 ARR 特定跟踪的指导原则。

## <a name="recording-a-trace-on-a-local-pc"></a>在本地 PC 上记录跟踪

若要确定 ARR 性能问题，你应该更愿意直接在 HoloLens 上执行跟踪，因为这是获取真正性能特征的快照的唯一方法。 但是，如果你想要在没有 HoloLens 性能限制的情况下执行跟踪，或者只是想了解如何使用 WPA 并且不需要实际跟踪，请参阅以下内容。

### <a name="wpr-configuration"></a>"配置

1. [:::no-loc text="Windows Performance Recorder":::](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder)从 "开始"*菜单*启动。
1. 展开**更多选项**
1. 单击 "**添加配置文件 ...** "
1. 选择 AzureRemoteRenderingNetworkProfiling 文件*wprp*。 可以在 "*工具"/"ETLProfiles*" 下的 ARR SDK 中找到此文件。
   现在，配置文件将在 "*自定义度量*" 下的 "中列出。 请确保它是唯一启用的配置文件。
1. 展开*第一级会审*：
    * 如果要执行的操作是捕获 ARR 网络事件的快速跟踪，请**禁用**此选项。
    * 如果需要将 ARR 网络事件与其他系统特征（如 CPU 或内存使用率）相关联，请**启用**此选项。
    * 如果启用此选项，则跟踪最有可能是大小为多 gb，并需要很长时间才能保存并在 WPA 中打开。

此后，"配置应如下所示：

!["配置](./media/wpr.png)

### <a name="recording"></a>录制

单击 "**开始**" 开始记录跟踪。 你可以随时启动和停止记录;在执行此操作之前，无需关闭应用程序。 正如您所看到的，无需指定要跟踪的应用程序，因为 ETW 将始终为整个系统记录跟踪。 `wprp`文件指定要记录的事件类型。

单击 "**保存**" 停止录制，并指定存储 ETL 文件的位置。

你现在拥有一个 ETL 文件，你可以直接在 WPA 中打开该文件或将其发送给其他人。

## <a name="recording-a-trace-on-a-hololens"></a>在 HoloLens 上记录跟踪

若要在 HoloLens 上录制跟踪，请启动设备，并将其 IP 地址输入到浏览器中以打开*设备门户*。

![设备门户](./media/wpr-hl.png)

1. 在左侧，导航到 "*性能 > 性能跟踪*"。
1. 选择**自定义配置文件**
1. 依次**:::no-loc text="Browse...":::**
1. 选择 AzureRemoteRenderingNetworkProfiling 文件*wprp*。 可以在 "*工具"/"ETLProfiles*" 下的 ARR SDK 中找到此文件。
1. 单击**开始跟踪**
1. HoloLens 现在正在记录跟踪。 请确保触发要调查的性能问题。 然后单击 "**停止跟踪**"。
1. 该跟踪将列在网页底部。 单击右侧的磁盘图标以下载 ETL 文件。

你现在拥有一个 ETL 文件，你可以直接在 WPA 中打开该文件或将其发送给其他人。

## <a name="analyzing-traces-with-wpa"></a>利用 WPA 分析跟踪

### <a name="wpa-basics"></a>WPA 基础知识

Windows 性能分析器是打开 ETL 文件和检查跟踪的标准工具。 本文介绍了 WPA 的工作方式是否超出了范围。 若要开始，请查看以下资源：

* 观看[介绍性视频](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer)了解第一篇概述。
* WPA 本身有一个 "*入门*" 选项卡，用于说明常见步骤。 查看可用主题。 尤其是在 "查看数据" 下，您可以快速了解如何为特定数据创建关系图。
* [此网站](https://randomascii.wordpress.com/2015/09/24/etw-central/)有很好的信息，但并非所有初学者都相关。

### <a name="graphing-data"></a>绘图数据

若要开始处理 ARR 跟踪，请注意以下几个方面。

![性能图表](./media/wpa-graph.png)

上图显示了一个表，其中显示了跟踪数据和相同数据的图形表示。

在底部的表中，记下黄色（金色）栏和蓝色条。 您可以拖动这些条形并将它们放置在任何位置。

**黄色条左侧**的所有列都被解释为**键**。 键用于构建左上方窗口中的树。 这里有两个*键*列： "Provider name" 和 "Task Name"。 因此，左上方窗口中的树结构为深度为两级。 如果对列进行重新排序，或在键区域中添加或删除列，树视图中的结构将更改。

**蓝条右侧的列**用于**图形显示**在右上方窗口中。 大多数情况下，只使用第一列，但某些图形模式需要多个数据列。 要使线条图形正常工作，必须设置该列的*聚合模式*。 使用 "Avg" 或 "Max"。 当某个像素涵盖包含多个事件的范围时，使用聚合模式来确定给定像素的关系图的值。 可以通过将聚合设置为 "Sum" 并放大和缩小来观察此问题。

中间的列没有任何特殊含义。

![“事件”视图](./media/wpa-event-view.png)

在 "*一般事件视图编辑器*" 中，您可以配置要显示的所有列、聚合模式、排序以及用作键或用于绘图的列。 在上面的示例中，**字段 2**处于启用状态，字段 3-6 处于禁用状态。 Field 2 通常是 ETW 事件的第一个*自定义数据*字段，因而是用于显示某些网络延迟值的 "FrameStatistics" 事件。 启用其他 "字段" 列以查看此事件的更多值。

### <a name="presets"></a>预设

若要正确分析跟踪，需要确定自己的工作流和首选的数据显示。 但是，若要快速了解 ARR 特定的事件，我们将在文件夹*Tools/ETLProfiles*中包含 Windows 软件保护平台配置文件和预设文件。 若要加载完整的配置文件，请从 WPA 菜单栏中选择 "*配置文件 > 应用 ...* "，或打开 "*我的预设*" 面板（*"窗口 > 我的预设*"），然后选择 "*导入*"。 前者将设置完整的 WPA 配置，如下图所示。 后者只为可用的各种视图配置创建预设，并允许您快速打开视图来查看特定的 ARR 事件数据。

![预设](./media/wpa-arr-trace.png)

上图显示了各种 ARR 特定事件的视图以及总体 CPU 使用率的视图。

## <a name="next-steps"></a>后续步骤

* [服务器端性能查询](../overview/features/performance-queries.md)
