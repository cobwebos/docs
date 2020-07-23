---
title: ArrInspector 检测工具
description: ArrInspector 工具的用户手册
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80680071"
---
# <a name="the-arrinspector-inspection-tool"></a>ArrInspector 检测工具

ArrInspector 是一种基于 web 的工具，用于检查正在运行的 Azure 远程呈现会话。 它旨在用于调试目的，检查要呈现的场景的结构，显示日志消息，并监视服务器上的实时性能。

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>连接到 ArrInspector

获取 ARR 服务器的主机名（以结尾 `mixedreality.azure.com` ）后，使用[ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector)进行连接。 此函数 `StartArrInspector.html` 在运行应用程序的设备上创建一个。 若要启动 ArrInspector，请在 PC 上使用浏览器（Edge、Firefox 或 Chrome）打开该文件。 此文件的有效期为24小时。

如果调用的应用 `ConnectToArrInspectorAsync` 已在电脑上运行：

* 如果你使用的是 Unity 集成，它可能会自动启动。
* 否则，你会在*用户文件夹 \\ LocalAppData \\ [your_app] \\ AC \\ Temp*中找到该文件。

如果应用在 HoloLens 上运行：

1. 使用[Windows 设备门户](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal)访问 HoloLens。
1. 请在*文件资源管理器中转到系统 >*。
1. 导航到*用户文件夹 \\ LocalAppData \\ [your_app] \\ AC \\ Temp*。
1. 将*StartArrInspector.htm*保存到你的电脑。
1. 打开*StartArrInspector.html*加载会话的 ArrInspector。

## <a name="the-performance-panel"></a>性能面板

![性能面板](./media/performance-panel.png)

此面板显示服务器公开的所有每帧性能值的关系图。 这些值当前包括帧时间、FPS、CPU 和内存使用情况、内存统计信息（如总体 RAM 使用量、对象计数等）。

若要可视化其中一个参数，请单击 "**添加新**项" 按钮，然后选择对话框中显示的可用值之一。 此操作会向面板中添加一个新的滚动图表，并实时跟踪这些值。 在其右侧，可以看到*最小*值、*最大*值和*当前*值。

您可以通过使用鼠标拖动图形的内容来平移图形，但是，仅当 ArrInspector 处于暂停状态时，才可以水平平移。

按住 CTRL 的同时拖动即可实现缩放。 还可以用底部的滑块来控制水平缩放。

默认情况下，垂直范围基于当前显示的值进行计算，最小值和最大值显示在右侧的文本框中。 手动设置值时，无论是通过直接在文本框中键入值，还是通过平移/缩放，图形都将使用这些值。 若要还原自动垂直框架，请单击右上角的图标。

![垂直范围](./media/vertical-range.png)

## <a name="the-log-panel"></a>日志面板

![日志面板](./media/log-panel.png)

日志面板显示服务器端生成的日志消息列表。 连接时，它最多显示200以前的日志消息，并在它们发生时打印新的日志消息。

您可以 `[Error/Warning/Info/Debug]` 使用顶部的按钮来筛选基于日志类型的列表。
![日志筛选器按钮](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>计时数据捕获面板

![计时数据捕获](./media/timing-data-capture.png)

此面板用于从服务器捕获计时信息并进行下载。 该文件使用[Chrome 跟踪 JSON 格式](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit)。 若要检查数据，请打开该 URL 上的 Chrome， `Chrome://tracing` 然后将下载的文件拖放到页面中。 计时数据在固定大小的环形缓冲区中持续收集。 写出后，捕获只包含有关过去的信息，这意味着几秒钟到几分钟的时间。

## <a name="the-scene-inspection-panel"></a>场景检查面板

![场景检查面板](./media/scene-inspection-panel.png)

此面板显示所呈现场景的结构。 对象层次结构位于左侧，所选对象的内容位于右侧。 该面板为只读，并将进行实时更新。

## <a name="the-vm-debug-information-panel"></a>VM 调试信息面板

![VM 调试信息面板](./media/state-debugger-panel.png)

此面板提供一些调试功能。

### <a name="restart-service"></a>重新启动服务

"**重新启动服务**" 按钮将重新启动 arrInspector 连接到的虚拟机上的运行时。 任何连接的客户端将断开连接，并且必须重新加载 arrInspector 页面以连接到重新启动的服务。

### <a name="collect-debug-information"></a>收集调试信息

"**收集 vm 的调试信息**" 按钮将打开一个对话框，通过该对话框可以触发 ARR 实例，以收集 VM 上的调试信息：

![VM 调试信息对话框](./media/state-debugger-dialog.png)

调试信息可帮助 Azure 远程呈现团队分析正在运行的 ARR 实例中发生的任何问题。 此对话框包含一个文本字段，可提供其他详细信息，例如，重现问题的步骤。

单击 "**开始收集**" 按钮后，该对话框将关闭，并且收集过程将开始。 收集 VM 上的信息可能需要几分钟的时间。

![VM 调试信息收集正在进行](./media/state-debugger-panel-in-progress.png)

收集完成后，你将在 "ArrInspector" 窗口中收到通知。 此通知包含标识此特定集合的 ID。 请务必保存此 ID，以便将其传递到 Azure 远程呈现团队。

![VM 调试信息收集成功](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> 无法下载或以其他方式访问 VM 调试信息。 只有 Azure 远程呈现团队有权访问所收集的数据。 你需要与我们联系并发送集合 ID，以便我们调查你所看到的问题。

## <a name="pause-mode"></a>暂停模式

在右上角，可以使用开关来暂停面板的实时更新。 此模式可用于仔细检查特定状态。

![暂停模式](./media/pause-mode.png)

重新启用实时更新时，将重置所有面板。

## <a name="host-configuration"></a>主机配置

默认情况下，该工具连接到在为 ArrInspector 提供服务的同一主机上运行的 ARR 服务器。 但是，你可以将其配置为检查其他服务器，假定它在打开工具端口的情况下运行 ARR 实例。

为此，请访问标题栏左侧的主菜单，并选择 "*主机配置*"。 单击 "**添加新主机**"，然后输入名称和主机名。 对于*主机名*，请使用以结尾的主机名 `.mixedreality.azure.com` ，不要包含 `http://` 或端口。

![主机配置](./media/host-configuration.png)

若要从一台主机快速切换到另一台主机，请使用右上角的下拉箭头。

![主机组合框](./media/host-switch-combo.png)

主机列表存储在浏览器的本地存储中，因此重新打开相同的浏览器时将保留该列表。
