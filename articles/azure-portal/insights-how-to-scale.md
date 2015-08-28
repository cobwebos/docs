<properties 
	pageTitle="手动或自动缩放实例计数" 
	description="了解如何扩展您的 Azure 服务。" 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.date="04/28/2015" 
	wacn.date=""/>

# 手动或自动缩放实例计数

在 [Azure 门户](https://manage.windowsazure.cn/)中，您可以手动设置服务的实例计数，也可以设置参数以根据需要自动缩放。这通常称为*扩展*或*缩减*。

基于实例计数进行缩放之前，应考虑到缩放不仅受实例计数影响，还受“定价层”影响。不同的定价层可以具有不同的内核和内存数量，因此在实例数量相同的情况下，它们的性能也就越好（即*增加*或*减少*）。本文专门介绍*缩减*和*扩展*。

您可以在门户中缩放，也可以使用 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/dn931953.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以手动或自动方式调整缩放。

## 手动缩放

1. 在 [Azure 门户](https://manage.windowsazure.cn/)中，单击“浏览”，然后导航到想要缩放的资源，如“App Service 计划”。

2. “操作”中的“缩放”磁贴将显示缩放的状态：当您手动缩放时会显示“关闭”；当您按一个或多个性能指标缩放时会显示“打开”。![“缩放”磁贴](./media/insights-how-to-scale/Insights_UsageLens.png)

3. 单击磁贴会转到“缩放”分页。在缩放分页的顶部，可以看到服务的自动缩放操作历史记录。  
    ![“缩放”分页](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
    
>[AZURE.NOTE]只有通过自动缩放执行的操作才会显示在此图表中。如果您手动调整实例计数，更改将不反映在此图表中。

4. 您可以使用滑块手动调整“实例”数。
5. 单击“保存”命令即可缩放到该实例数。 

## 基于预设度量值进行缩放

如果您想要基于一个度量值自动调整实例数，请在“缩放依据”下拉列表中选择想要的度量值。例如，对于“App Service 计划”，您可以按“CPU 百分比”缩放。

1. 当您选择一个度量值时，会出现滑块和/或文本框，让您可以输入想要缩放的实例数范围：

    ![带有“CPU 百分比”的“缩放”分页](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
    
    不论您的负载如何，自动缩放将永远不会让您的服务低于或超出所设置的边界。

2. 此外，您还可以选择度量值的目标范围。例如，如果选择“CPU 百分比”，您可以在服务中的所有实例上为平均 CPU 设置一个目标。当平均 CPU 超出您定义的最大值时将会扩展，同样，当平均 CPU 低于最小值将会缩减。

3. 单击“保存”命令。自动缩放将每隔几分钟进行检查，以确保符合度量值的实例范围和目标。当您的服务接收更多的流量时，您不必执行任何操作即可获得更多实例。

## 基于其他度量值进行缩放

您可以不基于“缩放条件”下拉列表中显示的预设值，而是基于度量值进行缩放，甚至可以采用一套复杂的扩展和缩减规则。

### 添加或更改规则

1. 选择“缩放依据”下拉列表中的“计划和性能规则”：
![性能规则](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. 如果您以前打开过自动缩放功能，则您会看到一个视图显示以前的确切规则。

3. 若要基于其他度量值进行缩放，请单击“添加规则”行。您还可以单击一个现有行，将之前的度量值更改为缩放所要依据的度量值。
![添加规则](./media/insights-how-to-scale/Insights_AddRule.png)

4. 现在，您需要选择缩放所要依据的度量值。当选择度量值时，要考虑几个事项：
    * 度量值所属的*资源*。通常，这将与您缩放的资源相同。但是，如果您想要按存储队列的深度进行缩放，资源就是缩放所要依据的队列。
    * *度量值名称*本身。 
    * 度量值的*时间聚合*。这是数据随着*持续时间*进行组合的方式。
    
5. 选择度量值后，为度量值选择阈值，然后选择运算符。例如，可以假定“大于 80%”。

6. 然后，选择您想要采取的操作。有几种不同类型的操作：
    * 增加或减少数量 - 此操作会添加或删除您定义的实例数的“值”
    * 增加或减少百分比 - 此操作会按百分比更改实例计数。例如，可以将 25 填入“值”字段中，如果当前有 8 个实例，将添加 2 个。
    * 增加或减小的目标数量 - 此操作会将实例计数设置为您定义的“值”。

7. 最后，您可以选择等待时间 - 此规则自上次扩展操作再次扩展后应等待多长时间。
    
8. 配置规则后，点击“确定”。

9. 配置完所需的所有规则后，请务必点击“保存”命令。

### 使用多个步骤进行缩放

上面的示例是最基本的。但是，如果您想要更主动地增加（或减少），甚至可以为同一度量值添加多个缩放规则。例如，您可以为“CPU 百分比”定义两个缩放规则：

1. 如果 CPU 百分比超出 60%，则按 1 个实例扩展
2. 如果 CPU 百分比超出 85%，则按 3 个实例扩展

![多个缩放规则](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

启用这个附加规则后，如果在执行缩放操作之前负载超出 85%，您将会获得两个附加实例而不是一个。

## 基于计划进行缩放


默认情况下，当您创建一个缩放规则时，该规则将始终应用。您单击配置文件标题时可以看到该规则：

![配置文件](./media/insights-how-to-scale/Insights_Profile.png)

但是，您可能希望一天或一周比周末更主动地缩放。您甚至可以在非工作时段完全关闭您的服务。

1. 若要执行此操作，请在您的配置文件中选择“重复”而不是“始终”，然后选择想要配置文件应用的时间。

2. 例如，若要让配置文件在一周期间应用，请在“天”下拉列表中取消选中“星期六”和“星期日”。

3. 若要让配置文件在白天应用，请将“开始时间”设置为一天中您希望的开始时间。
    ![默认重复周期](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. 单击“确定”。

5. 接下来，您需要添加想要在其他时间应用的配置文件。单击“添加配置文件”行。
    ![停止工作](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. 为新的第二个配置文件命名，例如，可以称之为“停止工作”。

7. 然后再次选择“重复”，并选择要在此时间期间应用的实例计数范围。

8. 与使用默认配置文件一样，选择要此配置文件应用于的“天”，然后选择一天中的“开始时间”。

>[AZURE.NOTE]自动缩放将为您选择的“时区”使用夏令时规则。但是，在夏令时期间，UTC 偏移将显示基本时区偏移，而不是夏令时 UTC 偏移。

9. 单击“确定”。

10. 现在，您需要添加要在第二个配置文件期间应用的规则。单击“添加规则”，然后可以创建与执行默认配置文件期间相同的规则。
    ![为停止工作添加规则](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. 请务必为扩展和缩减创建规则，否则，在执行配置文件期间，实例计数将只增加（或减少）。

12. 最后，单击“保存”。

## 后续步骤

* [监视服务度量值](/documentation/articles/insights-how-to-customize-monitoring)，确保服务可用且及时响应。
* [启用监视和诊断](/documentation/articles/insights-how-to-use-diagnostics)，以在服务上收集详细的高频度量值信息。
* 每当发生操作事件或度量值超出阈值时，都[接收警报通知](/documentation/articles/insights-receive-alert-notifications)。
* 如果想要确切了解代码在云中的执行情况，则[监视应用程序性能](/documentation/articles/insights-perf-analytics)
* [查看事件和审核日志](/documentation/articles/insights-debugging-with-events)，了解服务中发生的一切。
* 利用 Application Insights [监视任何网页的可用性和响应能力](/documentation/articles/app-insights-monitor-web-app-availability)，让您可以了解网页是否运行正常。
 

<!---HONumber=67-->