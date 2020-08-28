---
title: Windows 虚拟桌面诊断日志分析-Azure
description: 如何将 log analytics 用于 Windows 虚拟桌面诊断功能。
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a3fccc934fafd8ff7db2cffbd6ba641329ba8de2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006799"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>使用诊断功能 Log Analytics

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)。

Windows 虚拟桌面使用 [Azure Monitor](../azure-monitor/overview.md) 来监视和警报，例如许多其他 Azure 服务。 这样，管理员便可以通过单个界面识别问题。 此服务为用户和管理操作创建活动日志。 每个活动日志分为以下类别：

- 管理活动：
    - 跟踪尝试使用 Api 还是 PowerShell 更改 Windows 虚拟桌面对象是否成功。 例如，某人是否可以使用 PowerShell 成功创建主机池？
- 馈电
    - 用户能否成功订阅工作区？
    - 用户是否能看到远程桌面客户端中发布的所有资源？
- 连接:
    - 当用户启动和完成与服务的连接时。
- 主机注册：
    - 会话主机是否在连接时已成功注册到服务？
- 错误：
    - 用户是否遇到特定活动的任何问题？ 只要信息与活动联接，此功能就会生成一个跟踪活动数据的表。
- 检查点
    - 已达到活动生存期的特定步骤。 例如，在会话期间，用户负载平衡到特定主机，然后用户在连接过程中登录，等等。

由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

Azure Monitor 使你能够分析 Windows 虚拟桌面数据，并查看虚拟机 (VM) 性能计数器，一切都在同一工具内。 本文将介绍如何为 Windows 虚拟桌面环境启用诊断。

>[!NOTE]
>若要了解如何在 Azure 中监视 Vm，请参阅 [利用 Azure Monitor 监视 azure 虚拟机](../azure-monitor/insights/monitor-vm-azure.md)。 此外，请确保 [查看性能计数器阈值](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) ，以便更好地了解你在会话主机上的用户体验。

## <a name="before-you-get-started"></a>准备工作

你需要创建工作区，然后才能使用 Log Analytics。 为此，请按照以下两篇文章之一中的说明进行操作：

