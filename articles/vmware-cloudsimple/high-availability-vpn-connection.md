---
title: Azure VMware 解决方案（按云简单 ） - 配置从本地到云简单 VPN 网关的高可用性
description: 描述如何配置高可用性连接，从本地环境到为高可用性启用的 CloudSimple VPN 网关
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025259"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>配置从本地到云简单 VPN 网关的高可用性连接

网络管理员可以从其本地环境配置高可用性 IPsec 站点到站点 VPN 连接，到 CloudSimple VPN 网关。

本指南介绍了为 IPsec 站点到站点 VPN 高可用性连接配置本地防火墙的步骤。 详细步骤特定于本地防火墙的类型。 例如，本指南介绍了两种类型的防火墙的步骤：Cisco ASA 和帕洛阿尔托网络。

## <a name="before-you-begin"></a>开始之前

在配置本地防火墙之前，请完成以下任务。

1. 验证您的组织是否[预配了](create-nodes.md)所需的节点并创建了至少一个云简单私有云。
2. 在本地网络和云简单私有云之间[配置站点到站点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。

有关支持的阶段 1 和阶段 2 建议，请参阅[VPN 网关概述](cloudsimple-vpn-gateways.md)。

## <a name="configure-on-premises-cisco-asa-firewall"></a>配置本地思科 ASA 防火墙

本节中的说明适用于 Cisco ASA 版本 8.4 及更高版本。 在配置示例中，思科自适应安全设备软件版本 9.10 在 IKEv1 模式下部署和配置。

要使站点到站点 VPN 正常工作，您必须允许本地 Cisco ASA VPN 网关外部接口上的 CloudSimple 主和辅助公共 IP（对等 IP）中的 UDP 500/4500 和 ESP（IP 协议 50）。

### <a name="1-configure-phase-1-ikev1"></a>1. 配置第 1 阶段 （IKEv1）

要在外部接口上启用阶段 1 （IKEv1），请在 Cisco ASA 防火墙中输入以下 CLI 命令。

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. 创建 IKEv1 策略

创建 IKEv1 策略，该策略定义用于哈希、身份验证、迪菲-赫尔曼组、生存期和加密的算法和方法。

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. 创建隧道组

在 IPsec 属性下创建隧道组。 配置对等 IP 地址和隧道预共享密钥，在[配置站点到站点 VPN 网关](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)时设置该密钥。

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. 配置阶段 2（IPsec）

要配置阶段 2 （IPsec），请创建一个访问控制列表 （ACL），该列表定义要加密和隧道的流量。 在下面的示例中，感兴趣的流量来自从本地本地本地子网 （10.16.1.0/24） 到私有云远程子网 （192.168.0/24） 的隧道。 如果站点之间有多个子网，则 ACL 可以包含多个条目。

在 Cisco ASA 版本 8.4 及更高版本中，可以创建对象或对象组，作为网络、子网、主机 IP 地址或多个对象的容器。 为本地网创建对象，为远程子网创建对象，并将其用于加密 ACL 和 NAT 语句。

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>将本地本地本地子网定义为对象

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>将云简单远程子网定义为对象

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>为感兴趣的流量配置访问列表

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. 配置转换集

配置转换集 （TS），该转换集必须涉及```ikev1```关键字 。 TS 中指定的加密和哈希属性必须与[CloudSimple VPN 网关的默认配置](cloudsimple-vpn-gateways.md)中列出的参数匹配。

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. 配置加密映射

配置包含以下组件的加密映射：

* 对等 IP 地址
* 包含感兴趣流量的已定义 ACL
* 转换集

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. 应用加密映射

在外部接口上应用加密映射：

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. 确认适用的 NAT 规则

以下是使用的 NAT 规则。 确保 VPN 流量不受任何其他 NAT 规则的影响。

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>思科 ASA 建立的 IPsec 站点到站点 VPN 示例输出

第 1 阶段输出：

![Cisco ASA 防火墙的第 1 阶段输出](media/ha-vpn-connection-cisco-phase1.png)

第 2 阶段输出：

![Cisco ASA 防火墙的第 2 阶段输出](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>配置本地帕洛阿尔托网络防火墙

本节中的说明适用于帕洛阿尔托网络版本 7.1 及更高版本。 在此配置示例中，帕洛阿尔托网络 VM 系列软件版本 8.1.0 在 IKEv1 模式下部署和配置。

要使站点到站点 VPN 正常工作，您必须允许本地帕洛阿尔托网络网关外部接口上的 CloudSimple 主和辅助公共 IP（对等 IP）中的 UDP 500/4500 和 ESP（IP 协议 50）。

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. 创建主隧道和辅助隧道接口

登录到帕洛阿尔托防火墙，选择**网络** > **接口** > **隧道** > **添加**，配置以下字段，然后单击 **"确定**"。

* 接口名称。 第一个字段使用关键字"隧道"自动填充。 在相邻字段中，输入介于 1 到 9999 之间的任意数字。 此接口将用作主隧道接口，用于在本地数据中心和私有云之间传输站点到站点的流量。
* 备注。 输入注释，以便轻松识别隧道的用途
* 净流量配置文件。 保留默认值。
* 配置。将接口分配给：虚拟路由器：选择**默认**。 
        安全区域：为受信任的 LAN 流量选择区域。 在此示例中，LAN 流量区域的名称为"信任"。
* IPv4。 单击"**添加并**添加环境中任何非重叠未使用的 /32 ip 地址"，该地址将分配给主隧道接口，并将用于监视隧道（稍后说明）。

由于此配置适用于高可用性 VPN，因此需要两个隧道接口：一个主接口和一个辅助接口。 重复前面的步骤以创建辅助隧道接口。 选择其他隧道 ID 和未使用 /32 ip 地址。

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. 为通过站点到站点 VPN 到达的私有云子网设置静态路由

本地子网到达云简单专用云子网时，必须路由。

选择**网络** > **虚拟路由器** > *默认* > **静态路由** > **添加**，配置以下字段，然后单击 **"确定**"。

* 名称： 输入任何名称，以便轻松识别路线的用途。
* 目标。 指定从本地通过 S2S 隧道接口到达的云简单私有云子网
* 接口。 从下拉列表中选择步骤 1（第 2 节）中创建的主隧道接口。 在此示例中，它是隧道。
* 下一跳。 选择 **"无**"。
* 管理员距离。 保留默认值。
* 跃点数。 输入从 1 到 65535 的任何值。 关键是输入与主隧道接口对应的路由的较低指标，而与路由对应的辅助隧道接口相比，使前一个路由首选。 如果 tunnel.20 的公制值为 20，而隧道的公制值为 30，则最好使用隧道.20。
* 路由表。 保留默认值。
* BFD 配置文件。 保留默认值。
* 路径监视。 保持未选中状态。

重复上述步骤，为私有云子网创建另一个路由，以便通过辅助隧道接口用作辅助/备份路由。 这一次，选择不同的隧道 ID 和比主路由更高的指标。

### <a name="3-define-the-cryptographic-profile"></a>3. 定义加密配置文件

定义一个加密配置文件，指定用于在 IKEv1 阶段 1 中设置 VPN 隧道的标识、身份验证和加密的协议和算法。

选择**网络** > **扩展网络配置文件** > **IKE 加密** > **添加**，配置以下字段，然后单击 **"确定**"。

* 名称： 输入 IKE 加密配置文件的任何名称。
* DH集团。 单击"**添加**"并选择相应的 DH 组。
* 加密。 单击"**添加"** 并选择适当的加密方法。
* 身份验证。 单击"**添加**"并选择适当的身份验证方法。
* 密钥生存期。 保留默认值。
* IKEv2 身份验证倍数。 保留默认值。

### <a name="4-define-ike-gateways"></a>4. 定义 IKE 网关

定义 IKE 网关以在 VPN 隧道的每一端建立对等方之间的通信。

选择**网络** > **扩展网络配置文件** > **IKE 网关** > **添加**，配置以下字段，然后单击 **"确定**"。

常规选项卡：

* 名称： 输入要与主 CloudSimple VPN 对等体对等的 IKE 网关的名称。
* 版本。 选择**仅 IKEv1 模式**。
* 地址类型。 选择“IPv4”。****
* 接口。 选择面向公共的或外部接口。
* 本地 IP 地址。 保留默认值。
* 对等 IP 地址类型。 选择**IP**。
* 对等地址。 输入主云简单 VPN 对等 IP 地址。
* 身份验证。 选择**预共享密钥**。
* 预共享密钥/确认预共享密钥。 输入预共享密钥以匹配云简单 VPN 网关密钥。
* 本地标识。 输入本地帕洛阿尔托防火墙的公共 IP 地址。
* 对等识别。 输入主云简单 VPN 对等 IP 地址。

高级选项选项卡：

* 启用被动模式。 保持未选中状态。
* 启用 NAT 遍历。 如果本地帕洛阿尔托防火墙未位于任何 NAT 设备后面，请保持未选中状态。 否则，选择该复选框。

IKEv1：

* 交换模式。 选择**主**。
* IKE 加密配置文件。 选择您之前创建的 IKE 加密配置文件。 未选中启用碎片框。
* 死对等检测。 未选中该框。

重复上述步骤以创建辅助 IKE 网关。

### <a name="5-define-ipsec-crypto-profiles"></a>5. 定义 IPSEC 加密配置文件

选择**网络** > **扩展网络配置文件** > **IPSEC 加密** > **添加**，配置以下字段，然后单击 **"确定**"。

* 名称： 输入 IPsec 加密配置文件的名称。
* IPsec 协议。 选择**ESP**。
* 加密。 单击"**添加"** 并选择适当的加密方法。
* 身份验证。 单击"**添加**"并选择适当的身份验证方法。
* DH集团。 选择**无 pfs**。
* 一生。 设置为 30 分钟。
* 启用。 未选中该框。

重复上述步骤以创建另一个 IPsec 加密配置文件，该配置文件将用作辅助 CloudSimple VPN 对等体。 相同的 IPSEC 加密配置文件也可用于主和辅助 IPsec 隧道（请参阅以下过程）。

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. 定义隧道监控的监视器配置文件

选择 **"网络** > **扩展网络配置文件** > **监视器添加** > **Add**"，配置以下字段，然后单击 **"确定**"。

* 名称： 输入用于隧道监控的监视器配置文件的任何名称，以便对故障做出主动反应。
* 行动。 选择 **"故障转移**"。
* 间隔。 输入值**3**。
* 阈值。 输入值**7**。

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. 设置主和辅助 IPsec 隧道。

选择**网络** > **IPsec 隧道** > **添加**，配置以下字段，然后单击 **"确定**"。

常规选项卡：

* 名称： 输入要与主 CloudSimple VPN 对等互连的主 IPSEC 隧道的任何名称。
* 隧道接口。 选择主隧道接口。
* 键入 。 保留默认值。
* 地址类型。 选择“IPv4”。****
* IKE 网关。 选择主 IKE 网关。
* IPsec 加密配置文件。 选择主 IPsec 配置文件。 选择 **"显示高级"选项**。
* 启用重播保护。 保留默认值。
* 复制 TOS 标头。 未选中该框。
* 隧道监视器。 选中此框。
* 目标 IP。 输入通过站点到站点连接允许的属于云简单私有云子网的任何 IP 地址。 确保帕洛阿尔托上的隧道接口（如隧道.20 - 10.64.5.2/32 和隧道.30 - 10.64.6.2/32）允许通过站点到站点 VPN 到达云简单私有云 IP 地址。 有关代理的，请参阅以下配置。
* 个人资料。 选择监视器配置文件。

代理 ID 选项卡：单击**IPv4** > **添加**并配置以下内容：

* 代理 ID。 输入有趣的流量的任何名称。 在一个 IPsec 隧道中可能携带多个代理指示。
* Local。 指定允许通过站点到站点 VPN 与私有云子网通信的本地本地子网。
* 远程。 指定允许与本地子网通信的私有云远程子网。
* 协议。 选择**任何**。

重复上述步骤以创建另一个 IPsec 隧道，用于辅助 CloudSimple VPN 对等体。

## <a name="references"></a>参考

在思科 ASA 上配置 NAT：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">思科 ASA 5500 系列配置指南</a>

思科 ASA 上支持的 IKEv1 和 IKEv2 属性：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">思科 ASA 系列 CLI 配置指南</a>

在 Cisco ASA 上配置 IPsec 站点到站点 VPN，版本为 8.4 及更高版本：

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">使用 ASA 上的 ASDM 或 CLI 配置 IKEv1 IPsec 站点到站点隧道</a>

在 Azure 上配置思科自适应安全设备虚拟 （ASAv）：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">思科自适应安全虚拟设备 （ASAv） 快速入门指南</a>

在帕洛阿尔托使用代理 ID 配置站点到站点 VPN：

[设置站点到站点 VPN](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

设置隧道监视器：

[设置隧道监控](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE 网关或 IPsec 隧道操作：

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">启用/禁用、刷新或重新启动 IKE 网关或 IPsec 隧道</a>
