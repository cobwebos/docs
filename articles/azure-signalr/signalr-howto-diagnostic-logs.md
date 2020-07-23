---
title: Azure SignalR 服务的资源日志
description: 了解如何设置 Azure SignalR 服务的资源日志，以及如何利用它来自行排查问题。
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 55482457058d01162116494b637661db40010a50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85131959"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Azure SignalR 服务的资源日志

本教程讨论 Azure SignalR 服务的资源日志是什么、如何设置资源日志，以及如何使用资源日志进行故障排除。 

## <a name="prerequisites"></a>先决条件
若要启用资源日志，需要指定某个位置用于存储日志数据。 本教程使用 Azure 存储和 Log Analytics。

* [Azure 存储](../azure-monitor/platform/resource-logs-collect-storage.md) - 保留策略审核、静态分析或备份的资源日志。
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) - 灵活的日志搜索和分析工具，可用于分析 Azure 资源生成的原始日志。

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>设置 Azure SignalR 服务的资源日志

可查看 Azure SignalR 服务的资源日志。 这些日志更详细地展示了与 Azure SignalR 服务实例之间的连接。 资源日志提供每个连接的详细信息。 例如，连接的基本信息（用户 ID、连接 ID 和传输类型等）和事件信息（连接、断开连接和中止事件等）。 资源日志可用于识别问题、跟踪连接和分析。

### <a name="enable-resource-logs"></a>启用资源日志

