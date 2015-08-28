<properties 
	pageTitle="接收警报通知" 
	description="在满足警报规则条件时收到通知。" 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.date="04/25/2015" 
	wacn.date=""/>

# 接收警报通知

您可以根据 Azure 服务的监视度量值或事件接收警报。

对于有关度量值的警报规则，当指定度量值的值超过所分配的阈值时，警报规则变为活动状态并会发送通知。对于有关事件的警报规则，规则可以针对*每个*事件发送通知，或者，仅在发生一定数量的事件时才发送通知。

在创建警报规则时，您可以选择选项，以将电子邮件通知发送到服务管理员和协同管理员或您可指定的其他管理员。当规则变为活动状态时，以及当警报条件解除时，系统会发送一封通知电子邮件。

您可以使用 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/dn931945.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式来配置和获取有关警报规则的信息。

## 创建警报规则

1. 在[门户](https://manage.windowsazure.cn/)中，单击“浏览”，然后单击想要监视的资源。

2. 单击“操作”小视窗中的“警报规则”磁贴。

3. 单击“添加通知”命令。
    ![添加警报](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. 您可以为警报规则命名，并选择将在通知电子邮件中显示的说明。

5. 当您选择“度量值”时，您将为该度量值选择条件和阈值。这是 Azure 用来监视和绘制警报活动的时间范围。
    ![条件和阈值](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. 您还可以选择“事件”，并在某些事件发生时获得通知。
    ![事件](./media/insights-receive-alert-notifications/Insights_Events.png)

7. 最后，您可以选择将电子邮件通知发送给责任管理员。

单击“保存”后，每当所选择的度量值超出阈值，您在几分钟内即可得到通知。

## 管理警报规则

创建警报规则后，可以查看预览，了解该警报阈值与前一天度量值的比较情况。

![事件](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


当然，您可以编辑此警报规则，而且如果您想要暂时停止接收其相关通知，可以“禁用”或“启用”它。

## 后续步骤

* [监视服务度量值](/documentation/articles/insights-how-to-customize-monitoring)，确保服务可用且及时响应。
* [启用监视和诊断](/documentation/articles/insights-how-to-use-diagnostics)，以在服务上收集详细的高频度量值信息。
* 利用 Application Insights [监视任何网页的可用性和响应能力](/documentation/articles/app-insights-monitor-web-app-availability)，让您可以了解网页是否运行正常。
* 如果想要确切了解代码在云中的执行情况，则[监视应用程序性能](/documentation/articles/insights-perf-analytics)。
* [查看事件和审核日志](/documentation/articles/insights-debugging-with-events)，了解服务中发生的一切。
* [跟踪服务运行状况](/documentation/articles/insights-service-health)，了解 Azure 何时遇到性能下降或服务中断问题。
 

<!---HONumber=67-->