---
title: "逻辑应用安装本地数据网关 | Microsoft Docs"
description: "有关如何安装本地数据网关以便在逻辑应用中使用的信息。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ddd62d3112a2c93ea3c935ce89ff387049899c09


---
# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>为逻辑应用安装本地数据网关
## <a name="installation-and-configuration"></a>安装和配置
### <a name="prerequisites"></a>先决条件
最低：

* .NET 4.5 Framework
* 64 位版本的 Windows 7 或 Windows Server 2008 R2（或更高版本）

建议：

* 8 核 CPU
* 8 GB 内存
* 64 位版本的 Windows 2012 R2（或更高版本）

相关注意事项：

* 不能在域控制器上安装网关。
* 不应在可能关闭、休眠或未连接到 Internet 的计算机（如笔记本电脑）上安装网关，因为网关在以上任何情况下都无法运行。 此外，网关性能在无线网络上可能会受到影响。

### <a name="install-a-gateway"></a>安装网关
可以[在此处获取本地数据网关的安装程序](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

指定**本地数据网关**作为模式，使用工作或学校帐户登录，然后配置新网关或是迁移、还原或接管现有网关。

* 若要配置网关，请为它输入**名称**和**恢复密钥**，然后单击或点击“配置”。
  
    指定包含至少八个字符的恢复密钥，并将它保存在安全位置。 如果要迁移、还原或接管网关，则需要此密钥。
* 若要迁移、还原或接管现有网关，请提供创建网关时指定的恢复密钥。

### <a name="restart-the-gateway"></a>重新启动网关
网关作为 Windows 服务运行，与任何其他 Windows 服务一样，可以通过多种方式启动和停止它。 例如，可以在运行网关的计算机上使用提升的权限打开命令提示符，然后运行以下任一命令：

* 若要启动该服务，请运行以下命令：
  
    `net stop PBIEgwService`
* 若要启动该服务，请运行以下命令：
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>配置防火墙或代理
有关如何为网关提供代理信息的信息，请参阅 [配置代理设置](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/)。

可以通过从 PowerShell 提示符运行以下命令，来验证防火墙或代理是否可能在阻止连接。 这会测试与 Azure 服务总线之间的连接。 这只测试网络连接，与云服务器服务或网关无关。 它可帮助确定计算机是否可以实际访问 Internet。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

结果应类似于以下示例。 如果 **TcpTestSucceeded** 不为 true，则你可能会被防火墙阻止。

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

如果需要详尽信息，请将 **ComputerName** 和 **Port** 值替换为本主题后面[配置端口](#configure-ports)下列出的值。

防火墙可能还会阻止 Azure 服务总线与 Azure 数据中心进行的连接。 如果是这种情况，则你需要将针对这些数据中心的区域的所有 IP 地址列入白名单（取消阻止）。 可以[在此处获取 Azure IP 地址](https://www.microsoft.com/download/details.aspx?id=41653)的列表。

### <a name="configure-ports"></a>配置端口
网关会创建与 Azure 服务总线之间的出站连接。 它在出站端口上进行通信：TCP 443（默认值）、5671、5672、9350 到 9354。 网关不需要入站端口。

了解有关[混合解决方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)的详细信息。

| 域名 | 出站端口 | 说明 |
| --- | --- | --- |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |高级消息队列协议 (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |通过 TCP 的服务总线中继上的侦听器（需要 443 来获取访问控制令牌） |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |在 Power BI 服务无法访问网关时用于测试 Internet 连接。 |

如果需要将 IP 地址而不是域列入白名单，则可以下载和使用 [Microsoft Azure 数据中心 IP 范围列表](https://www.microsoft.com/download/details.aspx?id=41653)。 在某些情况下，Azure 服务总线连接会使用 IP 地址而不是完全限定的域名来建立。

### <a name="sign-in-account"></a>登录帐户
用户会使用工作或学校帐户进行登录。 这是组织帐户。 如果你注册了 Office 365 产品，但是未提供实际工作电子邮件，则该帐户可能类似于 jeff@contoso.onmicrosoft.com.云服务中的帐户会存储在 Azure Active Directory (AAD) 中的租户中。 在大多数情况下，AAD 帐户的 UPN 会与电子邮件地址匹配。

### <a name="windows-service-account"></a>Windows 服务帐户
本地数据网关配置为将 NT SERVICE\PBIEgwService 用于 Windows 服务登录凭据。 默认情况下，它具有作为服务登录的权限。 这是要在其安装网关的计算机的上下文。

这不是用于连接到本地数据源的帐户或是用于登录到云服务的工作或学校帐户。

## <a name="frequently-asked-questions"></a>常见问题
### <a name="general"></a>常规
**问题**：网关支持哪些数据源？<br/>
**答案**：撰写本文时是 SQL Server。

**问题**：对于云中的数据源（如 SQL Azure），我是否需要网关？ <br/>
**答案**：否。 网关只连接到本地数据源。

**问题**：实际 Windows 服务的名称是什么？<br/>
**答案**：在“服务”中，网关名为“Power BI 企业网关服务”。

**问题**：是否存在从云到网关的入站连接？ <br/>
**答案**：否。 网关使用与 Azure 服务总线之间的出站连接。

**问题**：如果我阻止出站连接，会发生什么情况？ 我需要打开什么？ <br/>
**答案**：查看网关使用的端口和主机。

**问题**：网关是否必须安装在与数据源相同的计算机上？ <br/>
**答案**：否。 网关将使用提供的连接信息连接到数据源。 从这个意义上说，将网关视为客户端应用程序。 它只需能够连接到提供的服务器名称。

**问题**：从网关运行对数据源的查询时的延迟是多少？ 最佳体系结构是什么？ <br/>
**答案**：若要减少网络延迟，请在网关安装在尽可能接近数据源的位置处。 如果可以在实际数据源上安装网关，则会最大程度减少引入的延迟。 还需考虑数据中心。 例如，如果你的服务在使用美国西部数据中心，并且你在 Azure VM 中托管了 SQL Server，则你在美国西部也需要 Azure VM。 这会最大程度减少延迟并避免 Azure VM 上的传出费用。

**问题**：对于网络带宽是否有要求？ <br/>
**答案**：建议网络连接具有较高吞吐量。 每个环境是不同的，所发送的数据量会影响结果。 使用 ExpressRoute 可以帮助保证本地与 Azure 数据中心之间的吞吐量级别。

可以使用第三方工具 Azure Speed Test 应用来帮助测量吞吐量。

**问题**：是否可以使用 Azure Active Directory 帐户运行网关 Windows 服务？ <br/>
**答案**：否。 该 Windows 服务必须具有有效的 Windows 帐户。 默认情况下，它会使用服务 SID NT SERVICE\PBIEgwService 来运行。

**问题**：如何将结果发送回云？ <br/>
**答案**：这通过 Azure 服务总线来进行。 有关详细信息信息，请参见其工作原理。

**问题**：我的凭据存储在何处？ <br/>
**答案**︰为数据源输入的凭据以加密方式存储在网关云服务中。 凭据在本地网关进行解密。

### <a name="high-availabilitydisaster-recovery"></a>高可用性/灾难恢复
**问题**：是否有使用网关实现高可用性方案的计划？ <br/>
**答案**：这在规划之中，但我们还没有时间表。

**问题**：有哪些选项可用于灾难恢复？ <br/>
**答案**：可以使用恢复密钥还原或移动网关。 安装网关时，指定恢复密钥。

**问题**：恢复密钥的好处是什么？ <br/>
**答案**：它提供了一种方法，用于在发生灾难之后迁移或恢复网关设置。

### <a name="troubleshooting"></a>故障排除
**问题**：网关日志在何处？ <br/>
**答案**：请参阅本主题后面的“工具”。

**问题**：如何查看正在发送到本地数据源的查询？ <br/>
**答案**：可以启用查询跟踪，这包括正在发送的查询。 请记住在故障排除完成时将它更改回原始值。 让查询跟踪保持启用状态会导致日志较大。

还可以查看数据源用于跟踪查询的工具。 例如，可以使用 SQL Server 的扩展事件或 SQL 事件探查器以及 Analysis Services。

## <a name="how-the-gateway-works"></a>网关的工作原理
用户与连接到本地数据源的元素进行交互时：

1. 云服务创建一个查询以及用于数据源的加密凭据，然后将该查询发送到队列以供网关进行处理。
2. 服务会分析该查询，并将请求推送到 Azure 服务总线。
3. 本地数据网关会针对挂起的请求轮询 Azure 服务总线。
4. 网关会获取查询，对凭据进行解密，然后使用这些凭据连接到数据源。
5. 网关将查询发送到数据源以便执行。
6. 结果会从数据源返回到网关，然后返回到云服务。 服务随后使用结果。

## <a name="troubleshooting"></a>故障排除
### <a name="update-to-the-latest-version"></a>更新到最新版本
如果网关版本过时，则可能会出现很多问题。  良好的常规做法是确保使用最新版本。  如果有一个月或更长时间未更新网关，则可能要考虑安装最新版本的网关，并了解是否可以重现问题。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>错误: 无法将用户添加到组。 (-2147463168 PBIEgwService Performance Log Users )
如果你尝试在不受支持的域控制器上安装网关，则可能会收到此错误。 你需要将网关部署在不是域控制器的计算机上。

## <a name="tools"></a>工具
### <a name="collecting-logs-from-the-gateway-configurator"></a>从网关配置器收集日志
可以为网关收集多个日志。 始终从日志开始！

#### <a name="installer-logs"></a>安装程序日志
`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>配置日志
`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>企业网关服务日志
`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>事件日志
数据管理网关和 PowerBIGateway 日志位于“应用程序和服务日志”下。

### <a name="fiddler-trace"></a>Fiddler 跟踪
[Fiddler](http://www.telerik.com/fiddler) 是 Telerik 提供的一种免费工具，可监视 HTTP 流量。  可以从客户端计算机来回查看 Power BI 服务。 这可能会显示错误和其他相关信息。

## <a name="next-steps"></a>后续步骤
* [创建与逻辑应用之间的本地连接](app-service-logic-gateway-connection.md)
* [企业集成功能](app-service-logic-enterprise-integration-overview.md)
* [逻辑应用连接器](../connectors/apis-list.md)




<!--HONumber=Nov16_HO3-->


