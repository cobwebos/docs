---
title: 为 Azure 云服务和虚拟机设置诊断 | Microsoft Docs
description: 了解如何在 Visual Studio 中设置调试 Azure 云服务和虚拟机 (VM) 所需的诊断。
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 34c667b0a594682e4d099e7bff64bfdb336b850b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292534"
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>为 Azure 云服务和虚拟机设置诊断
需要对 Azure 云服务或虚拟机进行故障排除时，可使用 Visual Studio 更轻松地设置 Azure 诊断。 诊断可以在运行云服务的虚拟机和虚拟机实例上捕获系统数据和日志记录数据。 诊断数据传输到所选的存储帐户。 有关 Azure 中诊断日志记录的详细信息，请参阅[为 Azure 应用服务中的 Web 应用启用诊断日志记录](app-service/web-sites-enable-diagnostic-log.md)。

本文介绍如何在部署前和部署后使用 Visual Studio 来启用和设置 Azure 诊断。 了解如何在 Azure 虚拟机上设置诊断、如何选择要收集的诊断信息的类型，以及如何在收集信息后查看这些信息。

可以使用以下选项之一来设置 Azure 诊断：

* 在 Visual Studio 的“诊断配置”对话框中更改诊断设置。 设置保存在名为 diagnostics.wadcfgx 的文件中（在 Azure SDK 2.4 及更低版本中，该文件名为 diagnostics.wadcfg）。 也可直接修改配置文件。 如果手动更新文件，则配置更改在下次将云服务部署到 Azure 或在模拟器中运行该服务时生效。
* 使用 Visual Studio 中的云资源管理器或服务器资源管理器更改正在运行的云服务或虚拟机的诊断设置。

## <a name="azure-sdk-26-diagnostics-changes"></a>Azure SDK 2.6 诊断更改
以下更改适用于 Visual Studio 中的 Azure SDK 2.6 及更高版本的项目：

* 本地模拟器现在支持诊断。 这意味着，在 Visual Studio 中进行开发和测试时，可以收集诊断数据并确保应用程序创建相应的跟踪。 使用 Azure 存储模拟器在 Visual Studio 中运行云服务项目时，连接字符串 `UseDevelopmentStorage=true` 启用诊断数据收集。 所有诊断数据都在“开发存储”存储帐户中收集。
* 诊断存储帐户连接字符串 `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` 存储在服务配置 (.cscfg) 文件中。 在 Azure SDK 2.5 中，诊断存储帐户是在 diagnostics.wadcfgx 文件中指定的。

将 Azure SDK 2.6 及更高版本与 Azure SDK 2.4 及更低版本进行对比就可以发现，连接字符串在某些关键方面的使用方式并不相同：

