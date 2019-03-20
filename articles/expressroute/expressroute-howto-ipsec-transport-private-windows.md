---
title: 配置 Windows 主机的 IPsec 传输模式 - 专用对等互连：ExpressRoute：Azure | Microsoft Docs
description: 如何使用 GPO 和 OU 通过 ExpressRoute 专用对等互连在 Azure Windows VM 和本地 Windows 主机之间启用 IPsec 传输模式。
services: expressroute
author: fabferri
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: fabferri
ms.custom: seodec18
ms.openlocfilehash: d728980517988e2dc39be4e4b64d20157a1aef54
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58121173"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>配置 ExpressRoute 专用对等互连的 IPsec 传输模式

本文可帮助在运行 Windows 的 Azure VM 和本地 Windows 主机之间通过 ExpressRoute 专用对等互连在传输模式下创建 IPsec 隧道。 本文中的步骤使用组策略对象创建此配置。 虽然可以在不使用组织单位 (OU) 和组策略对象 (GPO) 的情况下创建此配置，但 OU 和 GPO 的组合有助于简化对安全策略的控制并允许快速进行纵向扩展。 本文中的步骤假定你已具有 Active Directory 配置并且可以熟练使用 OU 和 GPO。

## <a name="about-this-configuration"></a>关于此配置

以下步骤中的配置使用具有 ExpressRoute 专用对等互连的单个 Azure 虚拟网络 (VNet)。 但是，此配置可以跨越多个 Azure VNet 和本地网络。 本文有助于定义 IPsec 加密策略，并将其应用于作为相同 OU 的一部分的 Azure VM 和本地主机的组。 仅针对目标端口为 8080 的 HTTP 流量，在 Auzre VM（vm1 和 vm2）和本地 host1 之间配置加密。 可以根据要求创建不同类型的 IPsec 策略。

### <a name="working-with-ous"></a>使用 OU 

与 OU 关联的安全策略将通过 GPO 推送到计算机。 与向单个主机应用策略相比，使用 OU 的几个优点是：

* 将策略与 OU 关联可保证属于相同 OU 的计算机获取相同的策略。
* 更改与 OU 关联的安全策略会将更改应用到 OU 中的所有主机。

### <a name="diagrams"></a>图表

下图显示了互连和分配的 IP 地址空间。 Azure VM 和本地主机运行的是 Windows 2016。 Azure VM 和本地 host1 是同一个域的一部分。 Azure VM 和本地主机可以使用 DNS 正确地解析名称。

[![1]][1]

此图显示了 ExpressRoute 专用对等互连中正在传输的 IPsec 隧道。

[![4]][4]

### <a name="working-with-ipsec-policy"></a>使用 IPsec 策略

在 Windows 中，加密是与 IPsec 策略相关联的。 IPsec 策略确定哪些 IP 流量是受保护的，以及应用于 IP 数据包的安全机制。
**IPSec 策略**由以下各项组成：“筛选器列表”、“筛选器操作”和“安全规则”。

在配置 IPsec 策略时，请务必了解以下 IPsec 策略术语：

* **IPsec 策略：** 规则的集合。 在任意特定时间只能有一个策略处于活跃状态（“已分配”）。 每个策略可以有一个或多个规则，所有规则可同时启用。 一台计算机在给定的时间只能分配有一个活跃的 IPsec 策略。 但是，在 IPsec 策略内，可以定义在不同情况下可能采取的多个操作。 每个 IPsec 规则集与一个筛选器列表相关联，该列表影响规则所应用到的网络流量类型。

* **筛选器列表：** 筛选器列表是一个或多个筛选器的捆绑包。 一个列表可包含多个筛选器。 根据 IP 地址范围、协议或特定协议端口，筛选器定义通信是受到允许、受保护还是受到阻止。 每个筛选器匹配一组特定的条件；例如，从特定子网发送到特定计算机上特定目标端口的数据包。 当网络条件匹配这些筛选器中的一个或多个时，筛选器列表便被激活。 每个筛选器在特定筛选器列表内进行定义。 筛选器不能在筛选器列表之间共享。 但是，给定的筛选器列表可并入多个 IPsec 策略中。 

* **筛选器操作：** 安全方法定义在 IKE 协议期间计算机提供的一组安全算法、协议和密钥。 筛选器操作是按优先顺序排列的安全方法列表。  在计算机协商 IPsec 会话时，它根据筛选器操作列表中存储的安全设置接受或发送方案。

