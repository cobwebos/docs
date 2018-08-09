---
title: 安装本地数据网关 - Azure 逻辑应用 | Microsoft 文档
description: 在从逻辑应用访问本地数据之前如何下载并安装本地数据网关
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: yshoukry, LADocs
ms.suite: integration
ms.openlocfilehash: 616e3d81d577fd30e65117ec15c65250d3b3e27e
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503642"
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>为 Azure 逻辑应用安装本地数据网关

在将逻辑应用连接到本地数据源之前，请在本地计算机上下载并安装本地数据网关。 该网关充当一个桥梁，在本地（而不是云中）数据源与逻辑应用之间进行快速的数据传输和加密。 本文介绍如何下载、安装和设置本地数据网关。 

可对其他服务（例如 Power BI、Microsoft Flow、PowerApps 和 Azure Analysis Services）使用相同的网关安装过程。 详细了解[数据网关的工作原理](#gateway-cloud-service)。

<a name="supported-connections"></a>

网关支持 Azure 逻辑应用中以下数据源的[本地连接器](../connectors/apis-list.md#on-premises-connectors)：

*   BizTalk Server 2016
*   文件系统
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP Application Server 
*   SAP Message Server
*   SharePoint Server
*   SQL Server
*   Teradata

有关如何将网关用于其他服务的信息，请参阅以下文章：

* [Microsoft Power BI 本地数据网关](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps 本地数据网关](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow 本地数据网关](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>先决条件

* 具有[Azure 订阅](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer)的[工作或学校帐户](../active-directory/fundamentals/sign-up-organization.md)。 在安装网关期间，需登录到此帐户，以便将网关安装与 Azure 订阅相关联。 稍后在 Azure 门户中为网关安装创建 Azure 资源时，也要使用此帐户。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。

* 下面是本地计算机的要求：

  **最低要求**
  * .NET Framework 4.5.2
  * 64 位版本的 Windows 7 或 Windows Server 2008 R2（或更高版本）

  **建议的要求**
  * 8 核 CPU
  * 8 GB 内存
  * 64 位版本的 Windows Server 2012 R2（或更高版本）

* **重要注意事项**

  * 只能在本地计算机上安装本地数据网关，而不能在域控制器上安装。 但是，不一定要在数据源所在的同一台计算机上安装网关。 此外，所有数据源只需一个网关，因此，无需为每个数据源安装网关。

    > [!TIP]
    > 为了尽量降低延迟，可将网关安装在尽可能靠近数据源的位置或同一台计算机上（假设你有相应的权限）。

  * 在不会关机、休眠或连接到 Internet 的计算机上安装网关。 网关无法在这些条件下运行。 
  此外，在通过无线网络工作时，网关性能可能会下降。

  * 安装期间，只能使用由 Azure Active Directory (Azure AD) 托管的[工作或学校帐户](../active-directory/sign-up-organization.md)（而不是 Microsoft 帐户）登录。 
  此外，请确保此帐户不是 Azure B2B（来宾）帐户。 
  在通过为网关创建 Azure 资源注册网关安装时，必须在 Azure 门户中使用相同的登录帐户。 
  然后，在创建从逻辑应用到本地数据源的连接时，可以选择此网关资源。 
  [为何必须使用 Azure AD 工作或学校帐户？](#why-azure-work-school-account)

  > [!TIP]
  > 如果已注册 Office 365 产品/服务但未提供实际的工作电子邮件，可能会收到以下示例所示的登录地址：`username@domain.onmicrosoft.com` 
  >
  > 若要使用包含 [Visual Studio 标准订阅](https://visualstudio.microsoft.com/vs/pricing/)的 Microsoft 帐户，请先使用 Microsoft 帐户[在 Azure Active Directory 中创建一个目录（租户）](../active-directory/develop/quickstart-create-new-tenant.md)或使用默认目录。 
  > 将具有密码的用户添加到该目录，然后向该用户提供对订阅的访问权限。 
  > 然后在网关安装期间可以使用此用户名和密码登录。

  * 为网关安装选择的区域确定了稍后在 Azure 中通过创建 Azure 资源注册网关的位置。 
  在 Azure 中创建此网关资源时，必须选择网关安装所在的同一位置。 默认区域是管理 Azure 帐户的 Azure AD 租户所在的同一位置，但在安装网关期间可以更改位置。

  * 如果使用版本低于 14.16.6317.4 的安装程序安装了网关，则无法通过运行最新的安装程序更改网关位置。 但是，可以使用最新的安装程序来安装改用所需位置的新网关。
  
    如果网关安装程序的版本低于 14.16.6317.4，但尚未安装网关，则可以下载并改用最新的安装程序。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>安装数据网关

1. [在本地计算机上下载、保存并运行网关安装程序](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

2. 接受默认安装路径，或者在计算机上指定网关的安装位置。

3. 查看并接受使用条款和隐私声明，然后选择“安装”。

   ![接受使用条款和隐私声明](./media/logic-apps-gateway-install/accept-terms.png)

4. 成功安装网关后，提供工作或学校帐户的电子邮件地址，然后选择“登录”。

   ![使用工作或学校帐户登录](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. 选择“在此计算机上注册新网关” > “下一步”，将网关安装注册到[网关云服务](#gateway-cloud-service)。 

   ![注册网关](./media/logic-apps-gateway-install/register-new-gateway.png)

6. 提供网关安装的以下信息：

   * 要对安装使用的名称 

   * 要创建的恢复密钥，必须至少包含八个字符

     > [!IMPORTANT]
     > 请将恢复密钥保存在安全位置。 更改网关的位置或者迁移、恢复或接管现有网关时，需要使用此密钥。

   * 确认恢复密钥 

     ![安装网关](./media/logic-apps-gateway-install/set-up-gateway.png)

7. 检查为网关安装所用的网关云服务和 Azure 服务总线选择的区域。 

   ![检查区域](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > 在安装完网关后，若要更改此区域，需要该网关安装的恢复密钥。 此外，必须卸载并重新安装网关。 有关详细信息，请参阅[更改位置或者迁移、恢复或接管现有网关](#update-gateway-installation)。

   *为何要更改网关安装的区域？* 

   例如，为了降低延迟，可将网关的区域更改为逻辑应用所在的同一区域。 
   或者，可以选择最靠近本地数据源的区域。 
   *Azure 中的网关资源*和逻辑应用可以有不同的位置。

8. 若要接受默认区域，请选择“配置”。 若要更改默认区域，请执行以下步骤：

   1. 在当前区域的旁边，选择“更改区域”。 

      ![更改区域](./media/logic-apps-gateway-install/change-region.png)

   2. 在下一页上打开“选择区域”列表，选择所需的区域，然后选择“完成”。

      ![选择其他区域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. 显示确认页后，选择“关闭”。 

   安装程序会确认网关现已联机，并可供使用。

   ![已完成网关安装](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. 现在，请通过[为网关安装创建 Azure 资源](../logic-apps/logic-apps-gateway-connection.md)，在 Azure 中注册网关。 

## <a name="enable-high-availability"></a>启用高可用性

如果完成了多个网关安装并将其设置为群集，则本地数据网关支持高可用性。 如果在创建另一个网关时已有一个网关，则可以选择性地创建高可用性群集。 这些群集会将网关组织成组，帮助避免单一故障点。 若要使用此功能，请查看以下要求和注意事项：

* 只有一部分连接器支持高可用性，例如文件系统连接器和即将推出的其他连接器。 
     
* 主网关所在的同一个 Azure 订阅中必须至少有一个网关安装，并且你能够提供该安装的恢复密钥。 

* 主网关必须运行网关 2017 年 11 月更新版或更高版本。

满足这些要求后，在创建下一个网关时，请选择“添加到现有网关群集”，选择群集的主网关，然后提供该主网关的恢复密钥。
有关详细信息，请参阅[本地数据网关的高可用性群集](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters)。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>更改位置或者迁移、还原或接管现有网关

如果必须更改网关的位置、将网关安装移到新计算机、恢复已损坏的网关，或接管现有网关的所有权，需要使用安装网关期间提供的恢复密钥。 此操作会断开旧网关的连接。

1. 在计算机上的“控制面板”中，转到“程序和功能”。 在程序列表中，依次选择“本地数据网关”、“卸载”。

2. [重新安装本地数据网关](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

3. 安装程序打开后，使用之前用于安装网关的工作或学校帐户登录。

4. 依次选择“迁移、还原或接管现有网关”、“下一步”。

   ![选择“迁移、还原或接管现有网关”](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. 在“可用网关”或“可用网关群集”下，选择要更改的网关安装。 输入网关安装的恢复密钥。 

   ![选择主网关](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. 若要更改区域，请选择“更改区域”和新区域。

7. 完成后，选择“配置”。

## <a name="configure-proxy-or-firewall"></a>配置代理或防火墙

本地数据网关将与 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)建立出站连接。 如果工作环境要求流量通过代理来访问 Internet，此限制可能会阻止数据网关连接到网关云服务。 若要确定网络是否使用代理，请查看 superuser.com 上的以下文章： 

[How do I know what proxy server I'm using?(SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using)（如何知道我正在使用哪个代理服务器？） 

若要提供网关的代理信息，请参阅[配置代理设置](https://docs.microsoft.com/power-bi/service-gateway-proxy)。 若要检查代理或防火墙是否会阻止连接，请确认计算机是否可以实际连接到 Internet 和 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)。 在 PowerShell 提示符下运行以下命令：

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> 该命令只测试网络连接以及与 Azure 服务总线之间的连接。 该命令不会针对网关或者加密和存储凭据的网关云服务执行任何操作。 
>
> 此外，该命令仅适用于 Windows Server 2012 R2 或更高版本，以及 Windows 8.1 或更高版本。 在早期的 OS 版本上，可以使用 Telnet 来测试连接。 详细了解 [Azure 服务总线和混合解决方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

结果应类似于此示例，其中的 **TcpTestSucceeded** 设置为 **True**：

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

如果 **TcpTestSucceeded** 未设置为 **True**，可能是防火墙阻止了网关。 如需详尽信息，请将 **ComputerName** 和 **Port** 值替换为本文中[配置端口](#configure-ports)下列出的值。

防火墙可能也会阻止 Azure 服务总线向 Azure 数据中心发起的连接。 如果发生这种情况，请批准（取消阻止）区域中数据中心的所有 IP 地址。 对于这些 IP 地址，可[在此处获取 Azure IP 地址列表](https://www.microsoft.com/download/details.aspx?id=41653)。

## <a name="configure-ports"></a>配置端口

网关与 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)建立出站连接，并在出站端口上通信：TCP 443（默认值）、5671、5672、9350 到 9354。 网关不需要入站端口。 详细了解 [Azure 服务总线和混合解决方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

网关使用以下完全限定的域名：

| 域名 | 出站端口 | Description | 
| ------------ | -------------- | ----------- | 
| *.analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | 用于根据配置进行身份验证。 | 
| *.msftncsi.com | 443 | 在 Power BI 服务无法访问网关时用于测试 Internet 连接。 | 
| * .servicebus.windows.net | 443, 9350-9354 | 通过 TCP 的服务总线中继上的侦听器（需要 443 来获取访问控制令牌） | 
| * .servicebus.windows.net | 5671-5672 | 高级消息队列协议 (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

在某些情况下，Azure 服务总线连接是使用 IP 地址而不是完全限定的域名建立的。 因此，我们建议将防火墙中数据区域的 IP 地址加入白名单。 若要将 IP 地址而不是域加入白名单，可以下载并使用 [Microsoft Azure 数据中心 IP 范围列表](https://www.microsoft.com/download/details.aspx?id=41653)。 此列表中的 IP 地址采用[无类域间路由 (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表示法。

### <a name="force-https-communication-with-azure-service-bus"></a>强制与 Azure 服务总线进行 HTTPS 通信

某些代理仅允许流量发往端口 80 和 443。 默认情况下，与 Azure 服务总线之间的通信在除 443 以外的端口上进行。
可以强制网关通过 HTTPS 而不是通过直接 TCP 来与 Azure 服务总线通信，但这可能会显著降低性能。 若要执行此任务，请执行以下步骤：

1. 浏览到本地数据网关客户端所在的位置，通常可在此处找到：```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe```

   若要查找客户端位置，请在同一台计算机上打开服务控制台，找到“本地数据网关服务”，并查看“可执行文件的路径”属性。

2. 打开此配置文件：**Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. 将 **ServiceBusSystemConnectivityModeString** 值从 **AutoDetect** 更改为 **Https**：

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows 服务帐户

本地数据网关以名为“本地数据网关服务”的 Windows 服务形式运行，但对其“登录身份”帐户凭据使用“NT SERVICE\PBIEgwService”。 默认情况下，本地数据网关对安装该网关的计算机拥有“作为服务登录”权限。 若要创建和维护 Azure 门户中的网关，Windows 服务帐户必须至少具有“参与者”权限。 

> [!NOTE]
> Windows 服务帐户与用于连接到本地数据源的帐户或用于登录到云服务的工作或学校帐户不同。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>重启网关

数据网关以 Windows 服务的形式运行，与任何其他 Windows 服务一样，可以通过多种方式启动和停止该网关。 例如，可以在运行网关的计算机上使用提升的权限打开命令提示符，并运行以下任一命令：

* 若要启动该服务，请运行以下命令：
  
  `net stop PBIEgwService`

* 若要启动该服务，请运行以下命令：
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>租户级管理 

目前没有单独的位置可让租户管理员管理其他用户安装和配置的所有网关。 如果你是租户管理员，你可能希望组织中的用户将你以管理员的身份添加到他们安装的每个网关。 这样，你便可以通过“网关设置”页面或通过 [PowerShell 命令](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters)管理组织中的所有网关。 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>网关的工作原理

数据网关可以加速和保护逻辑应用、网关云服务与本地数据源之间的通信。 网关云服务可加密和存储数据源凭据与网关详细信息。 该服务还会在逻辑应用、本地数据网关与本地数据源之间路由查询及其结果。 

网关可与防火墙配合使用，只使用出站连接。 所有流量最初都是网关代理的安全出站流量。 网关通过 Azure 服务总线中继来自加密频道上的本地源的数据。 此服务总线在网关与调用方服务之间创建通道，但不存储任何数据。 通过网关的所有数据经过加密。

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

这些步骤说明当云中用户与连接到本地数据源的元素交互时会发生什么情况：

1. 网关云服务将创建一个查询以及用于数据源的加密凭据，并将该查询发送到队列以供网关进行处理。

2. 网关云服务分析该查询，并将请求推送到 Azure 服务总线。

3. 本地数据网关会针对挂起的请求轮询 Azure 服务总线。

4. 网关会获取查询，对凭据进行解密，并使用这些凭据连接到数据源。

5. 网关将查询发送到数据源以便执行。

6. 结果将从数据源发回给网关，并发送到网关云服务。 网关云服务随后使用结果。

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常见问题

### <a name="general"></a>常规

问：对于云中的数据源（如 Azure SQL 数据库），是否需要网关？ <br/>
**答**：不需要，网关只连接到本地数据源。

**问**：网关是否必须安装在与数据源相同的计算机上？ <br/>
**答**：不是，网关使用提供的连接信息连接到数据源。 从这种意义上讲，可将网关视为客户端应用程序。 网关只需能够连接到提供的服务器名称即可。

<a name="why-azure-work-school-account"></a>

**问**：为何必须使用工作或学校帐户登录？ <br/>
**答**：安装本地数据网关时只能使用工作或学校帐户。 登录帐户存储在由 Azure Active Directory (Azure AD) 管理的租户中。 通常，Azure AD 帐户的用户主体名称 (UPN) 与电子邮件地址匹配。

**问**：凭据存储在何处？ <br/>
**答**：为数据源输入的凭据将会加密，并存储在网关云服务中。 凭据在本地数据网关中解密。

**问**：对于网络带宽是否有要求？ <br/>
**答**：请检查网络连接的吞吐量是否正常。 每个环境是不同的，所发送的数据量可能影响结果。 为了保证本地数据源与 Azure 数据中心之间的吞吐量级别，请尝试 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)。 为了帮助计量吞吐量，请尝试 Azure Speed Test 等外部工具。

**问**：从网关运行对数据源的查询时的延迟是多少？ 最佳体系结构是什么？ <br/>
**答**：若要减少网络延迟，请将网关安装在尽可能靠近数据源的位置。 如果可以在实际数据源上安装网关，这种距离可最大程度降低造成的延迟。 此外，请考虑与 Azure 数据中心的距离。 例如，如果服务使用美国西部的数据中心，而你在 Azure VM 中托管了 SQL Server，则 Azure VM 最好也位于美国西部。 这种距离可最大程度降低延迟并避免 Azure VM 产生传出费用。

**问**：如何将结果发送回云？ <br/>
**答**：可通过 Azure 服务总线发送结果。

**问**：是否存在从云到网关的入站连接？ <br/>
**答**：否。网关使用与 Azure 服务总线之间的出站连接。

**问**：如果阻止出站连接，会发生什么情况？ 我需要打开什么？ <br/>
**答**：查看网关使用的端口和主机。

**问**：实际 Windows 服务的名称是什么？ <br/>
**答**：在任务管理器中的“服务”选项卡上，该服务的名称是“PBIEgwService”或“Power BI Enterprise Gateway Service”。 在“服务”控制台中，该服务的名称为“On-premises data gateway service”。 Windows 服务使用“NT SERVICE\PBIEgwService”作为服务 SID (SSID)。

**问**：是否可以使用 Azure Active Directory 帐户运行网关 Windows 服务？ <br/>
**答**：否。该 Windows 服务必须具有有效的 Windows 帐户。

### <a name="disaster-recovery"></a>灾难恢复

**问**：有哪些选项可用于灾难恢复？ <br/>
**答**：可以使用恢复密钥还原或移动网关。 安装网关时，指定恢复密钥。

**问**：恢复密钥的好处是什么？ <br/>
**答**：使用恢复密钥可在发生灾难后迁移或恢复网关设置。

## <a name="troubleshooting"></a>故障排除

本部分帮助你解决在设置和使用本地数据网关时可能遇到的一些常见问题。

**问：** 网关安装为何失败？ <br/>
**答**：如果目标计算机上的防病毒软件已过时，则可能会发生此问题。 可以更新防病毒软件，或者在安装网关期间禁用防病毒软件，安装后再重新启用该软件。

**问：** 在 Azure 中创建网关资源时为何看不到我的网关安装？ <br/>
**答**：此问题的可能原因如下：

* 网关安装已由 Azure 中的另一个网关资源注册并声明。 为网关安装创建网关资源后，实例列表中不会显示这些网关安装。
若要在 Azure 门户中检查网关注册，请查看所有 Azure 订阅的“本地数据网关”类型的所有 Azure 资源。 

* 网关安装人员的 Azure AD 标识不同于登录 Azure 门户的人员。 请检查你是否使用了用于安装网关的同一标识登录。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**问**：网关日志在何处？ <br/>
**答**：请参阅本文稍后的[“日志”部分](#logs)。

**问**：如何查看正在发送到本地数据源的查询？ <br/>
**答**：可以启用查询跟踪，包括要发送的查询。 请记得在完成故障排除后将查询跟踪改回原始值。 一直保持启用查询跟踪会创建大量的日志。

还可以查看数据源用于跟踪查询的工具。 例如，可以使用 SQL Server 的扩展事件或 SQL 事件探查器以及 Analysis Services。

### <a name="outdated-gateway-version"></a>网关版本已过时

如果网关版本过时，可能会出现很多问题。 良好的常规做法是确保使用最新版本。 如果有一个月或更长时间未更新网关，可能要考虑安装最新版本的网关，并确定是否可以重现问题。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>错误: 无法将用户添加到组。 (-2147463168 PBIEgwService Performance Log Users)

如果尝试在不受支持的域控制器上安装网关，可能会收到此错误。 请确保将网关部署在不是域控制器的计算机上。

<a name="logs"></a>

### <a name="logs"></a>日志

为帮助故障排除，请始终先收集并检查网关日志。 可通过多种方式收集日志，但安装网关后最简单的日志收集方法是使用网关安装程序的用户界面。 

1. 在计算机上打开本地数据网关安装程序。
2. 在左侧菜单中，选择“诊断”。
3. 在“网关日志”下，选择“导出日志”。

   ![从网关安装程序导出日志](./media/logic-apps-gateway-install/export-logs.png)

下面是可以找到各种日志的其他位置：

| 日志类型 | 位置 | 
|----------|----------| 
| **安装程序日志** | %localappdata%\Temp\On-premises_data_gateway_<*yyyymmdd*>.<*编号*>.log | 
| **配置日志** | C:\Users\<*用户名*>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*yyyymmdd*>.<*编号*>.log | 
| **企业网关服务日志** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*yyyymmdd*>.<*编号*>.log | 
||| 

**事件日志**

若要查找网关的事件日志，请执行以下步骤：

1. 在安装网关的计算机上打开“事件查看器”。 
2. 展开“事件查看器(本地)” > “应用程序和服务日志”。 
3. 选择“本地数据网关服务”。

   ![查看网关的事件日志](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="telemetry"></a>遥测

如需其他监视和故障排除信息，可以启用遥测并收集遥测数据。 

1. 浏览到本地数据网关客户端所在的位置，通常可在此处找到：```C:\Program Files\On-premises data gateway```

   若要查找客户端位置，请在同一台计算机上打开服务控制台，找到“本地数据网关服务”，并查看“可执行文件的路径”属性。

2. 打开此配置文件：**Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. 将 **SendTelemetry** 值更改为 **true**：

   ```html
   <setting name="SendTelemetry" serializeAs="String">
      <value>true</value>
   </setting>
   ```

4. 保存更改，然后重启 Windows 服务。

### <a name="review-slow-query-performance"></a>检查查询性能缓慢的问题

如果你发现通过网关的查询运行速度缓慢，可以启用附加日志记录，用于输出查询及其持续时间。 这些日志可能有助于找出哪些查询速度缓慢或长时间运行。 若要优化查询性能，可能需要修改数据源，例如，调整 SQL Server 查询的索引。

若要确定查询的持续时间，请执行以下步骤：

1. 浏览到网关客户端所在的同一位置，通常可此处找到：```C:\Program Files\On-premises data gateway```

   若要查找客户端位置，请在同一台计算机上打开服务控制台，找到“本地数据网关服务”，并查看“可执行文件的路径”属性。

2. 如下所述打开并编辑这些配置文件：

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     在此文件中，将 **EmitQueryTraces** 值从 **false** 更改为 **true**，使网关可以记录从网关发送到数据源的查询：

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > 根据网关的使用情况，启用 EmitQueryTraces 设置可能会显著增大日志大小。 检查完日志后，请确保将 EmitQueryTraces 重置为 **false**，而不要长期保留此设置。

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     若要让网关记录详细条目（包括显示持续时间的条目），请执行以下步骤之下，将**TracingVerbosity** 值从 **4** 更改为 **5**： 

     * 在此配置文件中，将 **TracingVerbosity** 值从 **4** 更改为 **5** 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * 打开网关安装程序，选择“诊断”，启用“附加日志记录”，然后选择“应用”：

       ![启用附加日志记录](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > 根据网关的使用情况，启用 TracingVerbosity 设置可能会显著增大日志大小。 检查完日志后，请确保在网关安装程序中禁用“附加日志记录”，或者在配置文件中将 TracingVerbosity 重置为 **4**，而不要长期保留此设置。

3. 若要找出查询的持续时间，请执行以下步骤：

   1. [导出](#logs)并打开网关日志。

   2. 若要查找某个查询，请搜索活动类型，例如： 

      | 活动类型 | Description | 
      |---------------|-------------| 
      | MGEQ | 通过 ADO.NET 运行的查询。 | 
      | MGEO | 通过 OLEDB 运行的查询。 | 
      | MGEM | 从 Mashup 引擎运行的查询。 | 
      ||| 

   3. 请注意第二个 GUID，它是请求 ID。

   4. 继续搜索活动类型，直到找到名为“FireActivityCompletedSuccessfullyEvent”的条目，其中包含以毫秒为单位的持续时间。 
   确认该条目是否具有相同的请求 ID，例如：

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > “FireActivityCompletedSuccessfullyEvent”条目是一个详细条目，除非“TracingVerbosity”设置处于级别 5，否则不会记录该条目。

### <a name="trace-traffic-with-fiddler"></a>使用 Fiddler 跟踪流量

[Fiddler](http://www.telerik.com/fiddler) 是 Telerik 提供的一种免费工具，可监视 HTTP 流量。 可在客户端计算机中查看 Power BI 服务发生的此流量。 此服务可能会显示错误和其他相关信息。

## <a name="next-steps"></a>后续步骤
    
* [从逻辑应用连接到本地数据](../logic-apps/logic-apps-gateway-connection.md)
* [企业集成功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)