* 在 Azure SDK 2.4 及更低版本中，连接字符串由诊断插件用作运行时以获取用于传输诊断日志的存储帐户信息。
* 在 Azure SDK 2.6 及更高版本中，Visual Studio 在发布过程中通过诊断连接字符串使用相应的存储帐户信息来设置 Azure 诊断扩展。 可以使用连接字符串，为 Visual Studio 在发布时使用的不同服务配置定义不同的存储帐户。 但是，因为诊断插件在 Azure SDK 2.5 之后不可用，.cscfg 文件本身不能设置诊断扩展。 必须使用 Visual Studio 或 PowerShell 之类的工具分别设置扩展。
* Visual Studio 的包输出包括每个角色的诊断扩展的公共配置 XML，可简化使用 PowerShell 设置诊断扩展的过程。 Visual Studio 使用诊断连接字符串来填充公共配置中的存储帐户信息。 公共配置文件在 Extensions 文件夹中创建。 公共配置文件使用的命名模式为 PaaSDiagnostics.&lt;角色名称\>.PubConfig.xml。 任何基于 PowerShell 的部署都可以使用此模式将每个配置映射到角色。
* [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)使用 .cscfg 文件中的连接字符串来访问诊断数据。 数据显示在“监视”选项卡上。需要连接字符串才能设置服务，以便在门户中显示详细监视数据。

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>将项目迁移到 Azure SDK 2.6 及更高版本
从 Azure SDK 2.5 迁移到 Azure SDK 2.6 或更高版本时，如果在 .wadcfgx 文件中指定了诊断存储帐户，此存储帐户会保留在该文件中。 可以针对不同的存储配置使用不同的存储帐户，若要充分利用这种灵活性，请手动将连接字符串添加到项目。 如果将项目从 Azure SDK 2.4 或更低版本迁移到 Azure SDK 2.6，则会保留诊断连接字符串。 但是，请注意 Azure SDK 2.6 中连接字符串处理方式的变化，如上一部分所述。

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Visual Studio 如何确定诊断存储帐户
* 如果在 .cscfg 文件中指定了诊断连接字符串，Visual Studio 会在发布时以及在打包过程中生成公共配置 XML 文件时使用它来设置诊断扩展。
* 如果未在 .cscfg 文件中指定诊断连接字符串，Visual Studio 会在发布时以及在打包过程中生成公共配置 XML 文件时回退到使用 .wadcfgx 文件中指定的存储帐户来设置诊断扩展。
* .cscfg 文件中的诊断连接字符串将优先于 .wadcfgx 文件中的存储帐户。 如果在 .cscfg 文件中指定了诊断连接字符串，Visual Studio 会使用该连接字符串，而忽略 .wadcfgx 中的存储帐户。

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>“更新开发存储连接字符串...”复选框的作用
有了“在发布到 Microsoft Azure 时使用 Microsoft Azure 存储帐户凭据更新诊断和缓存的开发存储连接字符串”复选框，就可以方便地使用发布过程中指定的 Azure 存储帐户更新任何开发存储帐户连接字符串。

例如，如果选择此复选框且诊断连接字符串指定了 `UseDevelopmentStorage=true`，则在将项目发布到 Azure 时，Visual Studio 会通过发布向导中的指定存储帐户自动更新诊断连接字符串。 但是，如果已将实际的存储帐户指定为诊断连接字符串，则会改用该帐户。

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Azure SDK 2.4 及更低版本与 Azure SDK 2.5 及更高版本之间的诊断功能差异
如果要将项目从 Azure SDK 2.4 及更低版本升级到 Azure SDK 2.5 或更高版本，则应考虑到以下诊断功能差异：

* **配置 API 已弃用**。 诊断的编程配置在 Azure SDK 2.4 及更低版本中可用，但在 Azure SDK 2.5 及更高版本中已弃用。 如果目前在代码中定义了诊断配置，则必须在已迁移的项目中从头开始重新配置这些设置，这样才能让诊断正常工作。 Azure SDK 2.4 的诊断配置文件是 diagnostics.wadcfg。 Azure SDK 2.5 及更高版本的诊断配置文件是 diagnostics.wadcfgx。
* **云服务应用程序的诊断只能在角色级别配置**。 在 Azure SDK 2.5 及更高版本中，不能在实例级别为云服务应用程序设置诊断。
* **每次部署应用时，都会更新诊断配置**。 如果从 Visual Studio 服务器资源管理器更改诊断配置并重新部署应用，这可能会导致奇偶校验问题。
* **在 Azure SDK 2.5 及更高版本中，故障转储在诊断配置文件而非代码中配置**。 如果在代码中配置故障转储，则必须手动将配置从代码转移到配置文件。 在迁移到 Azure SDK 2.6 时，不转移故障转储。

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>部署前在云服务项目中启用诊断
在 Visual Studio 中，可以为运行在 Azure 中的角色收集诊断数据，前提是部署前在模拟器中运行了服务。 在 Visual Studio 中对诊断设置所做的任何更改都将保存在配置文件 diagnostics.cscfg 中。 这些设置指定一个存储帐户，方便你在部署云服务时将诊断数据保存到该帐户。

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>部署前在 Visual Studio 中启用诊断

