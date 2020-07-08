---
title: 排查连接问题-Azure 事件中心 |Microsoft Docs
description: 本文提供了有关排查 Azure 事件中心连接问题的信息。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 15c93873a25e70b0f9a88fc5ea621b90d58e7581
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322380"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>排查连接问题-Azure 事件中心
客户端应用程序无法连接到事件中心的原因有多种。 你遇到的连接问题可能是永久性或暂时性的。 如果问题一直发生（永久），则可能需要检查连接字符串、组织的防火墙设置、IP 防火墙设置、网络安全设置（服务终结点、专用终结点等）等。 对于暂时性问题，升级到最新版本的 SDK，运行命令以检查已丢弃的数据包，获取网络跟踪有助于解决问题。 

本文提供了有关排查 Azure 事件中心连接问题的提示。 

## <a name="troubleshoot-permanent-connectivity-issues"></a>永久性连接问题疑难解答
如果应用程序根本无法连接到事件中心，请按照此部分中的步骤进行操作以解决此问题。 

### <a name="check-if-there-is-a-service-outage"></a>检查是否存在服务中断
在[azure 服务状态站点](https://azure.microsoft.com/status/)上检查 Azure 事件中心服务中断。

### <a name="verify-the-connection-string"></a>验证连接字符串 
验证所使用的连接字符串是否正确。 有关使用 Azure 门户、CLI 或 PowerShell 获取连接字符串，请参阅[获取连接字符串](event-hubs-get-connection-string.md)。 

对于 "Kafka 客户端"，验证是否已正确配置 producer.config 或 consumer.config 文件。 有关详细信息，请参阅[在事件中心中通过 Kafka 发送和接收消息](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs)。

### <a name="check-if-the-ports-required-to-communicate-with-event-hubs-are-blocked-by-organizations-firewall"></a>检查组织的防火墙是否阻止了与事件中心进行通信所需的端口
验证在组织的防火墙上未阻止用于与 Azure 事件中心通信的端口。 请参阅下表，了解需要打开与 Azure 事件中心进行通信所需的出站端口。 

| 协议 | 端口 | 详细信息 | 
| -------- | ----- | ------- | 
| AMQP | 5671 和 5672 | 请参阅 [AMQP 协议指南](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP、HTTPS | 80、443 |  |
| Kafka | 9093 | 请参阅[使用 Kafka 应用程序中的事件中心](event-hubs-for-kafka-ecosystem-overview.md)

下面是一个示例命令，用于检查5671端口是否被阻止。

```powershell
tnc <yournamespacename>.servicebus.windows.net -port 5671
```

在 Linux 上：

```shell
telnet <yournamespacename>.servicebus.windows.net 5671
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>验证是否允许在企业防火墙中使用 IP 地址
使用 Azure 时，有时必须允许企业防火墙或代理中的特定 IP 地址范围或 Url 访问正在使用或尝试使用的所有 Azure 服务。 验证事件中心使用的 IP 地址是否允许流量。 对于 Azure 事件中心使用的 IP 地址：请参阅[AZURE IP 范围和服务标记-公有云](https://www.microsoft.com/download/details.aspx?id=56519)和[服务标记-EventHub](network-security.md#service-tags)。

同时，验证是否允许命名空间的 IP 地址。 若要查找允许连接的正确 IP 地址，请执行以下步骤：

1. 从命令提示符运行以下命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 记下 `Non-authoritative answer` 中返回的 IP 地址。 只有将命名空间还原到其他群集时，它才会更改。

如果对命名空间使用区域冗余，则需要执行一些额外步骤： 

1. 首先，在命名空间上运行 nslookup。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 记下“非权威答案”部分中的名称，其采用以下格式之一： 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 对后缀为 s1、s2 和 s3 的每个实例运行 nslookup，以获取三个可用性区域中运行的所有三个实例的 IP 地址。 

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>检查应用程序是否需要在 vnet 的特定子网中运行
确认应用程序正在有权访问该命名空间的虚拟网络子网中运行。 如果不是这样，请在子网中运行可访问该命名空间的应用程序，或者将运行应用程序的计算机的 IP 地址添加到[IP 防火墙](event-hubs-ip-filtering.md)。 

为事件中心命名空间创建虚拟网络服务终结点时，命名空间只接受绑定到服务终结点的子网中的流量。 此行为有一个例外。 可以在 IP 防火墙中添加特定的 IP 地址，以便能够访问事件中心公共终结点。 有关详细信息，请参阅[网络服务终结点](event-hubs-service-endpoints.md)。

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>检查命名空间的 IP 防火墙设置
检查运行应用程序的计算机的 IP 地址是否未被 IP 防火墙阻止。  

默认情况下，只要请求附带有效的身份验证和授权，就可以从 Internet 访问事件中心命名空间。 有了 IP 防火墙，就可以使用 [CIDR（无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法将其进一步限制为仅一组 IPv4 地址或 IPv4 地址范围。

IP 防火墙规则应用于事件中心命名空间级别。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 如果某 IP 地址与事件中心命名空间上的允许 IP 规则不匹配，则将拒绝来自该地址的任何连接尝试并将其标记为“未经授权”。 响应不会提及 IP 规则。 IP 筛选器规则将按顺序应用，与 IP 地址匹配的第一个规则决定了将执行接受操作还是执行拒绝操作。

有关详细信息，请参阅为[Azure 事件中心命名空间配置 IP 防火墙规则](event-hubs-ip-filtering.md)。 若要检查是否具有 IP 筛选、虚拟网络或证书链问题，请参阅[排查网络相关问题](#troubleshoot-network-related-issues)。

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>查找 IP 防火墙阻止的 IP 地址
按照[启用诊断日志](event-hubs-diagnostic-logs.md#enable-diagnostic-logs)中的说明，为[事件中心虚拟网络连接事件](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema)启用诊断日志。 你将看到已拒绝的连接的 IP 地址。

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>检查是否只能使用专用终结点访问命名空间
如果事件中心命名空间配置为仅可通过专用终结点访问，请确认该客户端应用程序正在通过专用终结点访问该命名空间。 

使用[Azure 专用链接服务](../private-link/private-link-overview.md)，可以通过虚拟网络中的**专用终结点**访问 azure 事件中心。 专用终结点是一个网络接口，可以通过专用且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[配置专用终结点](private-link-service.md)。 

### <a name="troubleshoot-network-related-issues"></a>解决与网络相关的问题
若要排查事件中心与网络相关的问题，请执行以下步骤： 

浏览至 `https://<yournamespacename>.servicebus.windows.net/` 或使用 [wget](https://www.gnu.org/software/wget/)。 它有助于检查是否存在 IP 筛选或虚拟网络或证书链问题（使用 Java SDK 时最常见）。

**成功消息**的示例：

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

**失败错误消息**的示例：

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>排查暂时性连接问题
如果遇到间歇性连接问题，请参阅以下部分，了解故障排除提示。 

### <a name="use-the-latest-version-of-the-client-sdk"></a>使用最新版本的客户端 SDK
某些暂时性连接问题可能已在 SDK 的更高版本中得到修复，而不是你使用的版本。 确保在应用程序中使用最新版本的客户端 Sdk。 Sdk 是通过新的/更新的功能和 bug 修复持续改进的，因此请始终通过最新的包进行测试。 查看发行说明，了解已修复的问题以及添加/更新的功能。 

有关客户端 Sdk 的信息，请参阅[Azure 事件中心-客户端 sdk 一](sdks.md)文。 

### <a name="run-the-command-to-check-dropped-packets"></a>运行命令来检查丢弃的数据包
存在间歇性连接问题时，请运行以下命令，检查是否有丢弃的数据包。 此命令会尝试通过服务每隔 1 秒建立 25 个不同的 TCP 连接。 然后，可以检查其中有多少成功/失败，还可以查看 TCP 连接延迟。 可以从[此处](/sysinternals/downloads/psping)下载 `psping` 工具。

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
如果使用的是其他工具（如 `tnc`、`ping` 等），则可以使用等效的命令。 

如果上述步骤没有帮助，请获取网络跟踪，并使用 [Wireshark](https://www.wireshark.org/) 之类的工具对其进行分析。 如果需要，请联系 [Microsoft 支持部门](https://support.microsoft.com/)。 

### <a name="service-upgradesrestarts"></a>服务升级/重新启动
由于后端服务升级和重启，可能会出现暂时性连接问题。 出现这种情况时，你可能会看到以下症状： 

- 传入的消息/请求可能会减少。
- 日志文件可能包含错误消息。
- 应用程序可能会在几秒内断开与服务的连接。
- 可能会暂时限制请求。

如果应用程序代码使用 SDK，则重试策略已内置且处于活动状态。 应用程序会重新连接，此操作不会对应用程序/工作流产生重大影响。 否则，请在几分钟后重试连接到服务，以查看问题是否消失。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

* [排查身份验证和授权问题](troubleshoot-authentication-authorization.md)
