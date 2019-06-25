---
title: 监视任何网站的可用性和响应能力 | Microsoft Docs
description: 在 Application Insights 中设置 Web 测试。 当网站不可用或响应速度缓慢时接收警报。
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303724"
---
# <a name="monitor-the-availability-of-any-website"></a>监视任何网站的可用性

已部署到你的 web 应用/网站后，你可以设置重复执行测试来监视可用性和响应能力。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 将来自全球各地的 Web 请求定期发送到应用程序。 如果你的应用程序没有响应，或它的响应速度太慢，它可以通知你。

对于可以从公共 Internet 访问的任何 HTTP 或 HTTPS 终结点，均可设置可用性测试。 您无需对要测试的网站进行任何更改。 事实上，它甚至不必是你拥有的站点。 你可以测试一种 REST API，取决于你的服务的可用性。

### <a name="types-of-availability-tests"></a>可用性测试的类型：

有三种类型的可用性测试：

* [URL ping 测试](#create-a-url-ping-test)：可以在 Azure 门户中创建的简单测试。
* [多步骤 web 测试](availability-multistep.md):可在播放测试更复杂的方案的 web 请求的序列的录制。 在 Visual Studio Enterprise 中创建和上传到门户，以便执行多步骤 web 测试。
* [自定义跟踪可用性测试](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet):`TrackAvailability()`方法可以用于创建您自己的自定义的可用性测试。

**您可以创建每个 Application Insights 资源最多 100 个可用性测试。**

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

若要创建可用性测试，首先需要创建 Application Insights 资源。 如果已创建资源后，请转到下一部分[创建 URL Ping 测试](#create-a-url-ping-test)。

从 Azure 门户中，选择**创建资源** > **开发人员工具** > **Application Insights**和[创建Application Insights 资源](create-new-resource.md)。

## <a name="create-a-url-ping-test"></a>创建 URL ping 测试

名称"URL ping 测试"有点用词不当。 为了清楚起见，此测试未发出任何使用 ICMP （Internet 控制消息协议） 来检查站点的可用性。 而是使用更高级的 HTTP 请求功能验证是否正在响应终结点。 它还测量与该响应关联的性能，还能以设置自定义的成功条件结合更高级功能，如分析从属请求，并允许重试。

若要创建第一个可用性请求，请打开可用性窗格中，然后选择**创建测试**。

![至少填写网站的 URL](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>创建测试

|设置| 说明
|----|----|----|
|**URL** |  URL 可以是任何网页，你想要测试，但它必须是公共 internet 中可见。 该 URL 可以包括查询字符串。 因此，例如，可以稍微训练一下数据库。 如果 URL 解析为重定向，最多可以跟踪 10 个重定向。|
|**分析从属请求**| 测试将请求图像、 脚本、 样式文件和属于受测网页一部分的其他文件。 记录的响应时间包括获取这些文件所耗费的时间。 如果任何这些资源不能成功下载整个测试的超时时间内，则测试失败。 如果不选中此选项，则测试只请求指定 URL 的文件。 启用此选项会导致更严格的检查。 测试可能会失败的情况下，手动浏览站点时可能不明显。
|**启用重试**|当测试失败时，会在短时间后重试。 仅当连续三次尝试失败时，才报告失败。 然后，将按照一般的测试频率执行后续测试。 重试会暂停，直到下次成功为止。 可在每个测试位置单独应用此规则。 **我们建议使用此选项**。 平均大约有 80% 的失败可在重试后消除。|
|**测试频率**| 设置从每个测试位置运行测试的频率。 如果有五个测试位置，且默认频率为五分钟，则平均每隔一分钟测试站点一次。|
|**测试位置**| 是从我们的服务器将 web 请求发送到你的 URL 的位置的位置。 **建议的测试位置我们最小数量为 5**以确保，你可以区分问题在你的网站与网络问题。 最多可以选择 16 个位置。

**如果你的 URL 不是从公共 internet 可见的您可以选择有选择地打开防火墙，以允许仅通过的测试事务**。 若要了解有关我们的可用性测试代理的防火墙例外情况的详细信息，请查阅[IP 地址指南](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests)。

> [!NOTE]
> 我们强烈建议您从多个位置测试**至少五个不同位置**。 这是为了防止可能由特定位置的暂时性问题导致的虚假警报。 此外我们发现的最佳配置是能够**测试的位置数目将等于警报位置阈值 + 2**。

### <a name="success-criteria"></a>成功标准

|设置| 说明
|----|----|----|
| **测试超时** |减少此值可以接收有关响应变慢的警报。 如果未在这段时间内收到站点的响应，则将测试视为失败。 如果选择了“分析依赖请求”，则必须在这段时间内收到所有图像、样式文件、脚本和其他依赖资源  。|
| **HTTP 响应** | 视为成功的返回状态代码。 代码 200 指示返回了正常网页。|
| **内容匹配** | 一个字符串，类似于"欢迎 ！" 我们测试区分大小写的匹配项是否出现在每个响应中。 它必须是不带通配符的纯字符串。 别忘了，如果页面内容更改，可能需要更新。 **只能使用英文字符支持与内容匹配** |

### <a name="alerts"></a>警报

|设置| 说明
|----|----|----|
|**准实时 （预览）** | 我们建议使用准实时警报。 创建可用性测试后，完成配置此类警报。  |
|**经典** | 我们不建议使用经典警报的新的可用性测试。|
|**警报位置阈值**|建议最少 3/5 个位置。 警报位置阈值和测试位置数之间的最佳关系非常**警报位置阈值** = **数最少的五个测试位置测试位置-2。**|

## <a name="see-your-availability-test-results"></a>查看可用性测试结果

可用性测试结果可以与折线图和散点图视图进行可视化。

几分钟后，单击**刷新**若要查看测试结果。

![行视图](./media/monitor-web-app-availability/availability-refresh-002.png)

散点图视图中显示的测试结果中都有诊断测试步骤详细信息的示例。 测试引擎存储已失败的测试的诊断详细信息。 对于成功的测试，将存储执行子集的诊断详细信息。 将鼠标悬停在对任何绿/红点，若要查看测试，测试名称和位置。

![行视图](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

选择特定测试、位置或减少时间段，可查看围绕感兴趣的时间段的更多结果。 使用搜索资源管理器以查看所有执行结果，或者使用分析查询以针对此数据运行自定义报告。

## <a name="inspect-and-edit-tests"></a>检查和编辑测试

若要编辑、 暂时禁用或删除测试，请单击测试名称旁边的省略号。 可能需要 20 分钟后进行更改将传播到所有的测试代理的配置更改。

![查看测试详细信息。 编辑并禁用 web 测试](./media/monitor-web-app-availability/edit.png)

对服务执行维护时，可能需要禁用可用性测试或与这些测试关联的警报规则。

## <a name="if-you-see-failures"></a>如果看到失败

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

除了原始结果，还可以查看中的两个密钥可用性指标[指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. 可用性：已成功的测试占执行的所有测试的百分比。
2. 测试持续时间：执行的所有测试的平均测试持续时间。

## <a name="automation"></a>自动化

* [使用 PowerShell 脚本自动设置可用性测试](../../azure-monitor/app/powershell.md#add-an-availability-test)。
* 设置在引发警报时调用的 [webhook](../../azure-monitor/platform/alerts-webhooks.md) 。

## <a name="troubleshooting"></a>故障排除

专用[疑难解答文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>后续步骤

* [可用性警报](availability-alerts.md)
* [多步骤 web 测试](availability-multistep.md)