1. 在角色的快捷菜单中，选择“属性”。 在角色的“属性”对话框中，选择“配置”选项卡。
2. 在“诊断”部分中，确保“启用诊断”复选框已选中。
   
    ![访问“启用诊断”选项](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. 若要为诊断数据指定存储帐户，请选择省略号 (…) 按钮。
   
    ![指定要使用的存储帐户](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. 在“创建存储连接字符串”对话框中，指定在需要进行连接时，是使用 Azure 存储模拟器、Azure 订阅还是手动输入的凭据。
   
    ![存储帐户对话框](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * 如果选择“Microsoft Azure 存储模拟器”，则连接字符串设置为 `UseDevelopmentStorage=true`。
   * 如果选择“你的订阅”，则可选择要使用的 Azure 订阅，然后输入帐户名称。 若要管理 Azure 订阅，请选择“管理帐户”。
   * 如果选择“手动输入的凭据”选项，则请输入要使用的 Azure 帐户的名称和密钥。
5. 若要查看“诊断配置”对话框，请选择“配置”。 每个选项卡（“常规”和“日志目录”除外）都表示可以收集的诊断数据源。 默认的“常规”选项卡提供以下诊断数据收集选项：“仅限错误”、“所有信息”和“自定义计划”。 默认的“仅限错误”选项占用最少量的存储，因为该选项不传输警告或跟踪消息。 “所有信息”选项传输的信息最多，占用最多的存储，因此是成本最高的选项。
   
    ![启用 Azure 诊断和配置](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. 对于此示例，请选择“自定义计划”选项，以便自定义所收集的数据。
7. 在“磁盘配额(MB)”框中，可以设置需要在存储帐户中为诊断数据分配的空间量。 可以更改或接受默认值。
8. 在要收集的诊断数据的每个选项卡上，选中“启用\<日志类型\>的传输”复选框。 例如，如果要收集应用程序日志，请在“应用程序日志”选项卡上选中“启用应用程序日志的传输”复选框。 另外，请指定每种诊断数据类型所需的其他任何信息。 有关每个选项卡的配置信息，请参阅本文后面的“设置诊断数据源”部分。 
9. 允许收集所有需要的诊断数据后，请选择“确定”。
10. 照常在 Visual Studio 中运行 Azure 云服务项目。 使用应用程序时，允许的日志信息会保存到指定的 Azure 存储帐户中。

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>在 Azure 虚拟机上启用诊断
在 Visual Studio 中，可以为 Azure 虚拟机收集诊断数据。

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>在 Azure 虚拟机上启用诊断

1. 在“服务器资源管理器”中，选择“Azure”节点，然后连接到 Azure 订阅（如果尚未连接）。
2. 展开“虚拟机”节点。 可以创建新虚拟机，也可以选择现有节点。
3. 在所需虚拟机的快捷菜单上，选择“配置”。 此时会显示虚拟机配置对话框。
   
    ![配置 Azure 虚拟机](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. 如果尚未安装，请添加“Microsoft 监视代理诊断”扩展。 使用此扩展可以收集 Azure 虚拟机的诊断数据。 在“已安装的扩展”下的“选择可用扩展”下拉列表框中，选择“Microsoft Monitoring Agent 诊断”。
   
    ![安装 Azure 虚拟机扩展](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > 其他诊断扩展可用于虚拟机。 有关详细信息，请参阅[适用于 Windows 的虚拟机扩展和功能](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features)。
   > 
   > 
5. 若要添加扩展并查看其“诊断配置”对话框，请选择“添加”。
6. 若要指定存储帐户，请选择“配置”，然后选择“确定”。
   
    每个选项卡（“常规”和“日志目录”除外）都表示可以收集的诊断数据源。
   
    ![启用 Azure 诊断和配置](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    默认选项卡“常规”提供了以下诊断数据收集选项：“仅限错误”、“所有信息”和“自定义计划”。 默认选项“仅限错误”占用最少量的存储空间，因为该选项不传输警告或跟踪消息。 “所有信息”选项传输的信息最多，因此就存储来说是成本最高的选项。
7. 对于此示例，可以选择“自定义计划”选项，以便自定义所收集的数据。
8. “磁盘配额(MB)”框指定要在存储帐户中为诊断数据分配的空间量。 可以根据需要更改默认值。
9. 在要收集的诊断数据的每个选项卡上，选中其“启用\<日志类型\>的传输”复选框。
   
    例如，如果要收集应用程序日志，请选中“应用程序日志”选项卡上的“启用应用程序日志的传输”复选框。另外，请指定每种诊断数据类型所需的其他任何信息。 有关每个选项卡的配置信息，请参阅本文后面的“设置诊断数据源”部分。
10. 允许收集所有需要的诊断数据后，请选择“确定”。
11. 保存更新的项目。
    
    “Microsoft Azure 活动日志”窗口中的消息指示虚拟机已更新。

## <a name="set-up-diagnostics-data-sources"></a>设置诊断数据源
启用了诊断数据收集后，可以准确地选择要收集的数据源以及收集哪些信息。 后续部分介绍“诊断配置”对话框中的选项卡，以及每个配置选项的含义。

### <a name="application-logs"></a>应用程序日志
应用程序日志包含由 Web 应用程序生成的诊断信息。 如果要捕获应用程序日志，请选中“启用应用程序日志的传输”复选框。 若要增加或减少应用程序日志传输到存储帐户的时间间隔，请更改“传输周期(分钟)”值。 还可以更改日志中捕获的信息量，方法是设置“日志级别”值。 例如，选择“详细”以获取更多信息，或选择“关键”以仅捕获关键错误。 如果使用特定诊断提供程序来传输应用程序日志，则可通过将提供程序的 GUID 添加到“提供程序 GUID”框中来捕获这些日志。

  ![应用程序日志](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

有关应用程序日志的详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](app-service/web-sites-enable-diagnostic-log.md)。

### <a name="windows-event-logs"></a>Windows 事件日志
若要捕获 Windows 事件日志，请选中“启用 Windows 事件日志的传输”复选框。 若要增加或减少事件日志传输到存储帐户的时间间隔，请更改“传输周期(分钟)”值。 选中与要跟踪的事件类型对应的复选框。

![事件日志](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

如果使用的是 Azure SDK 2.6 或更高版本并想要指定自定义数据源，请在“\<数据源名称\>”文本框中输入它，然后选择“添加”。 该数据源将添加到 diagnostics.cfcfg 文件中。

如果使用的是 Azure SDK 2.5 并想要指定自定义数据源，可以将其添加到 diagnostics.wadcfgx 文件的 `WindowsEventLog` 节，如下例所示：

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>性能计数器
性能计数器信息可以帮助找到系统瓶颈，并优化系统和应用程序性能。 有关详细信息，请参阅[在 Azure 应用程序中创建和使用性能计数器](https://msdn.microsoft.com/library/azure/hh411542.aspx)。 若要捕获性能计数器，请选中“启用性能计数器的传输”复选框。 若要增加或减少事件日志传输到存储帐户的时间间隔，请更改“传输周期(分钟)”值。 选中与要跟踪的性能计数器对应的复选框。

![性能计数器](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

若要跟踪未列出的性能计数器，请使用建议的语法输入该性能计数器， 然后选择“添加”。 虚拟机上的操作系统决定了可以跟踪哪些性能计数器。有关语法的详细信息，请参阅 [Specify a counter path](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx)（指定计数器路径）。

### <a name="infrastructure-logs"></a>基础结构日志
基础结构日志包含的信息涉及 Azure 诊断基础结构、RemoteAccess 模块和 RemoteForwarder 模块。 若要收集有关基础结构日志的信息，请选中“启用基础结构日志的传输”复选框。 若要增加或减少基础结构日志传输到存储帐户的时间间隔，请更改“传输周期(分钟)”值。

![诊断基础结构日志](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

有关详细信息，请参阅[使用 Azure 诊断收集日志记录数据](https://msdn.microsoft.com/library/azure/gg433048.aspx)。

### <a name="log-directories"></a>日志目录
日志目录包含从 Internet Information Services (IIS) 请求、失败的请求或所选文件夹的日志目录收集的数据。 若要捕获日志目录，请选中“启用日志目录的传输”复选框。 若要增加或减少日志传输到存储帐户的时间间隔，请更改“传输周期(分钟)”值。

选中要收集的日志（如“IIS 日志”和“失败的请求日志”）对应的复选框。 虽然系统提供了默认的存储容器名称，但是可以更改这些名称。

可以从任何文件夹捕获日志。 在“从绝对目录记录”部分指定路径，然后选择“添加目录”。 日志捕获到指定的容器中。

![日志目录](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW 日志
如果使用 [Windows 事件跟踪](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) 并要捕获 ETW 日志，请选中“启用 ETW 日志的传输”复选框。 若要增加或减少日志传输到存储帐户的时间间隔，请更改“传输周期(分钟)”值。

将从指定的事件源和事件清单捕获事件。 若要指定事件源，请在“事件源”部分输入名称，然后选择“添加事件源”。 同样，可以在“事件清单”部分指定事件清单，然后选择“添加事件清单”。

![ETW 日志](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

ASP.NET 通过 [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) 命名空间中的类支持 ETW 框架。 Microsoft.WindowsAzure.Diagnostics 命名空间继承自标准 [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) 类并对其进行了扩展，在 Azure 环境中，可以通过该命名空间将 [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) 用作日志记录框架。 有关详细信息，请参阅[在 Microsoft Azure 中控制日志记录和跟踪](https://msdn.microsoft.com/magazine/ff714589.aspx)以及[在 Azure 云服务和虚拟机中启用诊断](cloud-services/cloud-services-dotnet-diagnostics.md)。

### <a name="crash-dumps"></a>故障转储
若要捕获有关角色实例何时发生故障的信息，请选中“启用故障转储的传输”复选框。 （由于 ASP.NET 能够处理大多数异常，因此故障转储通常仅对辅助角色有用。）若要增加或减少专用于故障转储的存储空间的百分比，请更改“目录配额(%)”值。 可以更改将故障转储存储到其中的存储容器，然后选择要捕获“完整”转储还是“微型”转储。

下一屏幕截图列出了当前跟踪的进程。 选中与要捕获的进程对应的复选框。 若要将另一进程添加到列表，请输入进程名称，然后选择“添加进程”。

![故障转储](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

有关详细信息，请参阅[在 Microsoft Azure 中控制日志记录和跟踪](https://msdn.microsoft.com/magazine/ff714589.aspx)，以及 [Microsoft Azure Diagnostics Part 4: Custom logging components and Azure Diagnostics 1.3 changes](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/)（Microsoft Azure 诊断第 4 部分：自定义日志记录组件和 Azure 诊断 1.3 更改）。

## <a name="view-the-diagnostics-data"></a>查看诊断数据
收集云服务或虚拟机的诊断数据后，可以查看这些数据。

### <a name="to-view-cloud-service-diagnostics-data"></a>查看云服务的诊断数据
1. 照常部署云服务，然后运行该服务。
2. 诊断数据可以在 Visual Studio 生成的报告中查看，或是在存储帐户的表中查看。 若要在报告中查看数据，请打开 Cloud Explorer 或服务器资源管理器，打开所需角色节点的快捷菜单，然后选择“查看诊断数据”。
   
    ![查看诊断数据](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    随即显示包括可用数据的报告。
   
    ![Visual Studio 中的 Microsoft Azure 诊断报告](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    如果最新数据没有显示，可能需要等待传输周期结束。
   
    若要立即更新数据，请选择“刷新”链接。 若要让数据自动更新，请在“自动刷新”下拉列表框中选择一个时间间隔。 若要导出错误数据，请选择“导出到 CSV”按钮以创建逗号分隔值文件。可以在 Excel 工作表中打开该文件。
   
    在 Cloud Explorer 或服务器资源管理器中，打开与部署相关联的存储帐户。
3. 在表查看器中打开诊断表，并查看所收集的数据。 对于 IIS 日志和自定义日志，可以打开 Blob 容器。 下表列出的表或 Blob 容器包含不同日志文件的数据。 除了该日志文件的数据，表条目还包含 EventTickCount、DeploymentId、Role 和 RoleInstance，有助于确定数据由哪个虚拟机和角色生成，以及生成的时间。 
   
   | 诊断数据 | 说明 | Location |
   | --- | --- | --- |
   | 应用程序日志 |代码通过调用 System.Diagnostics.Trace 类的方法而生成的日志。 |WADLogsTable |
   | 事件日志 |虚拟机上 Windows 事件日志中的数据。 Windows 在这些日志中存储信息，但应用程序和服务也使用这些日志来报告错误或记录信息。 |WADWindowsEventLogsTable |
   | 性能计数器 |可以对虚拟机上可用的任何性能计数器来收集数据。 操作系统提供性能计数器，其中包含多种统计数据，例如内存使用率和处理器时间。 |WADPerformanceCountersTable |
   | 基础结构日志 |从诊断基础结构自身生成的日志。 |WADDiagnosticInfrastructureLogsTable |
   | IIS 日志 |记录 Web 请求的日志。 如果云服务获取了大量的流量，这些日志可能很长。 最好是只在需要时才收集和存储此类数据。 |在 Blob 容器中，相应部署、角色和实例路径的 wad-iis-failedreqlogs 下，可以找到失败请求的日志。 在 wad-iis-logfiles 下可以找到完整日志。 各文件的条目记录在 WADDirectories 表中。 |
   | 故障转储 |提供云服务进程（通常为辅助角色）的二进制映像。 |wad-crush-dumps Blob 容器 |
   | 自定义日志文件 |预定义的数据日志。 |可以通过代码指定自定义日志文件在存储帐户中的位置。 例如，可以指定自定义 Blob 容器。 |
4. 如有任何类型的数据出现截断，可以尝试增大该数据类型的缓冲区，或是缩短从虚拟机到存储帐户的数据传输间隔。
5. （可选）间或清除存储帐户中的数据，降低整体存储开销。
6. 进行完整部署时，diagnostics.cscfg 文件（Azure SDK 2.5 中为 .wadcfgx）会在 Azure 中更新，云服务将拾取对诊断配置的所有更改。 如果改为更新现有部署，则不会在 Azure 中更新 .cscfg 文件。 但仍可按照下一部分中的步骤更改诊断设置。 有关执行完整部署和更新现有部署的详细信息，请参阅 [Publish Azure Application Wizard](vs-azure-tools-publish-azure-application-wizard.md)（发布 Azure 应用程序向导）。

### <a name="to-view-virtual-machine-diagnostics-data"></a>查看虚拟机的诊断数据
1. 在虚拟机的快捷菜单上，选择“查看诊断数据”。
   
    ![查看 Azure 虚拟机中的诊断数据](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    此时会显示“诊断摘要”对话框。
   
    ![Azure 虚拟机诊断摘要](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    如果最新数据没有显示，可能需要等待传输周期结束。
   
    若要立即更新数据，请选择“刷新”链接。 若要让数据自动更新，请在“自动刷新”下拉列表框中选择一个时间间隔。 若要导出错误数据，请选择“导出到 CSV”按钮以创建逗号分隔值文件。可以在 Excel 工作表中打开该文件。

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>部署后设置云服务诊断
如果要调查的问题涉及已在运行的云服务，则可能要收集在最初部署此角色之前未指定的数据。 在这种情况下，可以通过更改服务器资源管理器中的设置来启动该数据的收集。 可以为角色的单个实例或所有实例设置诊断，具体取决于是从实例快捷菜单还是从角色快捷菜单打开“诊断配置”对话框。 如果配置角色节点，所做的任何更改都会应用于所有实例。 如果配置实例节点，所做的任何更改都只应用于该实例。

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>为正在运行的云服务设置诊断
1. 在服务器资源管理器中，展开“云服务”节点，然后展开节点的列表，找到要调查的角色和/或实例。
   
    ![配置诊断](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. 在实例节点或角色节点的快捷菜单上，选择“更新诊断设置”，然后选择要收集的诊断设置。
   
    有关配置设置的信息，请参阅本文中的“设置诊断数据源”部分。 有关如何查看诊断数据的信息，请参阅本文中的“查看诊断数据”部分。
   
    如果在服务器资源管理器中更改数据收集，则在完全重新部署云服务之前，这些更改会一直生效。 如果使用默认的发布设置，则不会覆盖这些更改。 默认的发布设置是更新现有部署，而非进行完全的重新部署。 若要确保设置在部署时清除，请转到发布向导中的“高级设置”选项卡，然后清除“部署更新”复选框。 在清除该复选框的情况下重新部署时，这些设置将还原为 .wadcfgx（或 .wadcfg）文件中的设置（与通过角色的“属性”编辑器进行设置一样）。 如果更新部署，Azure 会保留此前的设置。

## <a name="troubleshoot-azure-cloud-service-issues"></a>排查 Azure 云服务问题
如果在处理云服务项目时遇到问题，例如陷入“繁忙”状态的某个角色反复回收或引发内部服务器错误，则可以使用一些工具和方法来诊断并解决问题。 有关常见问题和解决方案的具体示例，以及用于诊断并解决此类错误的概念和工具的概述，请参阅 [Azure PaaS compute diagnostics data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)（Azure PaaS 计算诊断数据）。

## <a name="q--a"></a>问题解答
**什么是缓冲区大小，应设置为多大？**

在每个虚拟机实例上，“配额”限制了本地文件系统上可以存储的诊断数据量。 另外，请指定每类可用诊断数据的缓冲区大小。 此缓冲区大小的作用类似于该类数据的单独配额。 若要确定整体配额和剩余内存量，请查看对话框的底部，了解诊断数据类型。 指定的缓冲区越大、数据类型越多，便越接近整体配额。 可以通过修改 diagnostics.wadcfg 或 .wadcfgx 配置文件更改整体配额。 诊断数据与应用程序的数据存储在同一文件系统上。 如果应用程序占用大量的磁盘空间，则不应增加整体诊断配额。

**什么是传输周期，应设置为多长？**

传输周期是两次数据捕获之间经过的时间长度。 在每个传输周期后，数据都会从虚拟机上的本地文件系统移到存储帐户的表中。 如果在传输周期结束之前，收集的数据量已经超出了配额，将放弃较早数据。 如果数据由于超出缓冲区大小或整体配额而丢失，则可能需要缩短传输周期。

**时间戳使用哪个时区？**

时间戳使用托管云服务的数据中心的本地时区。 使用日志表中的以下三个时间戳列：

* **PreciseTimeStamp**：事件的 ETW 时间戳。 即，从客户端记录事件的时间。
* **TIMESTAMP**：已向下舍入到上传频率边界的 PreciseTimeStamp 的值。 例如，如果上传频率为 5 分钟，事件时间为 00:17:12，则 TIMESTAMP 为 00:15:00。
* **Timestamp**：在 Azure 表中创建实体时的时间戳。

**收集诊断信息时，如何管理开销？**

默认设置（“日志级别”设置为“错误”，“传输周期”设置为“1 分钟”）设计为尽量减少开销。 收集的诊断数据越多，或者传输周期越短，计算开销便越大。 不要收集超过所需的数据，不再需要数据收集时务必禁用它。 始终可以再次启用该功能，即使在运行时也是如此，如本文前面所述。

**如何从 IIS 收集失败请求的日志？**

默认情况下，IIS 不收集失败请求的日志。 可以通过编辑 Web 角色的 web.config 文件，将 IIS 设置为收集失败的请求的日志。

**从 RoleEntryPoint 方法中，没能像 OnStart 中一样获得跟踪信息。什么地方错了？**

RoleEntryPoint 的方法在 WAIISHost.exe 上下文而非 IIS 中调用。 web.config 中通常会启用跟踪的配置信息在此不适用。 若要解决此问题，请向 Web 角色项目添加一个 .config 文件，并使其名称匹配包含 RoleEntryPoint 节点的输出程序集。 在默认 Web 角色项目中，.config 文件的名称应该是 WAIISHost.exe.config。将以下行添加到此文件：

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

在“属性”窗口中，将“复制到输出目录”属性设置为“始终复制”。

## <a name="next-steps"></a>后续步骤
若要详细了解 Azure 中的诊断日志记录，请参阅[在 Azure 云服务和虚拟机中启用诊断](cloud-services/cloud-services-dotnet-diagnostics.md)和[在 Azure 应用服务中启用 Web 应用的诊断日志记录](app-service/web-sites-enable-diagnostic-log.md)。

