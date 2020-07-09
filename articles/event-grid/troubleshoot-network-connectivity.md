---
title: 排查网络连接问题-Azure 事件网格 |Microsoft Docs
description: 本文介绍有关 Azure 事件网格中的网络连接问题疑难解答的信息。
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: 5eb40d464fb718f0bd6dffe0d00f6420f4ea4995
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118998"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>排查连接问题-Azure 事件网格

客户端应用程序无法连接到事件网格主题/域的原因有多种。 你遇到的连接问题可能是永久性或暂时性的。 如果此问题一直发生（永久），则可能需要检查组织的防火墙设置、IP 防火墙设置、服务标记、私有终结点等。 对于暂时性问题，运行命令来检查丢弃的数据包，获取网络跟踪有助于解决问题。

本文提供了有关通过 Azure 事件网格解决连接问题的提示。

## <a name="troubleshoot-permanent-connectivity-issues"></a>永久性连接问题疑难解答

如果应用程序根本无法连接到事件网格，请按照此部分中的步骤进行操作以解决此问题。

### <a name="check-if-there-is-a-service-outage"></a>检查是否存在服务中断

在[azure 服务状态站点](https://azure.microsoft.com/status/)上检查 Azure 事件网格服务是否中断。

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>检查与事件网格通信所需的端口是否未被组织的防火墙阻止

验证在组织的防火墙上未阻止用于与 Azure 事件网格通信的端口。 请参阅下表，了解需要打开与 Azure 事件网格通信的出站端口。

| 协议 | 端口 |
| -------- | ----- |
| HTTPS    | 443   |

下面是一个示例命令，用于检查443端口是否被阻止。

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

在 Linux 上：

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>验证是否允许在企业防火墙中使用 IP 地址

使用 Azure 时，有时必须允许企业防火墙或代理中的特定 IP 地址范围或 Url 访问正在使用或尝试使用的所有 Azure 服务。 验证事件网格所使用的 IP 地址是否允许流量。 对于 Azure 事件网格所使用的 IP 地址：请参阅[AZURE IP 范围和服务标记-公有云](https://www.microsoft.com/download/details.aspx?id=56519)和[服务标记-AzureEventGrid](network-security.md#service-tags)。

> [!NOTE]
> 可以将新的 IP 地址添加到 AzureEventGrid 服务标记中，但这并不是正常的。 因此，最好是对服务标记执行每周一次检查。

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>验证网络安全组中是否允许 AzureEventGrid 服务标记

如果你的应用程序在子网内运行，并且存在关联的网络安全组，请确认是否允许 internet 出站或允许 AzureEventGrid 服务标记。 请参阅[服务标记](../virtual-network/service-tags-overview.md)

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>检查主题/域的 IP 防火墙设置

检查运行应用程序的计算机的公共 IP 地址是否未被 EventGrid 主题/域 IP 防火墙阻止。

默认情况下，只要请求附带有效的身份验证和授权，就可以从 internet 访问事件网格主题/域。 有了 IP 防火墙，就可以使用 [CIDR（无类别域间路由）](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)表示法将其进一步限制为仅一组 IPv4 地址或 IPv4 地址范围。

在事件网格主题/域级别应用 IP 防火墙规则。 因此，这些规则适用于通过任何受支持协议从客户端发出的所有连接。 由于与事件网格主题/域上的允许的 IP 规则不匹配的 IP 地址的任何连接尝试被拒绝。 响应不会提及 IP 规则。

有关详细信息，请参阅为[Azure 事件网格主题/域配置 IP 防火墙规则](configure-firewall.md)。

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>查找 IP 防火墙阻止的 IP 地址

启用事件网格的诊断日志主题/域[启用诊断日志](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic)。 你将看到已拒绝的连接的 IP 地址。

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>检查是否只能使用专用终结点访问 EventGrid 主题/域

如果事件网格主题/域配置为仅可通过专用终结点访问，请确认该客户端应用程序正在通过专用终结点访问主题/域。 若要确认此情况，请检查客户端应用程序是否在子网内运行，以及该子网中的事件网格主题/域是否有专用终结点。

[Azure 专用链接服务](../private-link/private-link-overview.md)使你能够通过虚拟网络中的**专用终结点**访问 azure 事件网格。 专用终结点是一个网络接口，可以通过专用且安全的方式将你连接到 Azure 专用链接支持的服务。 专用终结点使用 VNet 中的专用 IP 地址将服务有效接入 VNet 中。 发往服务的所有流量都可以通过专用终结点路由，因此不需要网关、NAT 设备、ExpressRoute 或 VPN 连接或公共 IP 地址。 虚拟网络与服务之间的流量将通过 Microsoft 主干网络，因此不会从公共 Internet 泄露。 可以连接到 Azure 资源的实例，从而获得最高级别的访问控制粒度。

有关详细信息，请参阅[配置专用终结点](configure-private-endpoints.md)。

## <a name="troubleshoot-transient-connectivity-issues"></a>排查暂时性连接问题

如果遇到间歇性连接问题，请参阅以下部分，了解故障排除提示。

### <a name="run-the-command-to-check-dropped-packets"></a>运行命令来检查丢弃的数据包

存在间歇性连接问题时，请运行以下命令，检查是否有丢弃的数据包。 此命令会尝试通过服务每隔 1 秒建立 25 个不同的 TCP 连接。 然后，可以检查其中有多少成功/失败，还可以查看 TCP 连接延迟。 可以从[此处](/sysinternals/downloads/psping)下载 `psping` 工具。

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

如果使用其他工具（如tcpping.exe），则可以使用等效的命令 `tcpping` [ ](https://www.elifulkerson.com/projects/tcping.php)。

如果上述步骤没有帮助，请获取网络跟踪，并使用 [Wireshark](https://www.wireshark.org/) 之类的工具对其进行分析。 如果需要，请联系 [Microsoft 支持部门](https://support.microsoft.com/)。

### <a name="service-upgradesrestarts"></a>服务升级/重新启动

由于后端服务升级和重启，可能会出现暂时性连接问题。 出现这种情况时，你可能会看到以下症状：

- 传入的消息/请求可能会减少。
- 日志文件可能包含错误消息。
- 应用程序可能会在几秒内断开与服务的连接。
- 可能会暂时限制请求。

捕获这些暂时性错误，然后再重试，然后重试调用将确保你的代码可从这些暂时性问题中恢复。

## <a name="next-steps"></a>后续步骤

如需更多帮助，请在 [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-eventgrid)中发布问题，或开具[支持票证](https://azure.microsoft.com/support/options/)。
