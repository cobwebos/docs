<properties linkid="web-sites-create-web-jobs" urlDisplayName="使用 Web 作业在 Microsoft Azure 网站中运行后台任务" pageTitle="使用 Web 作业在 Microsoft Azure 网站中运行后台任务  网站" metaKeywords="Microsoft Azure  Websites, Web Jobs, background tasks" description="了解如何在 Microsoft Azure 网站中运行后台  任务。" metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure  Websites" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

# 使用 Web 作业在 Azure 网站中运行后台任务

Azure 网站允许你在网站中以三种方式运行程序或脚本：按需、连续或按计划。使用 Microsoft Azure Web 作业无需支付额外的费用。

本文说明如何使用 Azure 管理门户来部署 Web 作业。有关如何使用 Visual Studio 或连续传递过程进行部署的信息，请参阅[如何将 Azure Web 作业部署到 Azure 网站](/zh-cn/documentation/articles/websites-dotnet-deploy-webjobs)。

Azure WebJobs SDK 简化了许多 Web 作业编程任务。有关详细信息，请参阅[什么是 WebJobs SDK](../websites-dotnet-webjobs-sdk)。

## 目录 ##
- [可接受的脚本文件类型](#acceptablefiles)
- [创建按需运行任务](#CreateOnDemand)
- [创建连续运行任务](#CreateContinuous)
- [创建计划任务](#CreateScheduled)
	- [计划作业和 Azure 计划程序](#Scheduler)
- [查看作业历史记录](#ViewJobHistory)
- [说明](#WHPNotes)
- [后续步骤](#NextSteps)

## <a name="acceptablefiles"></a>可接受的脚本或程序文件类型

接受以下文件类型：

* .cmd、.bat、.exe（使用 Windows cmd）
* .ps1（使用 Powershell）
* .sh（使用 Bash）
* .php（使用 php）
* .py（使用 Python）
* .js（使用 Node）

## <a name="CreateOnDemand"></a>创建按需运行任务

1. 在"Web 作业"页的命令栏中，单击"添加"。此时将显示"新作业"对话框。
	
	![On Demand Task][OnDemandWebJob]
	
2. 在"名称"下，提供任务的名称。名称必须以字母或数字开头，并且不能包含除"-"和"_"以外的任何特殊字符。
	
3. 在"内容(Zip 文件 - 最大 100 MB)"框中，浏览到包含你的脚本的 zip 文件。Zip 文件应包含可执行文件 (.exe .cmd .bat .sh .php .py .js)，以及运行程序或脚本所需的任何支持文件。
	
4. 在"如何运行"框中，选择"按需运行"。
	
5. 选中对话框右下角的复选标记，以将该脚本上载到你的网站。为任务指定的名称将显示在列表中：
	
	![Task List][WebJobsList]
	
6. 若要运行该脚本，请在列表中选择其名称，然后在门户页面底部的命令栏中单击"运行一次"。
	
	![Run Once][RunOnce]

## <a name="CreateContinuous"></a>创建连续运行任务

1. 若要创建连续执行的任务，请按照与创建运行一次的任务相同的步骤进行操作，但在"如何运行"框中，请选择"连续运行"。
	
	![New Continuous Task][NewContinuousJob]
	
2. 若要启动或停止连续运行的任务，请在列表中选择任务，然后在命令栏中单击"启动"或"停止"。

> [AZURE.NOTE] 如果你的网站在多个实例上运行，则连续运行的任务将在所有实例上运行。按需运行任务和计划任务在 Microsoft Azure 针对负载平衡所选择的单个实例上运行。

> [AZURE.NOTE]
> 对于连续运行任务，建议你在"配置"页上为网站启用"Always On"。Always On 功能（在基本和标准模式下可用）可防止网站被卸载，即使网站已空闲一段时间也是如此。如果你的网站始终处于加载状态，则连续运行的任务可能会更可靠地运行。 

## <a name="CreateScheduled"></a>创建计划任务

1. 若要创建计划任务，请执行前述相同步骤，但在"如何运行"框中，请选择"按计划运行"。
	
	![New Scheduled Job][NewScheduledJob]
	
2. 为作业选择"计划程序区域"，然后单击对话框右下角的箭头进入下一个屏幕。

3. 在"创建作业"对话框中，选择想要的"定期"类型：**"一次性作业"**或**"定期作业"**。
	
	![Schedule Recurrence][SchdRecurrence]
	
4. 还需选择"开始"时间：**"立即"或"在特定时间"**。
	
	![Schedule Start Time][SchdStart]
	
5. 如果你想要在特定时间启动，请在"开始时间"下选择开始时间值。
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. 如果你选择定期作业，请使用"重复间隔"选项指定重复执行的频率，并使用"结束时间"选项指定结束时间。
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. 如果选择"周"，则可以选中"按特定计划"框，并指定你想要在星期几运行作业。
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. 如果选择"月"并选中"按特定计划"框，则可以设置在每月的特定编号"日"运行作业。 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. 如果选择"工作日"，则可以选择想要在一个月中的哪个或哪些工作日运行作业。
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. 最后，还可以使用"次数"选项，选择想要在一个月中的哪一周（第一周、第二周、第三周等）的指定工作日运行作业。
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. 创建一个或多个作业后，其名称将与其状态、计划类型和其他信息一起显示在"Web 作业"选项卡中。将保留最近 30 个任务的历史信息。
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>计划作业和 Azure 计划程序

可以在 Azure 计划程序门户中进一步配置计划作业。

1.	在"Web 作业"页上，单击作业的"计划"链接以导航到 Azure 计划程序门户页。 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. 在计划程序页上，单击该作业。
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. 将打开"作业操作"页，从中可以进一步配置该作业。 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>查看作业历史记录

1. 若要查看作业的执行历史记录（包括使用 WebJobs SDK 创建的作业），请单击"日志"列下方的该作业的相应链接。（如果需要，你可以使用剪贴板图标将日志文件页的 URL 复制到剪贴板中）
	
	![Logs Link][WebJobLogs]
		
2. 单击链接将打开该任务的 Web 作业详细信息页。此页显示了命令运行的名称、它运行的最后时间及其成功或失败状态。在"最近的作业运行"下，单击某个时间以查看更多详细信息。
	
	![WebJobDetails][WebJobDetails]
	
3. 此时将显示"Web 作业运行详细信息"。单击"切换输出"以查看日志内容的文本。输出日志使用文本格式。 
	
	![Web job run details][WebJobRunDetails]
	
4. 若要在单独的浏览器窗口中查看输出文本，请单击"下载"链接。若要下载文本本身，请右键单击该链接，并使用你的浏览器选项来保存该文件的内容。
	
	![Download log output][DownloadLogOutput]
	
5. 页面顶部的"Web 作业"链接提供了用于访问历史记录仪表板上的 Web 作业列表的简便方法。
	
	![Link to web jobs list][WebJobsLinkToDashboardList]
	
	![List of jobs in history dashboard][WebJobsListInJobsDashboard]
	
	单击其中一条链接可将你带到所选作业的 Web 作业详细信息页。


## <a name="WHPNotes"></a>说明
	
- 从 2014 年 3 月开始，如果没有对 scm（部署）站点发出任何请求，并且未在 Azure 中打开网站的门户，则免费模式下的网站可能会在 20 分钟之后超时。对实际网站发出的请求不会对此进行重置。
- 需要对连续作业的代码进行编写，才能使其在无穷循环中运行。
- 仅当站点启动后连续作业才能连续运行。
- 基本和标准模式提供了 Always On 功能，启用该功能可防止站点进入空闲状态。

## <a name="NextSteps"></a>后续步骤
 
有关详细信息，请参阅[有关 Azure Web 作业的推荐资源][WebJobsRecommendedResources]。

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:/zh-cn/documentation/articles/store-new-relic-cloud-services-dotnet-application-performance-management/

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
