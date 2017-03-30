---
title: "用于开发人员分析的 Visual Studio 扩展发行说明"
description: "用于开发人员分析的 Visual Studio 工具的最新更新。"
services: application-insights
documentationcenter: 
author: acearun
manager: carmonm
ms.assetid: 2001db30-efc5-417a-a413-93c1b218975f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: aruna
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 5edd37a3a042f710e7d1b9e148c24d9a548a4fea
ms.lasthandoff: 03/22/2017


---
# <a name="release-notes-for-developer-analytics-tools"></a>开发人员分析工具发行说明

## <a name="version-718-visual-studio-2015"></a>版本 7.18 (Visual Studio 2015)

* 重新设计了 Toast 通知。
* 在 Application Insights 搜索中，“不”筛选事件的详细信息视图。
* Bug 修复

## <a name="version-86-visual-studio-2017-rtw-and-rc4-and-version-717-visual-studio-2015"></a>版本 8.6（Visual Studio 2017 RTW 和 RC4）及版本 7.17 (Visual Studio 2015)

* 通过 Visual Studio 发布应用时，现可在 Azure 门户的指标资源浏览器中对数据进行注释标记
* 现可将标记添加到代码文件的滚动条中，对应于 Application Insights 中的红色和黄色 CodeLens 警告
* 更新了“配置”窗口中的定价信息
* Bug 修复

