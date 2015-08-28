<properties 
	pageTitle="监视服务度量值" 
	description="了解如何在 Azure 中自定义监视图表。" 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal"
documentationCenter=""/>

<tags 
	ms.service="azure-portal" 
	ms.date="04/28/2015" 
	wacn.date=""/>

# 监视服务度量值

所有 Azure 服务跟踪的关键度量值可让您监视服务的运行状况、性能、可用性和使用情况。可以在 Azure 门户中查看这些度量值，也可以使用 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/dn931930.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式访问完整的度量值集。

对于某些服务，您可能需要打开诊断才能查看度量值。对于其他服务，比如虚拟机，您将获得一组基本度量值，但需要启用完整的高频度量值集。请参阅[启用监视和诊断](/documentation/articles/insights-how-to-use-diagnostics)，了解详细信息。

## 使用监视图表 

您可以依据所选的任何时间段，将任何度量值绘制成图表。

1. 在 [Azure 门户](https://manage.windowsazure.cn/)中，单击“浏览”，然后单击想要监视的资源。

2. “监视”部分包含每个 Azure 资源的最重要度量值。例如，Web 应用具有“请求和错误”，而虚拟机则会具有“CPU 百分比”和“磁盘读写”：
    ![“监视”小视窗](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. 单击任意图表均将显示“度量值”分页。在分页上，除图形外，还有一个表格显示度量值集合（比如平均值、最小值和最大值、所选时间范围）。下面是资源的警报规则。
    ![“度量值”分页](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. 若要自定义显示的行数，请单击图表上的“编辑”按钮，或单击“度量值”分页上的“编辑图表”命令。

5. 在“编辑查询”分页上，可以执行三项操作：
    - 更改时间范围
    - 在条形图与折线图之间切换显示
    - 选择不同的度量值
    ![编辑查询](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. 更改时间范围非常简单，只需选择一个不同的范围（比如“前一个小时”），然后单击分页底部的“保存”即可。还可以选择“自定义”，它允许您选择过去 2 周内的任意时间段。例如，可以查看整个两周时间，或仅查看昨天中的 1 小时。在文本框中输入不同的小时。
    ![自定义时间范围](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. 在时间范围下，可以选择要在图表上显示的任意数量的度量值。

8. 当您单击“保存”时，将为该特定资源保存您所做的更改。例如，如果您有两个虚拟机，并在一个虚拟机上更改图表，这不会影响另一个虚拟机。

## 创建并排比较图表

利用门户中的强大自定义功能，您可以根据所需数量添加图表。

1. 在分页顶部的“...” 菜单中，单击“添加磁贴”：  
    ![添加菜单](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. 然后，您可以从屏幕右侧的“库”中选择图表：
    ![库](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. 如果看不到想要的度量值，您始终可以添加一个预设度量值，并“编辑”图表以显示所需的度量值。 

## 监视使用率配额

大多数度量值都显示随时间变化的趋势，但某些数据比如使用率配额，则是带有阈值的时间点信息。

对于有配额的资源，您还可以在分页中查看使用率配额：

![使用情况](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

像使用度量值一样，您也可以使用 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/dn931963.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式访问完整的使用率配额集。

## 后续步骤

* 每当度量值超过阈值时，都[接收警报通知](/documentation/articles/insights-receive-alert-notifications)。
* [启用监视和诊断](/documentation/articles/insights-how-to-use-diagnostics)，以在服务上收集详细的高频度量值信息。
* [自动缩放实例计数](/documentation/articles/insights-how-to-scale)，确保服务可用且及时响应。
* 如果想要确切了解代码在云中的执行情况，则[监视应用程序性能](/documentation/articles/insights-perf-analytics)。
* 使用 [Application Insights for JavaScript 应用和网页](/documentation/articles/app-insights-web-track-usage)，针对访问网页的浏览器获取相关的客户端分析。
* 利用 Application Insights [监视任何网页的可用性和响应能力](/documentation/articles/app-insights-monitor-web-app-availability)，让您可以了解网页是否运行正常。
 

<!---HONumber=67-->