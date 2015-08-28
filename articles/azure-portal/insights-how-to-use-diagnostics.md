<properties 
	pageTitle="启用监视和诊断" 
	description="了解如何在 Azure 中为您的资源设置诊断。" 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.date="04/28/2015" 
	wacn.date=""/>

# 启用监视和诊断

在 [Azure 门户](http://manage.windowsazure.cn)中，您可以针对资源配置丰富、频繁出现的监视和诊断数据。也可以使用 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/dn931932.aspx) 或 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) 以编程方式配置诊断。

在 Azure 中，诊断、监视和度量值数据保存在您选择的存储帐户中。这样您可以随意使用任何工具读取数据，从存储浏览器，到 Power BI，再到第三方工具，均可使用。

## 当您创建资源时

当您首次在 [Azure 门户](http://manage.windowsazure.cn)中创建服务时，大多数服务允许您启用诊断。

1. 转到“新建”，然后选择所感兴趣的资源。 

2. 选择“可选配置”。
    ![“诊断”分页](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. 选择“诊断”，然后单击“打开”。您将需要选择您希望保存诊断的存储帐户。向存储帐户发送诊断时，将按正常数据速率向您收取存储和事务费用。

4. 单击“确定”并创建资源。

## 更改现有资源的设置

如果您已创建一个资源，并且想要更改诊断设置（例如，更改数据收集的级别），可以在 Azure 门户中执行改操作。

1. 转到资源，然后单击“设置”命令。

2. 选择“诊断”。

3. “诊断”分页提供有关该资源的所有可能的诊断和监视收集数据。对于一些资源，您可以为数据选择“保留”策略，以从存储帐户清理数据。
    ![存储诊断](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. 选择设置后，单击“保存”命令。如果是首次启用，可能需要一段时间才能显示监视数据。

### 适用于虚拟机的数据收集类别
对于虚拟机，所有度量值和日志的记录间隔是一分钟，因此您可以始终拥有关于虚拟机的最新信息。

- **基本度量值**：虚拟机相关（比如处理器和内存）的运行状况度量值 
- **网络和 Web 度量值**：有关网络连接和 Web 服务的度量值
- **.NET 度量值**：有关虚拟机上运行的 .NET 和 ASP.NET 应用程序的度量值
- **SQL 度量值**：如果您运行的是 Microsoft SQL 服务，则为其性能度量值
- **Windows 事件的应用程序日志**：发送到应用程序通道的 Windows 事件
- **Windows 事件系统日志**：发送到系统通道的 Windows 事件。这还包括来自 [Microsoft 反恶意软件](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)的所有事件。 
- **Windows 事件安全日志**：发送到安全通道的 Windows 事件
- **诊断基础结构日志**：有关诊断收集基础结构日志记录
- **IIS 日志**：有关 IIS 服务器的日志

请注意，目前不支持 Linux 的某些分发，并且必须在虚拟机上安装来宾代理。

## 后续步骤

* 每当发生操作事件或度量值超出阈值时，都[接收警报通知](documentation/articles/insights-receive-alert-notifications)。
* [监视服务度量值](documentation/articles/insights-how-to-customize-monitoring)，确保服务可用且及时响应。
* [自动缩放实例计数](documentation/articles/insights-how-to-scale)，确保根据需要缩放服务。
* 如果想要确切了解代码在云中的执行情况，则[监视应用程序性能](documentation/articles/insights-perf-analytics)。
* [查看事件和审核日志](documentation/articles/insights-debugging-with-events)，了解服务中发生的一切。
* [跟踪服务运行状况](documentation/articles/insights-service-health)，了解 Azure 何时遇到性能下降或服务中断问题。 
 

<!---HONumber=67-->