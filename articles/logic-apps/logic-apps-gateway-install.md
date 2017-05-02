---
title: "安装本地数据网关 - Azure 逻辑应用 | Microsoft 文档"
description: "通过安装本地数据网关，从逻辑应用访问本地数据"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
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
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: b9971117d5f61669a5161a28c96b11b2fd600b61
ms.lasthandoff: 04/14/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>为 Azure 逻辑应用安装本地数据网关

本地数据网关支持以下连接：

*   BizTalk Server
*   DB2  
*   文件系统
*   Informix
*   MQ
*   MySQL
*   Oracle Database 
*   SAP Application Server 
*   SAP Message Server
*   仅限适用于 HTTP（而非 HTTPS）的 SharePoint
*   SQL Server
*   Teradata

有关这些连接的详细信息，请参阅 [Connectors for Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)（适用于 Azure 逻辑应用的连接器）。

## <a name="installation-and-configuration"></a>安装和配置

### <a name="requirements"></a>要求

最低：

* .NET 4.5 Framework
* 64 位版本的 Windows 7 或 Windows Server 2008 R2（或更高版本）

建议：

* 8 核 CPU
* 8 GB 内存
* 64 位版本的 Windows 2012 R2（或更高版本）

相关注意事项：

* 只能在本地计算机上安装本地数据网关。
不能在域控制器上安装该网关。

* 不要在可能会关闭、休眠或未连接到 Internet 的计算机上安装该网关，因为网关在这种情况下无法运行。 此外，在通过无线网络工作时，网关性能可能会下降。

* 只能在 Azure 中使用工作或学校电子邮件地址，以便将本地数据网关与基于 Azure Active Directory 的帐户相关联。

    如果使用 Microsoft 帐户（例如 @outlook.com），可以使用 Azure 帐户  [创建工作或学校电子邮件地址](../virtual-machines/windows/create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)。

### <a name="install-the-gateway"></a>安装网关