资源日志默认已禁用。 若要启用资源日志，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中的“监视”下，单击“诊断设置”。 

    ![在窗格中导航到诊断设置](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. 然后单击“添加诊断设置”。

    ![添加资源日志](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. 设置所需的存档目标。 目前，我们支持“存档到存储帐户”和“发送到 Log Analytics”。 

1. 选择要存档的日志。

    ![诊断设置窗格](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. 保存新的诊断设置。

新设置在大约 10 分钟后生效。 在此之后，日志将出现在“诊断日志”窗格上配置的存档目标中。

有关配置诊断的详细信息，请参阅 [Azure 资源日志概述](../azure-monitor/platform/platform-logs-overview.md)。

### <a name="resource-logs-categories"></a>资源日志类别

Azure SignalR 服务将资源日志捕获到一个类别中：

* **所有日志**：跟踪与 Azure SignalR 服务建立的连接。 日志提供有关连接/断开连接、身份验证和限制的信息。 有关详细信息，请参阅下一部分。

### <a name="archive-to-a-storage-account"></a>存档到存储帐户

日志存储在“诊断日志”窗格中配置的存储帐户内。 系统会自动创建一个名为 `insights-logs-alllogs` 的容器来存储资源日志。 日志存储在该容器中的 `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` 文件内。 简单而言，其路径由 `resource ID` 和 `Date Time` 组成。 日志文件按 `hour` 拆分。 因此，分钟值始终为 `m=00`。

所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目均包含字符串字段，这些字段采用以下部分所述的格式。

存档日志 JSON 字符串包含下表列出的元素：

**格式**

“属性” | 描述
------- | -------
time | 日志事件时间
level | 日志事件级别
ResourceId | Azure SignalR 服务的资源 ID
location | Azure SignalR 服务的位置
category | 日志事件的类别
operationName | 事件的操作名称
callerIpAddress | 服务器/客户端的 IP 地址
properties | 与此日志事件相关的详细属性。 有关更多详细信息，请参阅下面的属性表

**属性表**

“属性” | 说明
------- | -------
type | 日志事件的类型。 目前，我们提供有关与 Azure SignalR 服务建立的连接的信息。 仅 `ConnectivityLogs` 类型可用
collection | 日志事件的集合。 允许的值为：`Connection`、`Authorization` 和 `Throttling`
connectionId | 连接的标识
transportType | 连接的传输类型。 允许的值为：`Websockets` \| `ServerSentEvents` \| `LongPolling`
connectionType | 连接的类型。 允许的值为：`Server` \| `Client`。 `Server`：从服务器端建立的连接；`Client`：从客户端建立的连接
userId | 用户的标识
message | 日志事件的详细消息

以下代码是存档日志 JSON 字符串的示例：

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Log Analytics 的存档日志架构

若要查看资源日志，请执行以下步骤：

1. 在目标 Log Analytics 中单击 `Logs`。

    ![Log Analytics 菜单项](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. 输入 `SignalRServiceDiagnosticLogs`，并选择资源日志的查询时间范围。 若要进行高级查询，请参阅 [Azure Monitor 中的 Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)

    ![在 Log Analytics 中查询日志](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

存档日志列包含下表列出的元素：

名称 | 说明
------- | ------- 
TimeGenerated | 日志事件时间
集合 | 日志事件的集合。 允许的值为：`Connection`、`Authorization` 和 `Throttling`
OperationName | 事件的操作名称
位置 | Azure SignalR 服务的位置
Level | 日志事件级别
CallerIpAddress | 服务器/客户端的 IP 地址
Message | 日志事件的详细消息
UserId | 用户的标识
ConnectionId | 连接的标识
ConnectionType | 连接的类型。 允许的值为：`Server` \| `Client`。 `Server`：从服务器端建立的连接；`Client`：从客户端建立的连接
TransportType | 连接的传输类型。 允许的值为：`Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>使用资源日志进行故障排除

若要对 Azure SignalR 服务进行故障排除，可以启用服务器端/客户端日志来捕获故障。 目前，Azure SignalR 服务会公开资源日志，你也可以为服务端启用日志。

遇到连接意外增加或丢失的情况时，可以利用资源日志进行故障排除。

典型问题通常与连接的意外数量更改、连接达到限制和授权失败有关。 请参阅下一部分了解如何进行故障排除。

#### <a name="unexpected-connection-number-changes"></a>意外的连接数更改

##### <a name="unexpected-connection-dropping"></a>意外的连接丢失

如果遇到意外的连接丢失，请先在服务、服务器和客户端启用日志。

如果连接断开，资源日志将记录此断开连接事件；在 `ConnectionEnded` 或 `operationName` 中会看到 `ConnectionAborted`。

`ConnectionAborted` 和 `ConnectionEnded` 之间的区别在于，`ConnectionEnded` 是客户端或服务器端触发的预期断开连接。 而 `ConnectionAborted` 通常是意外的连接丢失事件，中止原因会在 `message` 中提供。

下表列出了中止原因：

Reason | 说明
------- | ------- 
连接计数达到限制 | 连接计数达到当前定价层的限制。 考虑纵向扩展服务单元
应用程序服务器关闭了连接 | 应用服务器触发了中止。 可将其视为预期的中止
连接 ping 超时 | 通常，此问题是由网络问题造成的。 考虑从 Internet 检查应用服务器的可用性
服务重新加载并重新连接 | Azure SignalR 服务正在重新加载。 Azure SignalR 支持自动重新连接。可以等到已重新连接，或手动重新连接到 Azure SignalR 服务
内部服务器的暂时性错误 | Azure SignalR 服务中发生暂时性错误，应可自动恢复
服务器连接已丢失 | 服务器连接丢失并出现未知错误，请考虑先使用服务/服务器/客户端日志自我进行故障排除。 尝试排除基本问题（例如网络问题、应用服务器端问题等）。 如果问题未解决，请联系我们以获得更多帮助。 有关详细信息，请参阅[获取帮助](#get-help)部分。 

##### <a name="unexpected-connection-growing"></a>意外的连接增加

若要排查连接意外增加的问题，首先需要筛选出额外的连接。 可以向测试客户端连接添加唯一的测试用户 ID。 然后使用资源日志验证该 ID，如果看到多个客户端连接使用同一测试用户 ID 或 IP，则可能表示客户端创建并建立了超过预期数目的连接。 检查客户端。

#### <a name="authorization-failure"></a>授权失败

如果针对客户端请求返回了“401 未授权”，请检查资源日志。 如果遇到 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`，则表示访问令牌中的所有受众无效。 尝试使用日志中建议的有效受众。


#### <a name="throttling"></a>限制

如果发现无法在 SignalR 客户端与 Azure SignalR 服务之间建立连接，请检查资源日志。 如果在资源日志中遇到 `Connection count reaches limit`，则表示与 SignalR 服务建立的连接过多，从而达到了连接计数限制。 考虑纵向扩展 SignalR 服务。 如果在资源日志中遇到 `Message count reaches limit`，则表示使用的是免费层，并且已用完了消息配额。 若要发送更多消息，请考虑将 SignalR 服务更改为标准层。 有关详细信息，请参阅 [Azure SignalR 服务定价](https://azure.microsoft.com/pricing/details/signalr-service/)。

### <a name="get-help"></a>获取帮助

我们建议先自行排查问题。 大多数问题都是由应用服务器或网络问题造成的。 遵循[使用资源日志进行故障排除的指南](#troubleshooting-with-resource-logs)和[基本故障排除指南](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md)找出根本原因。
如果依然无法解决问题，请考虑在 GitHub 中提出问题或者在 Azure 门户中创建票证。
提供：
1. 问题发生前后大约 30 分钟的时间范围
2. Azure SignalR 服务的资源 ID
3. 问题详情，越具体越好：例如，应用服务器不发送消息、客户端连接丢失，等等
4. 从服务器/客户端以及其他可能有用的材料收集的日志
5. [可选] 重现代码

> [!NOTE]
> 如果在 GitHub 中提出问题，请将你的敏感信息（例如资源 ID、服务器/客户端日志）保密，并只以私密方式发送到 Microsoft 组织中的成员。