[请参阅此处的详细说明](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#devanalytics)

## <a name="version-716-visual-studio-2015"></a>版本 7.16 (Visual Studio 2015)

* Bug 修复

## <a name="version-85-visual-studio-2017-rc3-and-version-715-visual-studio-2015"></a>版本 8.5 (Visual Studio 2017 RC3) 及版本 7.15 (Visual Studio 2015)

* CodeLens 现显示发送数据到 Application Insights 资源的项目中的调试和实时遥测数据
* “配置”窗口中现显示 Application Insights 定价信息
* 针对请求和异常的 CodeLens 现支持用 Visual Basic 编写的 ASP.NET 项目
* Application Insights 搜索现显示已采样事件的未采样事件计数
* Bug 修复

## <a name="version-714-visual-studio-2015"></a>版本 7.14 (Visual Studio 2015)

* 针对可用性（Web 测试）和页面视图事件的搜索支持
* 针对可用性（Web 测试）和页面视图事件的趋势支持
* 针对 SDK 自适应采样的诊断工具和事件详细信息标签
* Bug 修复

## <a name="version-712-visual-studio-2015"></a>版本 7.12 (Visual Studio 2015)

* 新的发布通知格式
* Bug 修复

## <a name="version-84-visual-studio-2017-rc2-and-version-711-visual-studio-2015"></a>版本 8.4 (Visual Studio 2017 RC2) 及版本 7.11 (Visual Studio 2015)

* CodeLens 显示项目利用 Application Insights SDK 进行本地调试会话的请求
* CodeLens 可直接转到应用程序分析查看用户影响
* 可插入 JavaScript 收集页面视图
* Bug 修复

## <a name="version-710-visual-studio-2015"></a>版本 7.10 (Visual Studio 2015)

* Application Insights“配置”窗口的新设计
* Bug 修复

## <a name="version-79-visual-studio-2015"></a>版本 7.9 (Visual Studio 2015)

* CodeLens 显示在项目利用 Application Insights SDK 进行本地调试会话期间发生的异常
* Bug 修复

## <a name="version-83-visual-studio-2017-rc-and-version-78-visual-studio-2015"></a>版本 8.3 (Visual Studio 2017 RC) 及版本 7.8 (Visual Studio 2015)

* 在“配置”窗口添加 Application Insights 的新体验
* Bug 修复

## <a name="version-77-visual-studio-2015"></a>版本 7.7 (Visual Studio 2015)

* 利用自定义 ASP.NET 路由从遥测事件更准确地映射到方法
* Bug 修复

## <a name="version-76-visual-studio-2015"></a>版本 7.6 (Visual Studio 2015)

* 分析新“跟踪操作”选项卡上的操作所涉及的事件，该选项卡针对搜索工具中的事件
* Bug 修复

## <a name="version-75-visual-studio-2015"></a>版本 7.5 (Visual Studio 2015)

* 在诊断工具中显示请求的生产遥测信息
* 通过搜索工具中的相关项创建工作项
* Bug 修复

## <a name="version-74-visual-studio-2015"></a>版本 7.4 (Visual Studio 2015)

* “趋势”中的筛选器窗格现可调整大小
* Bug 修复

## <a name="version-73-visual-studio-2015"></a>版本 7.3 (Visual Studio 2015)

* CodeLens 中的请求
* “配置”窗口
* HockeyApp SDK 更新为 v4.2.2
* Bug 修复

## <a name="version-72-visual-studio-2015"></a>版本 7.2 (Visual Studio 2015)

* Bug 修复

## <a name="version-71-visual-studio-2015"></a>版本 7.1 (Visual Studio 2015)

* Application Insights 趋势中的遥测就绪状态指示器
* Bug 修复

## <a name="version-70"></a>版本 7.0
### <a name="azure-application-insights-trends"></a>Azure Application Insights 趋势
Azure Application Insights 是 Visual Studio 中的新工具，可以用来帮助分析应用随着时间的推移如何运行。 若要开始操作，请在“Application Insights”工具栏按钮上或“Application Insights 搜索”窗口中选择“浏览遥测趋势”。 或者，在“视图”菜单上，单击“其他窗口”，然后单击“Application Insights 趋势”。 选择五种常见查询中的一个，以便开始使用。 可以根据遥测类型、时间范围和其他属性分析不同的数据集。 若要查找数据中的异常情况，请选择“视图类型”下拉列表下的其中一个异常情况选项。 使用窗口底部的筛选选项，可轻松地全力探索遥测的特定子集。

![Application Insights 趋势](./media/app-insights-release-notes-vsix/Trends.png)

### <a name="exceptions-in-codelens"></a>CodeLens 中的异常
异常遥测现在显示在 CodeLens 中。 如果已将项目连接到 Application Insights 服务，你将看到在过去 24 小时内生产期间每个方法中发生的异常数。 通过 CodeLens，可以跳转到“搜索”或“趋势”，更详细地调查异常。

![CodeLens 中的异常](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### <a name="aspnet-core-support"></a>ASP.NET Core 支持
Application Insights 现在支持 Visual Studio 中的 ASP.NET Core RC2 项目。 可以将 Application Insights 从“新建项目”对话框添加到新的 ASP.NET Core RC2 项目，如以下屏幕截图所示。 或者，可以将它添加到现有项目，右键单击解决方案资源管理器中的项目，然后单击“添加 Application Insights 遥测”。

![ASP.NET Core 支持](./media/app-insights-release-notes-vsix/NetCoreSupport.png)

ASP.NET 5 RC1 和 ASP.NET Core RC2 项目还在“诊断工具”窗口中有新的支持。 当以本地方式在电脑上调试时，你将看到 Application Insights 事件，例如来自 ASP.NET 应用的请求和异常。 从每个事件，单击“搜索”向下钻取以获取详细信息。

![诊断工具支持](./media/app-insights-release-notes-vsix/DiagnosticTools.png)

### <a name="hockeyapp-for-universal-windows-apps"></a>适用于通用 Windows 应用的 HockeyApp
除了 Beta 分发和用户反馈外，HockeyApp 还为通用 Windows 应用提供符号化的故障报告。 我们甚至使添加 HockeyApp SDK 变得更加容易：右键单击通用 Windows 项目，然后单击“Hockey 应用 - 启用故障分析”。 这将安装 SDK、设置故障收集，并在云中预配 HockeyApp 资源，所有这些操作无需将应用上载到 HockeyApp 服务即可完成。

其他新增功能：

* 我们已使 Application Insights 搜索体验变得更快且更直观。 现在，当你选择时间范围和详细信息筛选器时，它们将自动应用。
* 并且在 Application Insights 搜索中，现在可以选择从请求遥测直接跳转至代码。
* 我们已对 HockeyApp 登录体验做了改进。
* 在诊断工具中，将显示异常的生产遥测信息。

## <a name="version-52"></a>版本 5.2
我们非常荣幸地宣布推出 Visual Studio 中的 HockeyApp 方案。 第一个集成是在通用 Windows 应用程序的 Beta 分发和 Visual Studio 中的 Windows 窗体应用。

借助 Beta 分发，可以将早期版本的应用上载到 HockeyApp 以便分发给选定客户或测试人员的子集。 通过将 Beta 分发与 HockeyApp 故障收集和用户反馈功能结合使用，可以为你提供关于应用在广泛发布前的有价值信息。 可以使用此信息来解决应用相关的问题，从而可以避免或尽可能减少将来出现的问题，例如较低的应用评级、负面反馈等。

看一下从 Visual Studio 中上载 Beta 分发的版本有多么简单。

### <a name="universal-windows-apps"></a>通用 Windows 应用程序
通用 Windows 应用项目节点的上下文菜单现在包含一个选项，可以将版本上载到 HockeyApp。

![适用于通用 Windows 应用的项目上下文菜单](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

选择项，HockeyApp 上载对话框将打开。 需要一个 HockeyApp 帐户来上载版本。 如果你是新用户，不必担心。 创建帐户的过程很简单。

连接时，将在对话框中看到上载表单。

![通用 Windows 应用的上载对话框](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

选择要上载的内容（.appxbundle 或 .appx 文件），然后在向导中选择发布选项。 或者，可以在下一页上添加发行说明。 选择“完成”开始上载。

上载完成后，将出现具有确认信息的 HockeyApp 通知和指向 HockeyApp 门户中应用的链接。

![上载完成通知](./media/app-insights-release-notes-vsix/UploadComplete.png)

就这么简单！ 只需几次单击，即可上载 Beta 分发的版本。

可以通过多种方式在 HockeyApp 门户中管理应用程序。 这包括邀请用户、查看故障报告和反馈、更改详细信息等。

![HockeyApp 门户](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

有关应用管理的更多详细信息，请参阅 [HockeyApp 知识库](http://support.hockeyapp.net/kb/app-management-2)。

### <a name="windows-forms-apps"></a>Windows 窗体应用
Windows 窗体项目节点的上下文菜单现在包含一个选项，可以将版本上载到 HockeyApp。

![适用于 Windows 窗体应用的项目上下文菜单](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

这将打开 HockeyApp 上载对话框，它类似于通用 Windows 应用中的对话框。

![Windows 窗体应用的上载对话框](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

请注意本向导中的新字段，用于指定应用版本。 对于通用 Windows 应用，通过清单来填充信息。 遗憾的是，Windows 窗体应用不具有此功能的等效项。 你将需要手动指定它们。

流程的其余部分与通用 Windows 应用类似：选择生成和发布选项，添加版本说明，然后在 HockeyApp 门户中进行上载和管理。

过程如此简单。 试一下，并告诉我们你的想法。

## <a name="version-43"></a>版本 4.3
### <a name="search-telemetry-from-local-debug-sessions"></a>搜索本地调试会话中的遥测
借助此版本，现在可以搜索在 Visual Studio 调试会话中生成的 Application Insights 遥测。 过去，你只能在已使用 Application Insights 注册应用时使用搜索。 现在，应用只需装有 Application Insights SDK 即可搜索本地遥测。

如果你的 ASP.NET 应用程序具有 Application Insights SDK，请执行以下步骤以使用搜索。

1. 调试应用程序。
2. 通过以下方式之一打开 Application Insights 搜索：
   * 在“视图”菜单上，单击“其他窗口”，然后单击“Application Insights 搜索”。
   * 单击“Application Insights”工具栏按钮。
   * 在解决方案资源管理器中，展开 **ApplicationInsights.config**，然后单击“搜索调试会话遥测”。
3. 如果尚未使用 Application Insights 注册，“搜索”窗口将在调试会话遥测模式下打开。
4. 单击“搜索”图标，查看本地遥测。

![上载完成](./media/app-insights-release-notes-vsix/LocalSearch.png)

## <a name="version-42"></a>版本 4.2
在此版本中，我们添加了相关功能，使得在事件上下文中搜索数据变得更容易，能够从更多数据事件跳转到代码，以及实现将日志记录数据发送到 Application Insights 的轻松体验。 此扩展每月更新一次。 若有反馈或功能请求，请将其发送到 aidevtools@microsoft.com。

### <a name="no-click-logging-experience"></a>无单击日志记录体验
如果已经在使用 NLog、log4net 或 System.Diagnostics.Tracing，则不必对将所有跟踪记录移动到 Application Insights 而担心。 在此版本中，我们集成了 Application Insights 日志记录适配器与常规配置体验。
如果已经配置了其中一个记录框架，以下部分将介绍如何获取它。
**如果已经添加 Application Insights：**

1. 右键单击项目节点，然后依次单击“Application Insights”和“配置 Application Insights”。 确保看到选项，以在配置窗口中添加正确的适配器。
2. 或者，在生成解决方案时，留意显示在屏幕右上角的弹出窗口，并单击“配置”。

![日志记录通知](./media/app-insights-release-notes-vsix/LoggingToast.png)

在已安装日志记录适配器后，运行应用程序，并确保在诊断工具选项卡中看到数据，如下所示：

![跟踪](./media/app-insights-release-notes-vsix/Traces.png)

### <a name="jump-to-or-find-the-code-where-the-telemetry-event-property-is-emitted"></a>跳转到或找到其中发出遥测事件属性的代码
借助新版本，用户可以单击事件详细信息中的任何值，将搜索当前打开的解决方案中的匹配字符串。 结果将显示在 Visual Studio“查找结果”列表中，如下所示：

![查找匹配项](./media/app-insights-release-notes-vsix/FindMatch.png)

### <a name="new-search-window-for-when-you-are-not-signed-in"></a>未登录时的新搜索窗口
我们改进了 Application Insights 搜索窗口的外观，从而有助于在应用处于生产环境时搜索数据。

![搜索窗口](./media/app-insights-release-notes-vsix/SearchWindow.png)

### <a name="see-all-telemetry-events-associated-with-the-event"></a>查看与事件关联的所有遥测事件
我们添加了新的选项卡，可提供对与用户所查看遥测事件相关的所有数据的预定义查询，它位于事件详细信息选项卡旁。 例如，某一请求具有名为**操作 ID** 的字段。 与此请求关联的每个事件的**操作 ID** 值都相同。 如果在操作处理请求时发生异常，将为异常提供与请求相同的操作 ID 以便于查找。 如果正在查找某一请求，请单击“此操作的所有遥测”打开新选项卡以显示新的搜索结果。

![相关项](./media/app-insights-release-notes-vsix/RelatedItems.png)

### <a name="forward-and-back-history-in-search"></a>前进和后退搜索中的历史记录
现在，可以在搜索结果之间来回切换。

![回退](./media/app-insights-release-notes-vsix/GoBAck.png)

## <a name="version-41"></a>4.1 版
此版本附带了大量新功能和更新。 需要已安装 Update 1 才能安装此版本。

### <a name="jump-from-an-exception-to-method-in-source-code"></a>从异常跳转到源代码中的方法
现在，如果要在 Application Insights 搜索窗口中查看生产应用中的异常，可以跳转到代码中出现异常的方法。 只需加载正确的项目即可，剩余的工作将由 Application Insights 完成！ （若要了解有关 Application Insights 搜索窗口的详细信息，请参阅以下部分中版本 4.0 的发行说明。）

工作原理 即使在未打开解决方案时，也可以使用 Applications Insights 搜索。 堆栈跟踪区域显示信息消息，并且堆栈跟踪中的许多项不可用。

如果文件信息可用，某些项可能是链接，但解决方案信息项仍然可见。

如果单击超链接，将跳转到代码中选定方法的位置。 版本号可能存在差异，但跳转至代码的正确版本的功能将在更高版本中引入。

![单击异常详细信息](./media/app-insights-release-notes-vsix/jumptocode.png)

### <a name="new-entry-points-to-the-search-experience-in-solution-explorer"></a>解决方案资源管理器中指向搜索体验的新入口点
现在，可以通过解决方案资源管理器来访问搜索。

![在解决方案资源管理器中搜索](./media/app-insights-release-notes-vsix/searchentry.png)

### <a name="displays-a-notification-when-publish-is-completed"></a>发布完成时显示通知
当项目联机发布时，将显示弹出对话框，以便可以在生产中查看 Application Insights 数据。

![发布完成通知](./media/app-insights-release-notes-vsix/publishtoast.png)

## <a name="version-40"></a>4.0 版
### <a name="search-application-insights-data-from-within-visual-studio"></a>从 Visual Studio 中搜索 Application Insights 数据
与 Application Insights 门户中的搜索功能一样，现在可以在 Visual Studio 中按事件类型、属性值和文本进行筛选和搜索，然后检查各个事件。

![搜索窗口](./media/app-insights-release-notes-vsix/search.png)

### <a name="see-data-coming-from-your-local-computer-in-diagnostic-tools"></a>使用诊断工具查看来自本地计算机的数据
除了其他调试数据外，还可以在 Visual Studio 诊断工具页上查看遥测。 仅支持 ASP.NET 4.5。

![诊断工具页](./media/app-insights-release-notes-vsix/diagtools.png)

### <a name="add-the-sdk-to-your-project-without-signing-in-to-azure"></a>在不登录到 Azure 的情况下向项目添加 SDK
无需登录到 Azure，即可通过“新建项目”对话框或从项目上下文菜单将 Application Insights 程序包添加到项目。 如果确实登录，将安装 SDK 并将它配置为像以前那样将遥测发送到门户。 如果未登录，SDK 将添加到项目，并将为诊断中心生成遥测。 在以后可以根据需要进行配置。

![“新建项目”对话框](./media/app-insights-release-notes-vsix/newproject.png)

### <a name="device-support"></a>设备支持
在 *Connect();* 2015 中，我们已[宣布](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)设备的移动开发人员体验即为 HockeyApp。 HockeyApp 可帮助你将 Beta 版本分发给测试人员、收集和分析应用中的所有故障，并直接从客户那里收集反馈。
在选择用来生成应用的任何一个平台（无论是 iOS、Android、Windows 还是 Xamarin、Cordova 或 Unity 之类的跨平台解决方案）上，HockeyApp 都支持该应用。

在将来版本的 Application Insights 扩展中，我们将在 HockeyApp 和 Visual Studio 之间引入更加集成的体验。 现在，你可以通过简单地添加 NuGet 引用来开始操作。 有关详细信息，请参阅[文档](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)。