1.    [从此处下载本地数据网关安装程序](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

2.    指定“本地数据网关”作为模式。

3. 使用 Azure 工作或学校帐户登录。 

4. 设置新网关，或者迁移、还原或接管现有网关。

    若要配置网关，请提供网关的名称和恢复密钥，然后选择“配置”。
  
    指定至少包含八个字符的恢复密钥，并将它保存在安全位置。 迁移、还原或接管网关时，需要此密钥。

    若要迁移、还原或接管现有网关，请提供创建网关时指定的恢复密钥。

### <a name="restart-the-gateway"></a>重新启动网关

网关以 Windows 服务的形式运行，与任何其他 Windows 服务一样，可以通过多种方式启动和停止该服务。 例如，可以在运行网关的计算机上使用提升的权限打开命令提示符，然后运行以下任一命令：

* 若要启动该服务，请运行以下命令：
  
    `net stop PBIEgwService`

* 若要启动该服务，请运行以下命令：
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>配置防火墙或代理

若要提供网关的代理信息，请参阅[配置代理设置](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)。

可以通过从 PowerShell 提示符运行以下命令，来验证防火墙或代理是否可能在阻止连接。 此命令将测试与 Azure 服务总线之间的连接。该命令只是测试网络连接，不会与云服务器服务或网关有任何交互。 此测试有助于确定计算机是否可以实际连接到 Internet。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

结果应类似于以下示例。 如果 **TcpTestSucceeded** 不为 true，可能是防火墙阻止了连接。

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

如需详尽信息，请将 **ComputerName** 和 **Port** 值替换为本主题后面[配置端口](#configure-ports)下列出的值。

防火墙可能也会阻止 Azure 服务总线向 Azure 数据中心发起的连接。 如果是这样，请批准（取消阻止）区域中数据中心的所有 IP 地址。
可以[在此处获取 Azure IP 地址](https://www.microsoft.com/download/details.aspx?id=41653)的列表。

### <a name="configure-ports"></a>配置端口
网关与 Azure 服务总线建立出站连接，并在出站端口上通信：TCP 443（默认值）、5671、5672、9350 到 9354。 网关不需要入站端口。

了解有关[混合解决方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)的详细信息。

| 域名 | 出站端口 | 说明 |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | 高级消息队列协议 (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | 通过 TCP 的服务总线中继上的侦听器（需要 443 来获取访问控制令牌） | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | 在 Power BI 服务无法访问网关时用于测试 Internet 连接。 | 

如果需要批准 IP 地址而不是域，可以下载并使用 [Microsoft Azure 数据中心 IP 范围列表](https://www.microsoft.com/download/details.aspx?id=41653)。 在某些情况下，Azure 服务总线连接是使用 IP 地址而不是完全限定的域名建立的。

### <a name="sign-in-accounts"></a>登录帐户

可以使用工作或学校帐户（组织帐户）登录。 如果注册了 Office 365 产品但未提供实际工作电子邮件，登录地址可能类似于 jeff@contoso.onmicrosoft.com。 云服务中的帐户存储在 Azure Active Directory (Azure AD) 中的租户内。 通常，Azure AD 帐户的 UPN 与电子邮件地址匹配。

### <a name="windows-service-account"></a>Windows 服务帐户

本地数据网关设置为将 NT SERVICE\PBIEgwService 用作 Windows 服务登录凭据。 默认情况下，在安装网关的计算机上下文中，网关拥有“作为服务登录”的权限。

此服务帐户与用于连接到本地数据源的帐户或用于登录到云服务的工作或学校帐户不同。

## <a name="how-the-gateway-works"></a>网关的工作原理
当其他人与连接到本地数据源的元素交互时：

1. 云服务创建一个查询以及用于数据源的加密凭据，然后将该查询发送到队列以供网关进行处理。
2. 服务会分析该查询，并将请求推送到 Azure 服务总线。
3. 本地数据网关会针对挂起的请求轮询 Azure 服务总线。
4. 网关会获取查询，对凭据进行解密，然后使用这些凭据连接到数据源。
5. 网关将查询发送到数据源以便执行。
6. 结果将从数据源发回给网关，然后发送到云服务。 服务随后使用结果。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="general"></a>常规

**问题**：对于云中的数据源（如 SQL Azure），我是否需要网关？ <br/>
**答案**：否。 网关只连接到本地数据源。

**问题**：实际 Windows 服务的名称是什么？<br/>
**答案**：在“服务”中，网关名为“Power BI 企业网关服务”。

**问题**：是否存在从云到网关的入站连接？ <br/>
**答案**：否。 网关使用与 Azure 服务总线之间的出站连接。

**问题**：如果我阻止出站连接，会发生什么情况？ 我需要打开什么？ <br/>
**答案**：查看网关使用的端口和主机。

**问题**：网关是否必须安装在与数据源相同的计算机上？ <br/>
**答案**：否。 网关使用提供的连接信息连接到数据源。 从这种意义上讲，可将网关视为客户端应用程序。 网关只需能够连接到提供的服务器名称即可。

**问题**：从网关运行对数据源的查询时的延迟是多少？ 最佳体系结构是什么？ <br/>
**答案**：若要减少网络延迟，请在网关安装在尽可能接近数据源的位置处。 如果可以在实际数据源上安装网关，这种距离可最大程度降低造成的延迟。 还需考虑数据中心。 例如，如果服务使用美国西部的数据中心，而你在 Azure VM 中托管了 SQL Server，则 Azure VM 也应该位于美国西部。 这种距离可最大程度降低延迟并避免 Azure VM 产生传出费用。

**问题**：对于网络带宽是否有要求？ <br/>
**答案**：建议为网络连接配置较高的吞吐量。 每个环境是不同的，所发送的数据量会影响效果。 使用 ExpressRoute 可以帮助保证本地与 Azure 数据中心之间的吞吐量级别。

可以借助第三方工具 Azure Speed Test 应用来测量吞吐量。

**问题**：是否可以使用 Azure Active Directory 帐户运行网关 Windows 服务？ <br/>
**答案**：否。 该 Windows 服务必须具有有效的 Windows 帐户。 默认情况下，服务使用服务 SID“NT SERVICE\PBIEgwService”来运行。

**问题**：如何将结果发送回云？ <br/>
**答案**：可通过 Azure 服务总线发送结果。

**问题**：我的凭据存储在何处？ <br/>
**答案**：为数据源输入的凭据将会加密，并存储在网关云服务中。 凭据在本地网关中解密。

### <a name="high-availabilitydisaster-recovery"></a>高可用性/灾难恢复
**问题**：是否有使用网关实现高可用性方案的计划？ <br/>
**答案**：我们正在规划这些方案，但目前没有时间表。

**问题**：有哪些选项可用于灾难恢复？ <br/>
**答案**：可以使用恢复密钥还原或移动网关。 安装网关时，指定恢复密钥。

**问题**：恢复密钥的好处是什么？ <br/>
**答案**：使用恢复密钥可在发生灾难后迁移或恢复网关设置。

## <a name="troubleshooting"></a>故障排除

**问题**：网关日志在何处？ <br/>
**答案**：请参阅本主题后面的“工具”。

**问题**：如何查看正在发送到本地数据源的查询？ <br/>
**答案**：可以启用查询跟踪，包括要发送的查询。 请记得在完成故障排除后将查询跟踪改回原始值。 一直保持启用查询跟踪会创建大量的日志。

还可以查看数据源用于跟踪查询的工具。 例如，可以使用 SQL Server 的扩展事件或 SQL 事件探查器以及 Analysis Services。

### <a name="update-to-the-latest-version"></a>更新到最新版本

如果网关版本过时，可能会出现很多问题。 良好的常规做法是确保使用最新版本。 如果有一个月或更长时间未更新网关，可能要考虑安装最新版本的网关，并确定是否可以重现问题。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>错误: 无法将用户添加到组。 (-2147463168 PBIEgwService Performance Log Users)

如果尝试在不受支持的域控制器上安装网关，可能会收到此错误。 请确保将网关部署在不是域控制器的计算机上。

## <a name="tools"></a>工具
### <a name="collect-logs-from-the-gateway-configurer"></a>从网关配置器收集日志

可以为网关收集多个日志。 始终从日志开始！

#### <a name="installer-logs"></a>安装程序日志

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>配置日志

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>企业网关服务日志

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>事件日志

可在“应用程序和服务日志”下找到数据管理网关和 PowerBIGateway 日志。

### <a name="fiddler-trace"></a>Fiddler 跟踪

[Fiddler](http://www.telerik.com/fiddler) 是 Telerik 提供的一种免费工具，可监视 HTTP 流量。 可在客户端计算机中查看 Power BI 服务发生的此流量。 此服务可能会显示错误和其他相关信息。

## <a name="next-steps"></a>后续步骤
    
* [从逻辑应用连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)
* [企业集成功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)

