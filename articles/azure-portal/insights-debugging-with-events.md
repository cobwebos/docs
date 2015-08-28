<properties 
	pageTitle="查看事件和审核日志" 
	description="了解如何查看在 Azure 订阅中发生的所有事件。" 
	authors="HaniKN-MSFT" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.date="04/28/2015" 
	wacn.date=""/>

# 查看事件和审核日志

从创建和删除到授予或撤消访问权限，Azure 资源上执行的所有操作都由 Azure 资源管理器进行全面审核。可以在 Azure 门户中浏览这些日志，也可以使用 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/dn931927.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式访问完整的事件集。

## 浏览影响 Azure 订阅的事件

1. 登录到 [Azure 门户](https://manage.windowsazure.cn/)。
2. 单击“浏览”并选择“审核日志”。  
    ![浏览中心](./media/insights-debugging-with-events/Insights_Browse.png)
3. 这将打开一个分页，显示过去 7 天内对您的任何订阅造成影响的所有事件。顶部是一个按级别显示数据的图表，图表下方是日志的完整列表：
    ![所有事件](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE]在 Azure 门户中，只能对给定的订阅查看 500 个最新事件。

4. 可以单击任何日志条目来查看其中所含的事件。例如，当您将一些内容部署到资源组时，可创建或修改许多不同的资源。对于每个条目，您可以查看：
    * 事件的“级别” - 例如，其可能只是一些要跟踪的内容（信息性），或者当有些内容出现问题您需要了解时（错误）。 
    * “状态” - 最终状态通常为“成功”或“失败”，但对于长期运行的操作，最终状态也可能是“已接受”。
    * 事件发生的*时间*。
    * 执行操作的*人员*（若有）。并非所有操作都由用户执行，有些操作由后端服务执行，因此不会具有“调用方”。
    * 事件的“相关 ID” - 这是此组操作的唯一标识符。

5. 从这里可以转到详细信息分页，以查看事件的具体细节。
   
    ![资源组](./media/insights-debugging-with-events/Insights_EventDetails.png)

    对于“失败”事件，此页通常包括“子状态”和“属性”部分，其中包含有关调试的有用详细信息。

## 筛选到特定日志

为了显示适用于特定实体的事件或特定类型的事件，可以通过单击“筛选器”命令来筛选“审核日志”分页。还可使用“筛选器”分页来更改“审核日志”分页的“时间跨度”。

单击此命令后，便会打开新的分页：

![筛选器](./media/insights-debugging-with-events/Insights_EventFilter.png)

筛选器有四种类型：

1. 按订阅筛选
2. 按“资源组”筛选
3. 按“资源类型”筛选
4. 按特定“资源”筛选 - 对于此类型，您必须粘入所感兴趣资源的完整*资源 ID*

此外，还可以按执行事件的人员或事件的级别来筛选事件。

选择完想要查看的内容后，单击分页底部的“更新”按钮。

## 监视影响特定资源的事件

1. 单击“浏览”以查找所感兴趣的资源。还可以查看整个“资源组”的所有日志。
2. 在资源的分页上，向下滚动直到找到“事件”磁贴。  
    ![事件磁贴](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. 单击该磁贴以查看专门针对所选资源筛选出的事件。可以使用“筛选器”命令来更改时间范围或应用更多特定的筛选器。

## 后续步骤

* 每当发生事件时，都[接收警报通知](/documentation/articles/insights-receive-alert-notifications)。
* [监视服务度量值](/documentation/articles/insights-how-to-customize-monitoring)，确保服务可用且及时响应。
* [跟踪服务运行状况](/documentation/articles/insights-service-health)，了解 Azure 何时遇到性能下降或服务中断问题。  

<!---HONumber=67-->