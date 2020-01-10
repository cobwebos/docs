---
title: Azure SignalR 服务的诊断日志
description: 了解如何为 Azure SignalR 服务设置诊断日志，以及如何利用它来自行排除故障。
author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 33d9a338e12fa4b3d2449f0c5b0576895364c3cf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750263"
---
# <a name="diagnostic-logs-for-azure-signalr-service"></a>Azure SignalR 服务的诊断日志

本教程介绍了 Azure SignalR 服务的诊断日志，以及如何设置诊断日志，以及如何对诊断日志进行故障排除。

## <a name="prerequisites"></a>必备组件
若要启用诊断日志，需要在某个位置存储日志数据。 本教程使用 Azure 存储和 Log Analytics。

* [Azure 存储](../azure-monitor/platform/resource-logs-collect-storage.md)-保留用于策略审核、静态分析或备份的诊断日志。
* [Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) -一种灵活的日志搜索和分析工具，可用于分析由 Azure 资源生成的原始日志。

## <a name="set-up-diagnostic-logs-for-an-azure-signalr-service"></a>为 Azure SignalR 服务设置诊断日志

可以查看 Azure SignalR 服务的诊断日志。 这些日志提供了更丰富的 Azure SignalR 服务实例连接视图。 诊断日志提供了每个连接的详细信息。 例如，连接的基本信息（用户 ID、连接 ID 和传输类型等）和事件信息（连接、断开和中止事件等）。 诊断日志可用于问题识别、连接跟踪和分析。

### <a name="enable-diagnostic-logs"></a>启用诊断日志

诊断日志默认已禁用。 若要启用诊断日志，请执行以下步骤：

