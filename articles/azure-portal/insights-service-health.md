<properties 
	pageTitle="跟踪服务运行状况" 
	description="了解 Azure 何时遇到性能下降或服务中断问题。" 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.date="04/25/2015" 
	wacn.date=""/>

# 跟踪服务运行状况

每次遇到服务中断或性能下降情况时，Azure 都会公布。可以在 Azure 门户中浏览这些事件，也可使用 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/dn931927.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式访问完整的事件集。

## 按区域查看服务运行状况

1. 登录到 [Azure 门户](https://manage.windowsazure.cn/)。

2. 在“主页”中，您应该会看到一个名为“服务运行状况”的磁贴
    ![主页](./media/insights-service-health/Insights_Home.png)

3. 单击该磁贴将显示 Azure 中所有区域的列表。您可以单击任何区域以查看该区域的服务运行状况历史记录。
    ![主页](./media/insights-service-health/Insights_Regions.png)

4. 还可以通过单击表中的区域来查看单个事件的详细信息。

## 浏览完整的服务运行状况日志

2. 单击“浏览”并选择“审核日志”。
    ![浏览中心](./media/insights-service-health/Insights_Browse.png)

3. 这将打开一个分页，显示过去 7 天内对您的任何订阅造成影响的所有事件。服务运行状况条目将显示在此列表中，但可能很难查找这些条目，因为列表可能较大。

4. 单击“筛选器”命令。

5. 选择“事件类别”并选择“服务运行状况”：
    ![所有事件](./media/insights-service-health/Insights_Filter.png)

6. 单击“更新”。

7. 您现在将看到影响到您的订阅的所有服务运行状况事件：
    ![资源组](./media/insights-service-health/Insights_HealthEvent.png)

8. 从这里可以转到详细信息分页，以查看事件的具体细节。
   
## 后续步骤

* 每当发生事件时，都[接收警报通知](/documentation/articles/insights-receive-alert-notifications)。
* [监视服务度量值](/documentation/articles/insights-how-to-customize-monitoring)，确保服务可用且及时响应。
* 利用 Application Insights [监视任何网页的可用性和响应能力](/documentation/articles/app-insights-monitor-web-app-availability)，让您可以了解网页是否运行正常。
 

<!---HONumber=67-->