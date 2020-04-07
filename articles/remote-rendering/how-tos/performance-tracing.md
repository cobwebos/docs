---
title: 创建客户端性能跟踪
description: 使用 WPF 进行客户端性能分析的最佳做法
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681306"
---
# <a name="create-client-side-performance-traces"></a>创建客户端性能跟踪

Azure 远程呈现的性能可能不如预期的原因有很多。 除了云服务器上的纯渲染性能外，尤其是网络连接的质量对体验有重要影响。 要分析服务器的性能，请参阅[服务器端性能查询一](../overview/features/performance-queries.md)章。

本章重点介绍如何通过*性能跟踪*识别潜在的客户端瓶颈。

## <a name="getting-started"></a>入门

如果您是 Windows 性能跟踪功能的新功能，本节将提及入门项和应用程序的最基本术语和应用程序。

### <a name="installation"></a>安装

用于使用 ARR 进行跟踪的应用程序是可用于所有 Windows 开发的普通工具。 它们通过 Windows[性能工具包提供](https://docs.microsoft.com/windows-hardware/test/wpt/)。 要获取此工具包，请下载[Windows 评估和部署工具包](https://docs.microsoft.com/windows-hardware/get-started/adk-install)。

### <a name="terminology"></a>术语

搜索有关性能跟踪的信息时，不可避免地会遇到一系列术语。 最重要的是：

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW**代表[**E**通风**T**赛车**为W**的内多斯](https://docs.microsoft.com/windows/win32/etw/about-event-tracing)。 它只是 Windows 中内置的有效内核级跟踪工具的总名称。 它被称为*事件*跟踪，因为支持 ETW 的应用程序将发出事件来记录可能有助于跟踪性能问题的操作。 默认情况下，操作系统已为磁盘访问、任务开关等事件发出事件。 ARR 等应用程序还会发出自定义事件，例如关于丢弃的帧、网络延迟等。

**ETL**代表**E**通风**T**赛车**L**奥格。 它只是意味着已收集（记录）跟踪，因此通常用作存储跟踪数据的文件的文件扩展名。 因此，当您执行跟踪时，您通常会在之后有\*一个 .etl 文件。

**WPR**代表[**W**indows **P**erformance **R**ecorder，](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder)是启动和停止记录事件跟踪的应用程序的名称。 WPR 采用配置文件文件\*（.wprp），用于配置要记录的确切事件。 ARR `wprp` SDK 提供了此类文件。 在台式 PC 上执行跟踪时，可以直接启动 WPR。 在 HoloLens 上执行跟踪时，您通常会通过 Web 界面。

**WPA**代表[**W**indos **P**erformance **A**nalyzer，](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer)是用于打开\*.etl 文件和筛选数据以识别性能问题的 GUI 应用程序的名称。 WPA 允许您按各种条件对数据进行排序、以多种方式显示数据、深入了解详细信息以及关联信息。

虽然可以在任何 Windows 设备上（本地 PC、HoloLens、云服务器等）创建 ETL 跟踪，但它们通常保存到磁盘，并在台式 PC 上使用 WPA 进行分析。 ETL 文件可以发送给其他开发人员，让他们查看。 但请注意，敏感信息（如文件路径和 IP 地址）可能会在 ETL 跟踪中捕获。 您可以通过两种方式使用 ETW：记录跟踪或分析跟踪。 录制跟踪是直截了当的，需要最少的设置。 另一方面，分析跟踪确实需要对 WPA 工具和您正在调查的问题有一个体面的理解。 下文将提供学习 WPA 的一般材料，以及如何解释 ARR 特定跟踪的指南。

## <a name="recording-a-trace-on-a-local-pc"></a>在本地 PC 上录制跟踪

要识别 ARR 性能问题，您应该希望直接在 HoloLens 上执行跟踪，因为这是获取真实性能特征快照的唯一方法。 但是，如果您特别想要在没有 HoloLens 性能限制的情况下执行跟踪，或者只想了解如何使用 WPA 并且不需要逼真的跟踪，下面是如何执行此操作。

### <a name="wpr-configuration"></a>WPR 配置

1. 从*开始菜单*启动[Windows 性能记录器](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder)。
1. 展开**更多选项**
1. 单击"**添加配置文件..."**
1. 选择文件*Azure 远程呈现网络分析.wprp*。 您可以在*工具/ETL 配置文件*下的 ARR SDK 中找到此文件。
   该配置文件现在将在 WPR 下在*自定义测量*下列出。 请确保它是唯一启用的配置文件。
1. 展开*第一级会审*：
    * 如果只想捕获 ARR 网络事件的快速跟踪，**请禁用**此选项。
    * 如果需要将 ARR 网络事件与其他系统特征（如 CPU 或内存使用情况）相关联，则**启用**此选项。
    * 如果启用此选项，跟踪很可能为多个 GB 大小，并且需要很长时间才能在 WPA 中保存和打开。

之后，WPR 配置应如下所示：

![WPR 配置](./media/wpr.png)

### <a name="recording"></a>记录

单击 **"开始"** 开始录制跟踪。 您可以随时开始和停止录制;在执行此操作之前，您无需关闭应用程序。 正如您所看到的，您不需要指定要跟踪的应用程序，因为 ETW 将始终记录整个系统的跟踪。 该文件`wprp`指定要记录的事件类型。

单击 **"保存**"停止录制并指定将 ETL 文件存储的位置。

现在，您有一个 ETL 文件，可以直接在 WPA 中打开该文件，也可以发送给其他人。

## <a name="recording-a-trace-on-a-hololens"></a>在 HoloLens 上记录跟踪

要在 HoloLens 上记录跟踪，请启动您的设备并将其 IP 地址输入浏览器以打开*设备门户*。

![设备门户](./media/wpr-hl.png)

1. 在左侧，导航到*性能>性能跟踪*。
1. 选择**自定义配置文件**
1. 单击 **"浏览..."**
1. 选择文件*Azure 远程呈现网络分析.wprp*。 您可以在*工具/ETL 配置文件*下的 ARR SDK 中找到此文件。
1. 单击 **"开始跟踪"**
1. HoloLens 正在记录一个跟踪。 请确保触发要调查的性能问题。 然后单击 **"停止跟踪**"。
1. 跟踪将列在网页底部。 单击右侧的磁盘图标以下载 ETL 文件。

现在，您有一个 ETL 文件，可以直接在 WPA 中打开该文件，也可以发送给其他人。

## <a name="analyzing-traces-with-wpa"></a>使用 WPA 分析跟踪

### <a name="wpa-basics"></a>WPA 基础知识

Windows 性能分析器是打开 ETL 文件和检查跟踪的标准工具。 对本文的 WPA 工作方式的解释已不为范围。 要开始，请查看以下资源：

* 观看[介绍性视频](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer)，了解第一次概述。
* WPA 本身具有 *"入门"* 选项卡，它解释了常见步骤。 查看可用主题。 特别是在"查看数据"下，您可以快速介绍如何为特定数据创建图形。
* 有[优秀的信息在这个网站上](https://randomascii.wordpress.com/2015/09/24/etw-central/)，但是，不是所有它相关的初学者。

### <a name="graphing-data"></a>绘制数据图形

要开始使用 ARR 跟踪，以下部分是一个很好的了解。

![性能图](./media/wpa-graph.png)

上图显示了跟踪数据的表和相同数据的图形表示形式。

在底部的表格中，注意黄色（金色）条和蓝色条。 您可以拖动这些条形，并将其放置在任何位置。

**黄色条形左侧的所有列**都解释为**键**。 键用于在左上角窗口中构造树。 在这里，我们有两*个关键*列，"提供程序名称"和"任务名称"。 因此，左上角窗口中的树结构是两层深。 如果重新排序列或从关键区域添加或删除列，则树视图中的结构将更改。

**蓝色条右侧的列**用于右上角窗口中的**图形显示**。 大多数时候只使用第一列，但某些图形模式需要多列数据。 要使线图正常工作，必须设置该列上的*聚合模式*。 使用"平均"或"最大值"。 当像素覆盖具有多个事件的范围时，聚合模式用于确定给定像素处图形的值。 这可以通过将聚合设置为"Sum"，然后放大和缩小来观察。

中间的列没有特殊的含义。

![“事件”视图](./media/wpa-event-view.png)

在 *"通用事件视图编辑器*"中，可以配置要显示的所有列、聚合模式、排序以及哪些列用作键或用于绘制图形。 在上面的示例中，**字段 2**已启用，字段 3 - 6 已禁用。 字段 2 通常是 ETW 事件的第一个*自定义数据*字段，因此对于表示某些网络延迟值的 ARR"FrameStatistics"事件而言。 启用其他"字段"列以查看此事件的进一步值。

### <a name="presets"></a>预设

要正确分析跟踪，您需要找出自己的工作流和首选数据显示。 但是，为了能够快速概览特定于 ARR 的事件，我们在文件夹*工具/ETL配置文件*中包括 Windows 软件保护平台配置文件和预设文件。 要加载完整配置文件，请从 WPA 菜单栏中选择 *"应用配置文件 >"，* 或打开"*我的预设"* 面板（*窗口>我的预设*）并选择"*导入*"。 前者将设置完整的 WPA 配置，如下图所示。 后者将仅对可用的各种视图配置进行预设，并允许您快速打开视图以查看特定 ARR 事件数据段。

![预设](./media/wpa-arr-trace.png)

上图显示了各种 ARR 特定事件的视图以及总体 CPU 利用率的视图。

## <a name="next-steps"></a>后续步骤

* [服务器端性能查询](../overview/features/performance-queries.md)