* **安全规则：** 规则控制 IPsec 策略如何及何时保护通信。 它使用筛选器列表和筛选器操作来创建 IPsec 规则以建立 IPsec 连接。 每个策略可以有一个或多个规则，所有规则可同时启用。 每个规则包含一个 IP 筛选器列表和一个在出现与该筛选器列表的匹配时发生的安全操作集合：
  * IP 筛选器操作
  * 身份验证方法
  * IP 隧道设置
  * 连接类型

[![5]][5]

## <a name="before-you-begin"></a>开始之前

确保符合以下先决条件：

* 必须拥有可用来实现组策略设置的正常运行的 Active Directory 配置。 有关 GPO 的详细信息，请参阅[组策略对象](https://msdn.microsoft.com/library/windows/desktop/aa374162(v=vs.85).aspx)。

* 必须有一个活动的 ExpressRoute 线路。
  * 有关创建 ExpressRoute 线路的详细信息，请参阅[创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)。 
  * 确认线路由连接提供程序启用。 
  * 确认已为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](expressroute-howto-routing-arm.md)一文。 
  * 确认已创建并完全预配一个 VNet 和一个虚拟网络网关。 按照说明[创建 ExpressRoute 的虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 虚拟网络网关使用的 GatewayType 是“ExpressRoute”而非 VPN。

* ExpressRoute 虚拟网络网关必须连接到 ExpressRoute 线路。 有关详细信息，请参阅[将 VNet 连接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)。

* 确认 Azure Windows VM 部署到 VNet。

* 确认在本地主机和 Azure VM 之间存在连接。

* 确认 Azure Windows VM 和本地主机能够使用 DNS 正确解析名称。

### <a name="workflow"></a>工作流

1. 创建 GPO 并将其关联到 OU。
2. 定义 IPsec 筛选器操作。
3. 定义 IPsec 筛选器列表。
4. 创建具有安全规则的 IPsec 策略。
5. 将 IPsec GPO 分配到 OU。

### <a name="example-values"></a>示例值

* **域名：** ipsectest.com

* **OU：** IPSecOU

* **本地 Windows 计算机：** host1

* **Azure Windows VM：** vm1、vm2

## <a name="creategpo"></a>1.创建 GPO

1. 若要创建一个新的链接到 OU 的 GPO，请打开“组策略管理”管理单元，并找到 GPO 将链接到的 OU。 在示例中，OU 名为“IPSecOU”。 

   [![9]][9]
2. 在“组策略管理”管理单元中，选择 OU 并右键单击。 在下拉菜单中，单击“在此域中创建 GPO 并将其链接到此处...”。

   [![10]][10]
3. 为 GPO 取一个直观的名称，以便可在之后轻松找到它。 单击“确定”以创建并链接 GPO。

   [![11]][11]

## <a name="enablelink"></a>2.启用 GPO 链接

若要将 GPO 应用到 OU，不仅该 GPO 必须链接到 OU，还必须启用启用该链接。

1. 找到创建的 GPO，右键单击，然后从下拉菜单中选择“编辑”。
2. 若要将 GPO 应用于 OU，请选择“已启用链接”。

   [![12]][12]

## <a name="filteraction"></a>3.定义 IP 筛选器操作

1. 从下拉菜单中，右键单击“Active Directory 上的 IP 安全策略”，然后单击“管理 IP 筛选器列表和筛选器操作...”。

   [![15]][15]
2. 在“管理筛选器操作”选项卡上，单击“添加”。

   [![16]][16]

3. 在“IP 安全筛选器操作向导”上，单击“下一步”。

   [![17]][17]
4. 为筛选器操作取一个直观的名称，以便可在之后找到它。 在本示例中，筛选器操作名为“myEncryption”。 还可以添加说明。 然后单击“下一步”。

   [![18]][18]
5. “协商安全性”允许定义在 IPsec 无法与另一台计算机成功建立时的行为。 选择“协商安全性”，然后单击“下一步”。

   [![19]][19]
6. 在“与不支持 IPsec 的计算机通信”页上，选择“不允许不安全的通信”，然后单击“下一步”。

   [![20]][20]
7. 在“IP 流量和安全性”页上，选择“自定义”，然后单击“设置...”。

   [![21]][21]
8. 在“自定义安全方法设置”页上，选择“数据完整性和加密 (ESP)**：SHA1、3DES”**。 然后单击“确定”。

   [![22]][22]
9. 在“管理筛选器操作”页上，可以看到“myEncryption”筛选器已成功添加。 单击“关闭”。

   [![23]][23]

## <a name="filterlist1"></a>4.定义 IP 筛选器列表

创建指定目标端口为 8080 的已加密 HTTP 流量的筛选器列表。

1. 若要限定必须加密哪些类型的流量，请使用 IP 筛选器列表。 在“管理 IP 筛选器列表”选项卡上，单击“添加”以添加新的 IP 筛选器列表。

   [![24]][24]
2. 在“名称”字段中，键入 IP 筛选器列表的名称。 例如，“azure-onpremises-HTTP8080”。 然后，单击“添加”。

   [![25]][25]
3. 在“IP 筛选器描述和镜像属性”页上，选择“镜像”。 镜像设置匹配两个方向的数据包，这使得可以进行双向通信。 然后单击“下一步”。

   [![26]][26]
4. 在“IP 流量源”页上，从“源地址:”下拉菜单中，选择“特定的 IP 地址或子网”。 

   [![27]][27]
5. 指定 IP 流量的“IP 地址或子网:”，然后单击“下一步”。

   [![28]][28]
6. 指定“目标地址:”IP 地址或子网。 然后单击“下一步”。

   [![29]][29]
7. 在“IP 协议类型”页上，选择“TCP”。 然后单击“下一步”。

   [![30]][30]
8. 在“IP 协议端口”页上，选择“从任意端口”和“至此端口:”。 在文本框中键入“8080”。 这些设置指定仅目标端口 8080 上的 HTTP 流量将被加密。 然后单击“下一步”。

   [![31]][31]
9. 查看 IP 筛选器列表。  IP 筛选器列表“azure-onpremises-HTTP8080”的配置为所有匹配以下条件的流量触发加密：

   * 10.0.1.0/24 (Azure Subnet2) 中的任意源地址
   * 10.2.27.0/25（本地子网）中的任意目标地址
   * TCP 协议
   * 目标端口 8080

   [![32]][32]

## <a name="filterlist2"></a>5.编辑 IP 筛选器列表

若要加密相反方向（从本地主机到 Azure VM）的同一类型的流量，需要第二个 IP 筛选器。 设置新筛选器的过程与用于设置第一个 IP 筛选器的过程相同。 唯一的区别是源子网和目标子网。

1. 若要将新的 IP 筛选器添加到 IP 筛选器列表中，请选择“编辑”。

   [![33]][33]
2. 在“IP 筛选器列表”页上，单击“添加”。

   [![34]][34]
3. 使用以下示例中的设置创建第二个 IP 筛选器：

   [![35]][35]
4. 创建第二个 IP 筛选器后，IP 筛选器列表将如下所示：

   [![36]][36]

如果在本地位置和 Azure 子网之间需要加密来保护应用程序，可以添加新的 IP 筛选器列表，而非修改现有 IP 筛选器列表。 将 2 个 IP 筛选器列表关联到相同的 IPsec 策略可提供更好的灵活性，因为可以在不影响另一个 IP 筛选器列表的情况下，随时修改或删除特定 IP 筛选器列表。

## <a name="ipsecpolicy"></a>6.创建 IPsec 安全策略 

创建具有安全规则的 IPsec 策略

1. 选择与 OU 相关联的“Active Directory 上的 IPSecurity 策略”。 右键单击，然后选择“创建 IP 安全策略”。

   [![37]][37]
2. 命名安全策略。 例如，“policy-azure-onpremises”。 然后单击“下一步”。

   [![38]][38]
3. 单击“下一步”，无需选中复选框。

   [![39]][39]
4. 确认已选中“编辑属性”复选框，然后单击“完成”。

   [![40]][40]

## <a name="editipsec"></a>7.编辑 IPsec 安全策略

将 IPsec 策略添加到之前配置的 IP 筛选器列表和筛选器操作。

1. 在 HTTP 策略属性“规则”选项卡上，单击“添加”。

   [![41]][41]
2. 在“欢迎”页面上，单击“**下一步**”。

   [![42]][42]
3. 规则提供选项来定义 IPsec 模式：隧道模式或传输模式。

   * 在隧道模式下，原始数据包由一组 IP 标头封装。 隧道模式通过加密原始数据包的 IP 标头来保护内部路由信息。 隧道模式在站点到站点 VPN 场景中的网关之间广泛实现。 隧道模式在大多数情况下用于主机之间的端到端加密。

   * 传输模式仅加密有效负载和 ESP 尾部；不会加密原始数据包的 IP 标头。 在传输模式下，数据包的 IP 源和 IP 目标不变。

   选择“此规则不指定隧道”，然后单击“下一步”。

   [![43]][43]
4. “网络类型”定义哪些网络连接与安全策略关联。 选择“所有网络连接”，然后单击“下一步”。

   [![44]][44]
5. 选择之前创建的 IP 筛选器列表“azure-onpremises-HTTP8080”，然后单击“下一步”。

   [![45]][45]
6. 选择之前创建的现有筛选器操作“myEncryption”。

   [![46]][46]
7. Windows 支持四种不同类型的身份验证：Kerberos、证书、NTLMv2 和预共享密钥。 由于我们使用的是已加入域的主机，因此请选择“Active Directory 默认(Kerberos V5 协议)”，然后单击“下一步”。

   [![47]][47]
8. 新的策略会创建安全规则：“azure-onpremises-HTTP8080”。 单击“确定”。

   [![48]][48]

IPsec 策略要求目标端口 8080 上的所有 HTTP 连接使用 IPsec 传输模式。 由于 HTTP 是明文协议，因此启用安全策略可确保数据通过 ExpressRoute 专用对等互连传输时处于加密状态。 Active Directory 的 IP 安全策略比高级安全 Windows 防火墙配置起来更为复杂，但是它的确可允许对 IPsec 连接进行更多自定义。

## <a name="assigngpo"></a>8.将 IPsec GPO 分配到 OU

1. 查看策略。 安全组策略已定义，但尚未分配。

   [![49]][49]
2. 要将安全组策略分配到 OU **IPSecOU**，请右键单击安全策略并选择“分配”。
   属于 OU 的每个计算机都将分配有该安全组策略。

   [![50]][50]

## <a name="checktraffic"></a>检查流量加密

若要查看应用于 OU 的加密 GPO，请在所有 Azure VM 上和 host1 中安装 IIS。 每个 IIS 均自定义为答复端口 8080 上的 HTTP 请求。
若要验证加密，可以在 OU 中的所有计算机中安装网络探查器（如 Wireshark）。
Powershell 脚本充当 HTTP 客户端以在端口 8080 上生成 HTTP 请求：

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
以下网络捕获显示本地 host1 的结果，并显示仅匹配已加密流量的筛选器 ESP：

[![51]][51]

如果运行本地 Powershell 脚本（HTTP 客户端），Azure VM 中的网络捕获将显示类似的跟踪。

## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "通过 ExpressRoute 的 IPsec 传输模式网络图"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "IPsec 需关注的流量"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPsec 策略"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "组策略中的组织单位"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "创建与 OU 相关联的 GPO"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "为与 OU 关联的 GPO 命名"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "编辑 GPO"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "管理 IP 筛选器列表和筛选器操作"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "添加筛选器操作"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "操作向导"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "筛选器操作名称"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "筛选器操作"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "指定建立了不安全的连接行为"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "安全机制"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "自定义安全方法"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "筛选器操作列表"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "添加新的 IP 筛选器列表"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "将 HTTP 流量添加到 IP 筛选器"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "匹配两个方向的数据包"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "源子网的选择"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "源网络"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "目标网络"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "协议"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "源端口和目标端口"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "筛选器列表"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "具有 HTTP 流量的 IP 筛选器列表"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "添加第二个 IP 筛选器"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP 筛选器列表的第二个条目"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP 筛选器列表的第二个条目"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "创建 IP 安全策略"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "IPsec 策略的名称"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPsec 策略向导"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "编辑 IPsec 策略"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "将新的安全规则添加到 IPsec 策略"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "创建新的安全规则"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "传输模式"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "网络类型"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "现有 IP 筛选器列表的选择"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "现有筛选器操作的选择"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "身份验证方法的选择"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "结束创建安全策略"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "链接到 GPO 但未分配的 IPsec 策略"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "分配到 GPO 的 IPsec 策略"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "IPsec 已加密流量的捕获"