- 如果你更喜欢使用 Azure 门户，请参阅 [在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。
- 如果喜欢 PowerShell，请参阅 [使用 Powershell 创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace-posh.md)。

创建工作区后，请按照 [将 Windows 计算机连接到 Azure Monitor](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) 中的说明获取以下信息：

- 工作区 ID
- 工作区的主密钥

稍后将在安装过程中需要此信息。

请确保查看 Azure Monitor 的权限管理，为监视和维护你的 Windows 虚拟桌面环境的用户启用数据访问。 有关详细信息，请参阅 [Azure Monitor 的角色、权限和安全入门](../azure-monitor/platform/roles-permissions-security.md)。

## <a name="push-diagnostics-data-to-your-workspace"></a>将诊断数据推送到你的工作区

可以将 Windows 虚拟桌面对象中的诊断数据推送到工作区的 Log Analytics 中。 首次创建对象时，可以立即设置此功能。

为新对象设置 Log Analytics：

1. 登录到 Azure 门户并前往 **Windows 虚拟桌面**。

2. 导航到要捕获其日志和事件的对象 (例如主机池、应用组或工作区) 。

3. 在屏幕左侧的菜单中选择 " **诊断设置** "。

4. 在屏幕右侧显示的菜单中，选择 " **添加诊断设置** "。

    "诊断设置" 页中显示的选项会因所编辑的对象类型而异。

    例如，在为应用组启用诊断时，将看到用于配置检查点、错误和管理的选项。 对于工作区，这些类别将配置源以跟踪用户订阅应用列表的时间。 若要了解有关诊断设置的详细信息，请参阅 [创建诊断设置以收集 Azure 中的资源日志和指标](../azure-monitor/platform/diagnostic-settings.md)。

     >[!IMPORTANT]
     >请记得为要监视的每个 Azure 资源管理器对象启用诊断。 启用诊断后，数据将可用于活动。 首次设置后可能需要几个小时。

5. 输入设置配置的名称，然后选择 " **发送到 Log Analytics**"。 你使用的名称不应包含空格，并且应符合 [Azure 命名约定](../azure-resource-manager/management/resource-name-rules.md)。 作为日志的一部分，你可以选择想要添加到 Log Analytics 的所有选项，如检查点、错误、管理等。

6. 选择“保存” 。

>[!NOTE]
>Log Analytics 提供将数据流式传输到 [事件中心](../event-hubs/event-hubs-about.md) 或将其存档在存储帐户中的选项。 若要了解有关此功能的详细信息，请参阅将 [azure 监视数据流式传输到事件中心](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) 和 [将 Azure 资源日志存档到存储帐户](../azure-monitor/platform/resource-logs-collect-storage.md)。

## <a name="how-to-access-log-analytics"></a>如何访问 Log Analytics

可以在 Azure 门户或 Azure Monitor 上访问 Log Analytics 工作区。

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics 工作区上的访问 Log Analytics

1. 登录到 Azure 门户。

2. 搜索 **Log Analytics 工作区**。

3. 在 "服务" 下，选择 " **Log Analytics 工作区**"。

4. 从列表中，选择为 Windows 虚拟桌面对象配置的工作区。

5. 进入工作区后，选择 " **日志**"。 可以通过 **搜索** 功能筛选菜单列表。

### <a name="access-log-analytics-on-azure-monitor"></a>访问 Azure Monitor 上的 Log Analytics

1. 登录到 Azure 门户

2. 搜索并选择“Monitor”。

3. 选择“日志”。 

4. 按照 "日志记录" 页中的说明设置查询的作用域。

5. 你已准备好查询诊断。 所有诊断表都具有 "WVD" 前缀。

>[!NOTE]
>有关 Azure Monitor 日志中存储的表的更多详细信息，请参阅 [Azure Monitor 数据引用](https://docs.microsoft.com/azure/azure-monitor/reference/)。 与 Windows 虚拟桌面相关的所有表都标记为 "WVD"。

## <a name="cadence-for-sending-diagnostic-events"></a>用于发送诊断事件的节奏

完成后，会将诊断事件发送到 Log Analytics。

仅 Log Analytics 连接活动的以下中间状态的报表：

- 已启动：用户在远程桌面客户端中选择并连接到应用或桌面。
- 已连接：当用户成功连接到承载应用或桌面的 VM 时。
- 已完成：当用户或服务器断开与活动发生时所在的会话时。

## <a name="example-queries"></a>查询示例

通过 Azure Monitor Log Analytics UI 访问示例查询：
1. 中转到 Log Analytics 工作区，然后选择 " **日志**"。 示例查询 UI 将自动显示。
1. 将筛选器更改为 " **类别**"。
1. 选择 **Windows 虚拟桌面** 查看可用查询。
1. 选择 " **运行** " 以运行所选查询。

请在 Azure Monitor Log Analytics 中详细了解 [已保存查询](../azure-monitor/log-query/saved-queries.md)中的示例查询接口。

下面的查询列表允许您查看单个用户的连接信息或问题。 可以在 [Log Analytics 查询编辑器](../azure-monitor/log-query/get-started-portal.md#write-and-run-basic-queries)中运行这些查询。 对于每个查询， `userupn` 将替换为要查找的用户的 UPN。


查找单个用户的所有连接：

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


要查找用户每天连接的次数，请执行以下操作：

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

按用户查找会话持续时间：

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

查找特定用户的错误：

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

若要确定是否为其他用户发生了特定错误：

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- 当用户打开完整桌面时，不会在 WVDCheckpoints 表中将其应用程序使用情况作为检查点进行跟踪。
>- WVDConnections 表中的 ResourcesAlias 列显示用户是连接到完整的桌面还是已发布的应用。 该列只显示在连接期间打开的第一个应用。 用户打开的任何已发布应用都将在 WVDCheckpoints 中进行跟踪。
>- WVDErrors 表显示管理错误、主机注册问题以及在用户订阅应用或桌面列表时出现的其他问题。
>- WVDErrors 可帮助你确定管理任务可以解决的问题。 对于这些类型的问题，ServiceError 上的值始终显示为 "false"。 如果 ServiceError = "true"，则需要将问题升级到 Microsoft。 确保你为升级的错误提供了 CorrelationID。

## <a name="next-steps"></a>后续步骤

若要查看诊断功能可以识别的常见错误方案，请参阅 [确定和诊断问题](diagnostics-role-service.md#common-error-scenarios)。
