---
title: 监视任何网站的可用性和响应能力 | Microsoft Docs
description: 在 Application Insights 中设置 Web 测试。 当网站不可用或响应速度缓慢时接收警报。
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670026"
---
# <a name="monitor-the-availability-of-any-website"></a>监视任何网站的可用性

部署 web 应用/网站之后，可以设置重复测试来监视可用性和响应能力。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 将来自全球各地的 Web 请求定期发送到应用程序。 如果你的应用程序未响应，或响应速度太慢，则会发出警报。

对于可以从公共 Internet 访问的任何 HTTP 或 HTTPS 终结点，均可设置可用性测试。 您无需对正在测试的网站进行任何更改。 事实上，它甚至无需成为你拥有的站点。 可以测试服务所依赖的 REST API 的可用性。

### <a name="types-of-availability-tests"></a>可用性测试的类型：

有三种类型的可用性测试：

* [URL ping 测试](#create-a-url-ping-test)：可以在 Azure 门户中创建的简单测试。
* [多步骤 web 测试](availability-multistep.md)：记录一系列 web 请求，可以播放这些请求来测试更复杂的方案。 多步骤 web 测试在 Visual Studio Enterprise 中创建并上载到门户以执行。
* [自定义跟踪可用性测试](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)：如果你决定创建自定义应用程序以运行可用性测试，则可以使用 `TrackAvailability()` 方法将结果发送到 Application Insights。

**对于每个 Application Insights 资源，最多可以创建100个可用性测试。**

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

若要创建可用性测试，首先需要创建一个 Application Insights 资源。 如果已创建资源，请转到下一节，[创建 URL Ping 测试](#create-a-url-ping-test)。

从 "Azure 门户中，选择"**创建资源**" > **开发人员工具** > " **Application Insights**并[创建 Application Insights 资源](create-new-resource.md)。

## <a name="create-a-url-ping-test"></a>创建 URL ping 测试

名称 "URL ping 测试" 是 misnomer 的一个小数位。 为清楚起见，此测试不会将 ICMP （Internet 控制消息协议）用于检查站点的可用性。 而是使用更高级的 HTTP 请求功能来验证终结点是否正在响应。 它还测量与该响应关联的性能，并添加设置自定义成功条件和更高级功能（例如分析从属请求和允许重试）的功能。

若要创建第一个可用性请求，请打开 "可用性" 窗格并选择 "**创建测试**"。

![至少填写网站的 URL](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>创建测试

|设置| 说明
|----|----|----|
|**URL** |  URL 可以是要测试的任何网页，但必须从公共 internet 中查看。 该 URL 可以包括查询字符串。 因此，例如，可以稍微训练一下数据库。 如果 URL 解析为重定向，最多可以跟踪 10 个重定向。|
|**分析从属请求**| 测试请求图像、脚本、样式文件以及其他属于受测网页的文件。 记录的响应时间包括获取这些文件所耗费的时间。 如果在整个测试的超时时间内无法成功下载这些资源中的任何资源，则测试将失败。 如果不选中此选项，则测试只请求指定 URL 的文件。 启用此选项将导致更严格的检查。 在手动浏览站点时，测试可能会失败，这可能不明显。
|**启用重试**|如果测试失败，则会在短时间间隔后重试。 仅当连续三次尝试失败时，才报告失败。 然后，将按照一般的测试频率执行后续测试。 重试会暂停，直到下次成功为止。 可在每个测试位置单独应用此规则。 **建议选择此选项**。 平均大约有 80% 的失败可在重试后消除。|
|**测试频率**| 设置从每个测试位置运行测试的频率。 如果有五个测试位置，且默认频率为五分钟，则平均每隔一分钟测试站点一次。|
|**测试位置**| 是服务器从其将 Web 请求发送到 URL 的位置。 **建议的最小测试位置数为 5** ，以确保可以将网站中的问题与网络问题区分开来。 最多可以选择 16 个位置。

**如果你的 URL 在公共 internet 上不可见，则可以选择有选择地打开防火墙，只允许测试事务通过**。 若要了解有关我们的可用性测试代理的防火墙例外的详细信息，请参阅[IP 地址指南](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)。

> [!NOTE]
> 我们强烈建议从多个位置进行测试，**最少使用 5**个位置。 这是为了防止可能由特定位置的暂时性问题导致的虚假警报。 此外，我们发现，最佳配置是将**测试位置数等于警报位置阈值 + 2**。

### <a name="success-criteria"></a>成功条件

|设置| 说明
|----|----|----|
| **测试超时** |减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源。|
| **HTTP 响应** | 视为成功的返回状态代码。 代码 200 指示返回了正常网页。|
| **内容匹配** | 类似于“欢迎!”的字符串。 我们测试区分大小写的匹配项是否出现在每个响应中。 它必须是不带通配符的纯字符串。 别忘了，如果页面内容更改，可能需要更新。 **内容匹配仅支持英文字符** |

### <a name="alerts"></a>警报

|设置| 说明
|----|----|----|
|**近乎实时（预览）** | 我们建议使用近实时警报。 在创建可用性测试后会配置此类警报。  |
|**经典** | 我们不再建议对新的可用性测试使用经典警报。|
|**警报位置阈值**|建议最少 3/5 个位置。 警报位置阈值和测试位置数量之间的最佳关系是**警报位置阈值** = **测试位置数-2，最少有5个测试位置。**|

## <a name="see-your-availability-test-results"></a>查看可用性测试结果

可通过折线图和散点图视图对可用性测试结果进行可视化。

几分钟后，单击 "**刷新**" 以查看测试结果。

![行视图](./media/monitor-web-app-availability/availability-refresh-002.png)

散点图视图显示了其中包含诊断测试步骤详细信息的测试结果的示例。 测试引擎存储已失败的测试的诊断详细信息。 对于成功的测试，将存储执行子集的诊断详细信息。 将鼠标悬停在任何绿色/红色点上可查看测试、测试名称和位置。

![行视图](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

选择特定测试、位置或减少时间段，可查看围绕感兴趣的时间段的更多结果。 使用搜索资源管理器以查看所有执行结果，或者使用分析查询以针对此数据运行自定义报告。

## <a name="inspect-and-edit-tests"></a>检查和编辑测试

若要编辑、暂时禁用或删除测试，请单击测试名称旁的省略号。 进行更改后，将配置更改传播到所有测试代理可能需要长达20分钟的时间。

![查看测试详细信息。 编辑和禁用 web 测试](./media/monitor-web-app-availability/edit.png)

对服务执行维护时，可能需要禁用可用性测试或与这些测试关联的警报规则。

## <a name="if-you-see-failures"></a>如果出现故障

单击红点。

![单击红点](./media/monitor-web-app-availability/open-instance-3.png)

从可用性测试结果中，可以看到所有组件的事务详细信息。 在此门户中，可以：

* 检查从服务器收到的响应。
* 使用在处理失败的可用性测试时收集的相关服务器端遥测数据进行故障诊断。
* 在 Git 或 Azure Boards 中记录问题或工作项以跟踪问题。 Bug 中将包含转至此事件的链接。
* 在 Visual Studio 中打开 Web 测试结果。

从[此处](../../azure-monitor/app/transaction-diagnostics.md)详细了解端到端事务诊断体验。

单击异常行可查看导致综合可用性测试失败的服务器端异常的详细信息。 还可以获取[调试快照](../../azure-monitor/app/snapshot-debugger.md)，进行更丰富的代码级诊断。

![服务器端诊断](./media/monitor-web-app-availability/open-instance-4.png)

除了原始结果外，还可以在[指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)中查看两个关键可用性指标：

1. 可用性：已成功的测试占执行的所有测试的百分比。
2. 测试持续时间：执行的所有测试的平均测试持续时间。

## <a name="automation"></a>自动化

* [使用 PowerShell 脚本自动设置可用性测试](../../azure-monitor/app/powershell.md#add-an-availability-test)。
* 设置在引发警报时调用的 [webhook](../../azure-monitor/platform/alerts-webhooks.md) 。

## <a name="troubleshooting"></a>故障排除

专用[故障排除文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [多步骤 web 测试](availability-multistep.md)


