---
title: "安装本地数据网关 - Azure 逻辑应用 | Microsoft 文档"
description: "访问本地数据源之前，请在本地数据源与逻辑应用之间安装用于快速数据传输和加密的本地数据网关"
keywords: "访问数据, 本地, 数据传输, 加密, 数据源"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 06/9/2017
ms.author: LADocs; dimazaid; estfan
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 7122b970c2e4703df9771e8ace4e710399ca3e6c
ms.contentlocale: zh-cn
ms.lasthandoff: 06/13/2017


---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>为 Azure 逻辑应用安装本地数据网关

只有在安装并设置本地数据网关之后，逻辑应用才能访问本地数据源。 该网关充当桥梁，在本地系统与逻辑应用之间提供快速数据传输和加密。 网关通过 Azure 服务总线中继来自加密频道上的本地源的数据。 所有流量最初都是网关代理的安全出站流量。 详细了解[数据网关的工作原理](#gateway-cloud-service)。

网关支持连接到以下本地数据源：

*   BizTalk Server
*   DB2  
*   文件系统
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   仅限适用于 HTTP（而非 HTTPS）的 SharePoint
*   SQL Server
*   Teradata

这些步骤说明了[在网关与逻辑应用之间设置连接](./logic-apps-gateway-connection.md)之前，如何首次安装本地数据网关。 有关受支持连接器的详细信息，请参阅[适用于 Azure 逻辑应用的连接器](https://docs.microsoft.com/azure/connectors/apis-list)。 

若要详细了解适用于其他 Microsoft 服务的数据网关，请参阅以下文章：

*   [Azure 应用程序网关](https://azure.microsoft.com/services/application-gateway/)：[应用程序网关概述](../application-gateway/application-gateway-introduction.md)
*   [Microsoft Power BI 本地数据网关](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow 本地数据网关](https://flow.microsoft.com/documentation/gateway-manage/)

<a name="requirements"></a>
## <a name="requirements"></a>要求

**最低**：

* .NET 4.5 Framework
* 64 位版本的 Windows 7 或 Windows Server 2008 R2（或更高版本）

**建议**：

* 8 核 CPU
* 8 GB 内存
* 64 位版本的 Windows 2012 R2（或更高版本）

**重要注意事项**：

* 只能在本地计算机上安装本地数据网关。
不能在域控制器上安装该网关。

   > [!TIP]
   > 不一定要在数据源所在的同一台计算机上安装网关。 为了尽量降低延迟，可将网关安装在尽可能靠近数据源的位置或同一台计算机上（假设你有相应的权限）。

* 不要在会关闭、休眠或未连接到 Internet 的计算机上安装该网关，因为网关在这种情况下无法运行。 此外，在通过无线网络工作时，网关性能可能会下降。

* 安装期间，必须使用由 Azure Active Directory (Azure AD) 托管的[工作或学校帐户](https://docs.microsoft.com/azure/active-directory/sign-up-organization)（而不是 Microsoft 帐户）登录。 

  后期在 Azure 门户中创建网关资源并将其与网关安装关联时，必须仍使用此工作或学校帐户。 在逻辑应用和本地数据源之间建立连接时，请选中此网关资源。 [为何必须使用 Azure AD 工作或学校帐户？](#why-azure-work-school-account)

  > [!TIP]
  > 如果注册了 Office 365 产品但未提供实际工作电子邮件，登录地址可能类似于 jeff@contoso.onmicrosoft.com。 

* 如果使用版本低于 14.16.6317.4 的安装程序安装了现有网关，则无法通过运行最新的安装程序更改网关位置。 但是，可以使用最新的安装程序来安装改用所需位置的新网关。
  
  如果网关安装程序的版本低于 14.16.6317.4，但尚未安装网关，则你可以下载并使用最新的安装程序。

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>安装数据网关

1.  [在本地计算机上下载并运行网关安装程序](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

2. 查看并接受使用条款和隐私声明。

3. 在本地计算机上指定要将网关安装到的路径。

4. 出现提示时，请使用 Azure 工作或学校帐户而不是 Microsoft 帐户登录。

   ![使用 Azure 工作或学校帐户登录](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. 现在，将安装的网关注册到[网关云服务](#gateway-cloud-service)。 选择“在此计算机上注册新网关”。

   网关云服务可加密和存储数据源凭据与网关详细信息。 
   该服务还会在逻辑应用、本地数据网关与本地数据源之间路由查询及其结果。

6. 为网关安装提供名称。 创建恢复密钥，然后确认恢复密钥。 

   > [!IMPORTANT] 
   > 恢复密钥必须至少包含八个字符。 请务必将该密钥保存在安全位置。 迁移、还原或接管现有网关时，也需要此密钥。

   1. 若要更改网关云服务的默认区域和网关安装使用的 Azure 服务总线，请选择“更改区域”。

      ![更改区域](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      默认区域是与 Azure AD 租户关联的区域。

   2. 在下一个窗格中，打开“选择区域”，选择其他区域。

      ![选择其他区域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      例如，可以选择逻辑应用所在的同一区域，或者选择离本地数据源最近的区域，以便降低延迟。 网关资源和逻辑应用可以有不同的位置。

      > [!IMPORTANT]
      > 安装后无法更改此区域。 此区域还确定并限制可在其中为网关创建 Azure 资源的位置。 因此，在 Azure 中创建网关资源时，请确保资源位置与安装网关期间选择的区域匹配。
      > 
      > 如果以后想要为网关使用不同的区域，必须安装新网关。

   3. 准备就绪后，选择“完成”。

7. 现在，请遵照 Azure 门户中的步骤，以便可以[为网关创建 Azure 资源](../logic-apps/logic-apps-gateway-connection.md)。 

详细了解[数据网关的工作原理](#gateway-cloud-service)。

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>迁移、还原或接管现有网关

若要执行这些任务，必须使用安装网关时指定的恢复密钥。

1. 在计算机的“开始”菜单中，选择“本地数据网关”。

2. 安装程序打开后，使用之前用于安装网关的 Azure 工作或学校帐户登录。

3. 选择“迁移”、“还原”或“接管”现有网关。

4. 提供要迁移、还原或接管的网关的名称和恢复密钥。

<a name="restart-gateway"></a>
## <a name="restart-the-gateway"></a>重新启动网关

网关以 Windows 服务的形式运行。 与其他任何 Windows 服务一样，可以通过多种方式启动和停止该服务。 例如，可以在运行网关的计算机上使用提升的权限打开命令提示符，然后运行以下任一命令：

* 若要启动该服务，请运行以下命令：
  
    `net stop PBIEgwService`

* 若要启动该服务，请运行以下命令：
  
    `net start PBIEgwService`

### <a name="windows-service-account"></a>Windows 服务帐户

本地数据网关设置为将 `NT SERVICE\PBIEgwService` 用作 Windows 服务登录凭据。 默认情况下，在安装网关的计算机中，网关拥有“作为服务登录”权限。

> [!NOTE]
> Windows 服务帐户与用于连接到本地数据源的帐户或用于登录到云服务的 Azure 工作或学校帐户不同。

## <a name="configure-a-firewall-or-proxy"></a>配置防火墙或代理

网关会创建与 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)之间的出站连接。 若要提供网关的代理信息，请参阅[配置代理设置](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)。

若要检查防火墙或代理是否阻止了连接，请确认计算机是否可以实际连接到 Internet 和 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)。 在 PowerShell 提示符下运行以下命令：

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> 该命令只测试网络连接以及与 Azure 服务总线之间的连接。 因此，该命令不会针对网关或者加密和存储凭据的网关云服务执行任何操作。 
>
> 此外，该命令仅适用于 Windows Server 2012 R2 或更高版本，以及 Windows 8.1 或更高版本。 在早期的 OS 版本上，可以使用 Telnet 来测试连接。 详细了解 [Azure 服务总线和混合解决方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

结果应类似于以下示例：

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

如果 **TcpTestSucceeded** 未设置为 **true**，可能是防火墙阻止了连接。 如需详尽信息，请将 **ComputerName** 和 **Port** 值替换为本主题后面[配置端口](#configure-ports)下列出的值。

防火墙可能也会阻止 Azure 服务总线向 Azure 数据中心发起的连接。 如果发生这种情况，请批准（取消阻止）区域中数据中心的所有 IP 地址。 对于这些 IP 地址，可[在此处获取 Azure IP 地址列表](https://www.microsoft.com/download/details.aspx?id=41653)。

## <a name="configure-ports"></a>配置端口

网关与 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)建立出站连接，并在出站端口上通信：TCP 443（默认值）、5671、5672、9350 到 9354。 网关不需要入站端口。 详细了解 [Azure 服务总线和混合解决方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

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

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>数据网关的工作原理

数据网关可以加速和保护逻辑应用、网关云服务与本地数据源之间的通信。 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

当云中的用户与连接到本地数据源的元素交互时：

1. 网关云服务将创建一个查询以及用于数据源的加密凭据，然后将该查询发送到队列以供网关进行处理。

2. 网关云服务分析该查询，并将请求推送到 Azure 服务总线。

3. 本地数据网关会针对挂起的请求轮询 Azure 服务总线。

4. 网关会获取查询，对凭据进行解密，然后使用这些凭据连接到数据源。

5. 网关将查询发送到数据源以便执行。

6. 结果将从数据源发回给网关，然后发送到网关云服务。 网关云服务随后使用结果。

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>常见问题

### <a name="general"></a>常规

**问**：对于云中的数据源（如 SQL Azure），是否需要网关？ <br/>
**答**：不需要。 网关只连接到本地数据源。

**问**：网关是否必须安装在与数据源相同的计算机上？ <br/>
**答**：不需要。 网关使用提供的连接信息连接到数据源。 从这种意义上讲，可将网关视为客户端应用程序。 网关只需能够连接到提供的服务器名称即可。

<a name="why-azure-work-school-account"></a>

**问**：为何必须使用 Azure 工作或学校帐户登录？ <br/>
**答**：安装本地数据网关时只能使用 Azure 工作或学校帐户。 登录帐户存储在由 Azure Active Directory (Azure AD) 管理的租户中。 通常，Azure AD 帐户的用户主体名称 (UPN) 与电子邮件地址匹配。

**问**：凭据存储在何处？ <br/>
**答**：为数据源输入的凭据将会加密，并存储在网关云服务中。 凭据在本地数据网关中解密。

**问**：对于网络带宽是否有要求？ <br/>
**答**：建议为网络连接配置较高的吞吐量。 每个环境是不同的，所发送的数据量会影响效果。 使用 ExpressRoute 可以帮助保证本地与 Azure 数据中心之间的吞吐量级别。
可以借助第三方工具 Azure Speed Test 应用来测量吞吐量。

**问**：从网关运行对数据源的查询时的延迟是多少？ 最佳体系结构是什么？ <br/>
**答**：若要减少网络延迟，请在网关安装在尽可能靠近数据源的位置处。 如果可以在实际数据源上安装网关，这种距离可最大程度降低造成的延迟。 还需考虑数据中心。 例如，如果服务使用美国西部的数据中心，而你在 Azure VM 中托管了 SQL Server，则 Azure VM 也应该位于美国西部。 这种距离可最大程度降低延迟并避免 Azure VM 产生传出费用。

**问**：如何将结果发送回云？ <br/>
**答**：可通过 Azure 服务总线发送结果。

**问**：是否存在从云到网关的入站连接？ <br/>
**答**：不需要。 网关使用与 Azure 服务总线之间的出站连接。

**问**：如果阻止出站连接，会发生什么情况？ 我需要打开什么？ <br/>
**答**：查看网关使用的端口和主机。

**问**：实际 Windows 服务的名称是什么？<br/>
**答**：在“服务”中，网关名为“Power BI 企业网关服务”。

**问**：是否可以使用 Azure Active Directory 帐户运行网关 Windows 服务？ <br/>
**答**：不需要。 该 Windows 服务必须具有有效的 Windows 帐户。 默认情况下，服务使用服务 SID“NT SERVICE\PBIEgwService”来运行。

### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复

**问**：有哪些选项可用于灾难恢复？ <br/>
**答**：可以使用恢复密钥还原或移动网关。 安装网关时，指定恢复密钥。

**问**：恢复密钥的好处是什么？ <br/>
**答**：使用恢复密钥可在发生灾难后迁移或恢复网关设置。

**问**：是否有使用网关实现高可用性方案的计划？ <br/>
**答**：我们正在规划这些方案，但目前没有时间表。

## <a name="troubleshooting"></a>故障排除

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**问**：如何查看正在发送到本地数据源的查询？ <br/>
**答**：可以启用查询跟踪，包括要发送的查询。 请记得在完成故障排除后将查询跟踪改回原始值。 一直保持启用查询跟踪会创建大量的日志。

还可以查看数据源用于跟踪查询的工具。 例如，可以使用 SQL Server 的扩展事件或 SQL 事件探查器以及 Analysis Services。

**问**：网关日志在何处？ <br/>
**答**：请参阅本主题后面的“工具”。

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