1.  在[Azure 门户](https://portal.azure.com)的 "**监视**" 下，单击 "**诊断设置**"。

    ![窗格导航到诊断设置](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1.  然后单击 "**添加诊断设置**"。

    ![添加诊断日志](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1.  设置所需的存档目标。 目前，我们支持**存档到存储帐户**并**发送到 Log Analytics**。

1. 选择要存档的日志。

    ![诊断设置窗格](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1.  保存新的诊断设置。

新设置在大约 10 分钟后生效。 在此之后，日志将出现在“诊断日志”窗格上配置的存档目标中。

有关配置诊断的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)。

### <a name="diagnostic-logs-categories"></a>诊断日志类别

Azure SignalR 服务在一个类别中捕获诊断日志：

* **所有日志**：跟踪连接到 Azure SignalR 服务的连接。 日志提供有关连接/断开连接、身份验证和限制的信息。 有关详细信息，请参阅下一节。

### <a name="archive-to-a-storage-account"></a>存档到存储帐户

日志存储在 "**诊断日志**" 窗格中配置的存储帐户中。 将自动创建一个名为 `insights-logs-alllogs` 的容器来存储诊断日志。 在容器中，日志存储在 `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`的文件中。 基本上，路径由 `resource ID` 和 `Date Time`组合在一起。 日志文件被 `hour`拆分。 因此，分钟数始终 `m=00`。

所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目均包含字符串字段，这些字段采用以下部分所述的格式。

存档日志 JSON 字符串包括下表中列出的元素：

**格式**

名称 | Description
------- | -------
time | 日志事件时间
级别 | 日志事件级别
resourceId | Azure SignalR 服务的资源 ID
location | Azure SignalR 服务的位置
category | 日志事件的类别
operationName | 事件的操作名称
callerIpAddress | 服务器/客户端的 IP 地址
properties | 与此日志事件相关的详细属性。 有关更多详细信息，请参阅下表中的属性表

**Properties 表**

名称 | Description
------- | -------
type | 日志事件的类型。 目前，我们提供有关连接到 Azure SignalR 服务的信息。 仅 `ConnectivityLogs` 类型可用
collection | 日志事件的集合。 允许的值为： `Connection`、`Authorization` 和 `Throttling`
connectionId | 连接的标识
transportType | 连接的传输类型。 允许的值为： `Websockets` \| `ServerSentEvents` \| `LongPolling`
connectionType | 连接的类型。 允许的值为： `Server` \| `Client`。 `Server`：从服务器端连接;`Client`：来自客户端的连接
userId | 用户标识
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

若要查看诊断日志，请执行以下步骤：

1. 在目标 Log Analytics 中单击 "`Logs`"。

    ![Log Analytics 菜单项](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. 输入 `SignalRServiceDiagnosticLogs`，并选择 "时间范围" 以查询诊断日志。 有关高级查询，请参阅[Azure Monitor 中的 Log Analytics 入门](../azure-monitor/log-query/get-started-portal.md)

    ![查询日志 Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

存档日志列包含下表中列出的元素：

名称 | Description
------- | ------- 
TimeGenerated | 日志事件时间
集合 | 日志事件的集合。 允许的值为： `Connection`、`Authorization` 和 `Throttling`
OperationName | 事件的操作名称
位置 | Azure SignalR 服务的位置
级别 | 日志事件级别
CallerIpAddress | 服务器/客户端的 IP 地址
消息 | 日志事件的详细消息
UserId | 用户标识
ConnectionId | 连接的标识
ConnectionType | 连接的类型。 允许的值为： `Server` \| `Client`。 `Server`：从服务器端连接;`Client`：来自客户端的连接
TransportType | 连接的传输类型。 允许的值为： `Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-diagnostic-logs"></a>诊断日志疑难解答

若要对 Azure SignalR 服务进行故障排除，可以启用服务器/客户端日志来捕获失败。 目前，Azure SignalR 服务公开诊断日志，你还可以为服务端启用日志。

遇到连接意外增长或丢弃情况时，可以利用诊断日志进行故障排除。

典型问题通常与连接的意外数量变化、连接达到连接限制和授权失败有关。 请参阅下一节，了解如何进行故障排除。

#### <a name="unexpected-connection-number-changes"></a>意外的连接号码更改

##### <a name="unexpected-connection-dropping"></a>意外的连接删除

如果遇到意外的连接丢弃，首先要启用服务、服务器和客户端的日志。

如果连接断开，诊断日志将记录此断开连接事件，你将看到 `operationName``ConnectionAborted` 或 `ConnectionEnded`。

`ConnectionAborted` 和 `ConnectionEnded` 之间的区别在于 `ConnectionEnded` 是客户端或服务器端触发的预期断开连接。 尽管 `ConnectionAborted` 通常是意外的连接删除事件，但会在 `message`中提供中止原因。

下表列出了中止原因：

原因 | Description
------- | ------- 
连接计数达到限制 | 连接计数达到当前定价层的限制。 考虑纵向扩展服务单位
应用程序服务器已关闭连接 | 应用服务器触发 abortion。 它可以被视为预期的 abortion
连接 ping 超时 | 通常，这是由网络问题导致的。 考虑从 internet 检查应用服务器的可用性
重新加载服务，重新连接 | 正在重新加载 Azure SignalR 服务。 Azure SignalR 支持自动重新连接，你可以等待，直到重新连接或手动重新连接到 Azure SignalR 服务
内部服务器暂时性错误 | Azure SignalR 服务中发生暂时性错误，应自动恢复
服务器连接已删除 | 服务器连接丢弃但出现未知错误，请考虑通过服务/服务器/客户端日志首先自行进行故障排除。 尝试排除基本问题（例如网络问题、应用服务器端问题，等等）。 如果未解决问题，请联系我们以获取更多帮助。 有关详细信息，请参阅[获取帮助](#get-help)部分。 

##### <a name="unexpected-connection-growing"></a>意外的连接增长

若要解决与意外连接有关的问题，您需要做的第一件事就是筛选出额外的连接。 您可以向测试客户端连接添加唯一的测试用户 ID。 然后使用诊断日志验证它，如果看到多个客户端连接具有相同的测试用户 ID 或 IP，则客户端可能会创建和建立比预期更多的连接。 检查客户端。

#### <a name="authorization-failure"></a>授权失败

如果为客户端请求返回401未经授权，请检查诊断日志。 如果遇到 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`，则表示访问令牌中的所有受众均无效。 尝试使用日志中建议的有效访问群体。


#### <a name="throttling"></a>限制

如果发现无法建立到 Azure SignalR 服务的 SignalR 客户端连接，请检查诊断日志。 如果在诊断日志中遇到 `Connection count reaches limit`，则与 SignalR 服务建立过多的连接，达到连接计数限制。 请考虑纵向扩展 SignalR 服务。 如果在诊断日志中遇到 `Message count reaches limit`，这意味着您将使用免费层，并且您将使用消息的配额。 如果要发送更多消息，请考虑将 SignalR 服务更改为 "标准" 层以发送其他消息。 有关详细信息，请参阅[Azure SignalR 服务价格](https://azure.microsoft.com/pricing/details/signalr-service/)。

### <a name="get-help"></a>获取帮助

我们建议你先亲自排除故障。 大多数问题都是由应用服务器或网络问题导致的。 遵循诊断日志和[基本故障排除指南](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md)中的[故障排除指南](#troubleshooting-with-diagnostic-logs)来找出根本原因。
如果仍无法解决问题，请考虑在 GitHub 中提出问题或在 Azure 门户中创建票证。
提供：
1. 发生问题时的时间范围大约30分钟
2. Azure SignalR 服务的资源 ID
3. 尽可能详细地说明详细信息：例如，microsoft.windows.appserver.2008 不发送消息，客户端连接断开等
4. 从服务器/客户端收集的日志以及其他可能有用的材料
5. 可有可无重现代码

> 注意：如果你在 GitHub 中打开问题，请将你的敏感信息（例如，资源 ID、服务器/客户端日志）保留为私有，只会将其私下发送到 Microsoft 组织中的成员。  
