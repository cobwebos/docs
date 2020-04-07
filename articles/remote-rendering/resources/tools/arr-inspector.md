---
title: Arr检查器检查工具
description: Arr 检查器工具的用户手册
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680071"
---
# <a name="the-arrinspector-inspection-tool"></a>Arr检查器检查工具

ArrInspector 是一个基于 Web 的工具，用于检查正在运行的 Azure 远程呈现会话。 它用于调试目的，用于检查正在呈现的场景的结构、显示日志消息和监视服务器上的实时性能。

![阿勒检验员](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>连接到 Arr 检查器

获取 ARR 服务器的主机名`mixedreality.azure.com`（以 ）结尾后，请使用[ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)进行连接。 此函数在应用程序`StartArrInspector.html`运行的设备上创建 。 要启动 ArrInspector，在 PC 上使用浏览器（边缘、火狐或 Chrome）打开该文件。 该文件仅有效 24 小时。

如果调用`ConnectToArrInspectorAsync`的应用已在 PC 上运行：

* 如果您使用的是 Unity 集成，它可能会自动为您启动。
* 否则，您将在*用户文件夹\\本地AppData\\[your_app]\\AC\\临时中*找到该文件。

如果应用在 HoloLens 上运行：

1. 使用[Windows 设备门户](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)访问 HoloLens。
1. 转到*系统>文件资源管理器*。
1. 导航到*用户文件夹\\本地AppData\\[your_app]\\\\AC 温度*。
1. 将*StartArr 检查器.html*保存到电脑。
1. 打开*StartArr 检查器.html*以加载会话的 Arr 检查器。

## <a name="the-performance-panel"></a>性能面板

![性能面板](./media/performance-panel.png)

此面板显示服务器公开的所有每帧性能值的图形。 这些值当前包括帧时间、FPS、CPU 和内存使用情况、内存统计信息（如总体 RAM 使用情况、对象计数等）。

要可视化这些参数之一，请单击"**添加新"** 按钮并选择对话框中显示的可用值之一。 此操作向面板添加新滚动图表，实时跟踪值。 在其右侧，您可以看到*最小*值、*最大值*和*当前*值。

但是，您可以通过使用鼠标拖动图形内容来平移图形，但是，只有在 ArrInspector 处于暂停状态时，才可以水平平移。

拖动时按住 CTRL，可以缩放。 水平变焦也可以用底部的滑块控制。

默认情况下，垂直范围根据当前显示的值进行计算，最小值和最大值显示在右侧的文本框中。 手动设置值时，通过直接在文本框中键入这些值，或者通过平移/缩放来设置这些值，图形将使用这些值。 要恢复自动垂直框架，请单击右上角的图标。

![垂直范围](./media/vertical-range.png)

## <a name="the-log-panel"></a>日志面板

![日志面板](./media/log-panel.png)

日志面板显示服务器端生成的日志消息的列表。 在连接时，它最多会显示 200 条以前的日志消息，并在它们发生时打印新的日志消息。

您可以使用顶部的按钮根据日志类型`[Error/Warning/Info/Debug]`筛选列表。
![日志筛选器按钮](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>计时数据捕获面板

![计时数据捕获](./media/timing-data-capture.png)

此面板用于从服务器捕获计时信息并下载它。 该文件使用[Chrome跟踪JSON格式](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)。 要检查数据，请打开 URL`Chrome://tracing`上的 Chrome，并将下载的文件拖放到页面。 计时数据在固定大小的环形缓冲区中连续收集。 写入时，捕获仅包含有关直接过去的信息，这意味着几秒钟到几分钟。

## <a name="the-scene-inspection-panel"></a>场景检查面板

![现场检查面板](./media/scene-inspection-panel.png)

此面板显示渲染场景的结构。 对象层次结构在左侧，所选对象的内容在右侧。 面板是只读的，并实时更新。

## <a name="the-vm-debug-information-panel"></a>VM 调试信息面板

![VM 调试信息面板](./media/state-debugger-panel.png)

此面板提供一些调试功能。

### <a name="restart-service"></a>重新启动服务

**重新启动服务**按钮重新启动 arrInspector 连接到的虚拟机上的运行时。 任何连接的客户端都将断开连接，并且必须重新加载 arrInspector 页才能连接到重新启动的服务。

### <a name="collect-debug-information"></a>收集调试信息

"**收集 VM 调试信息**"按钮将打开一个对话框，允许您触发 ARR 实例以在 VM 上收集调试信息：

![VM 调试信息对话框](./media/state-debugger-dialog.png)

调试信息可帮助 Azure 远程呈现团队分析正在运行的 ARR 实例中出现的任何问题。 该对话框具有一个文本字段，用于提供其他详细信息，例如重现问题的步骤。

单击"**开始收集"** 按钮后，对话框将关闭，收集过程将开始。 在 VM 上收集信息可能需要几分钟时间。

![VM 调试信息收集正在进行中](./media/state-debugger-panel-in-progress.png)

收集完成后，您将在 ArrInspector 窗口中收到通知。 此通知包含标识此特定集合的 ID。 请务必保存此 ID 以将其传递给 Azure 远程呈现团队。

![VM 调试信息收集成功](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> 您无法下载或以其他方式访问 VM 调试信息。 只有 Azure 远程呈现团队有权访问收集的数据。 您需要联系我们并发送收款 ID，以便我们调查您看到的问题。

## <a name="pause-mode"></a>暂停模式

在右上角，交换机允许您暂停面板的实时更新。 此模式可用于仔细检查特定状态。

![暂停模式](./media/pause-mode.png)

重新启用实时更新时，将重置所有面板。

## <a name="host-configuration"></a>主机配置

默认情况下，该工具连接到在同一主机上运行的 ARR 服务器，为 ArrInspector 提供服务。 但是，您可以将其配置为检查另一台服务器，前提是它运行的是打开工具端口的 ARR 实例。

为此，请访问标题栏左侧的主菜单，然后选择*主机配置*。 单击"**添加新主机**"，然后输入名称和主机名。 对于*主机名*，仅使用 以 结尾`.mixedreality.azure.com`的主机名，请`http://`不包含 或 端口。

![主机配置](./media/host-configuration.png)

要快速从一个主机切换到另一个主机，请使用右上角的下拉。

![主机组合](./media/host-switch-combo.png)

主机列表存储在浏览器本地存储中，因此在重新打开同一浏览器时将保留该列表。
