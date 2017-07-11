<a id="is-custom-ipsecike-policy-supported-on-all-azure-vpn-gateway-skus" class="xliff"></a>

### 是否所有 Azure VPN 网关 SKU 都支持自定义 IPsec/IKE 策略？
自定义 IPsec/IKE 策略在 Azure VpnGw1、VpnGw2、VpnGw3、标准 VPN 网关和高性能 VPN 网关上受支持。 **基本** SKU。

<a id="how-many-policies-can-i-specify-on-a-connection" class="xliff"></a>

### 在一个连接上可以指定多少个策略？
一个给定的连接只能指定一个策略组合。

<a id="can-i-specify-a-partial-policy-on-a-connection-eg-only-ike-algorithms-but-not-ipsec" class="xliff"></a>

### 能否在一个连接上指定部分策略？ （例如，仅指定 IKE 算法，不指定 IPsec）
否，必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。

<a id="what-are-the-algorithms-and-key-strengths-supported-in-the-custom-policy" class="xliff"></a>

### 自定义策略中支持的算法和密钥强度有哪些？
下表列出了支持的加密算法和密钥强度，可供客户配置。 必须为每个字段选择一个选项。

| IPsec/IKEv2  | 选项                                                                 |
| ---              | ---                                                                         |
| IKEv2 加密 | AES256、AES192、AES128、DES3、DES                                           |
| IKEv2 完整性  | SHA384、SHA256、SHA1、MD5                                                   |
| DH 组         | ECP384、ECP256、DHGroup24、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、无 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无    |
| IPsec 完整性  | GCMAES256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                          |
| PFS 组        | ECP384、ECP256、PFS24、PFS2048、PFS14、PFS2、PFS1、无                     |
| QM SA 生存期*  | 秒数（整数，至少为 300）和 KB 数（整数，至少为 1024）                                      |
| 流量选择器 | UsePolicyBasedTrafficSelectors** ($True/$False; default $False)                             |
|                  |                                                                             |

* (*) 在 Azure VPN 网关上，IKEv2 主模式 SA 生存期固定为 28,800 秒
* (**) 请参阅下一针对“UsePolicyBasedTrafficSelectors”的常见问题解答项

<a id="does-everything-need-to-match-between-the-azure-vpn-gateway-policy-and-my-on-premises-vpn-device-configurations" class="xliff"></a>

### Azure VPN 网关策略与本地 VPN 设备配置是否需完全匹配？
本地 VPN 设备配置必须匹配，或者必须包含可在 Azure IPsec/IKE 策略中指定的以下算法和参数：

* IKE 加密算法
* IKE 完整性算法
* DH 组
* IPsec 加密算法
* IPsec 完整性算法
* PFS 组
* 流量选择器 (*)

SA 生存期是本地规范，不需匹配。

如果启用 UsePolicyBasedTrafficSelectors，则需确保对于本地网络（本地网关）前缀与 Azure 虚拟网络前缀的所有组合，VPN 设备都定义了与之匹配的流量选择器（而不是任意到任意）。 例如，如果本地网络前缀为 10.1.0.0/16 和 10.2.0.0/16，虚拟网络前缀为 192.168.0.0/16 和 172.16.0.0/16，则需指定以下流量选择器：
* 10.1.0.0/16 <====> 192.168.0.0/16
* 10.1.0.0/16 <====> 172.16.0.0/16
* 10.2.0.0/16 <====> 192.168.0.0/16
* 10.2.0.0/16 <====> 172.16.0.0/16

有关如何使用此选项的更多详细信息，请参阅[连接多个基于策略的本地 VPN 设备](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

<a id="does-the-custom-policy-replace-the-default-ipsecike-policy-sets-for-azure-vpn-gateways" class="xliff"></a>

### 自定义策略是否会替换 Azure VPN 网关的默认 IPsec/IKE 策略集？
是的。一旦在连接上指定自定义策略，Azure VPN 网关就会只使用该连接的策略，既充当 IKE 发起方，又充当 IKE 响应方。

<a id="if-i-remove-a-custom-ipsecike-policy-does-the-connection-become-unprotected" class="xliff"></a>

### 如果删除自定义 IPsec/IKE 策略，连接是否会变得不受保护？
否。连接仍受 IPsec/IKE 保护。 从连接中删除自定义策略以后，Azure VPN 网关会还原为[默认的 IPsec/IKE 提议列表](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)，并再次重启与本地 VPN 设备的 IKE 握手。

<a id="would-adding-or-updating-an-ipsecike-policy-disrupt-my-vpn-connection" class="xliff"></a>

### 添加或更新 IPsec/IKE 策略是否会中断 VPN 连接？
是的。那样会导致短时中断（数秒），因为 Azure VPN 网关会断开现有连接并重启 IKE 握手，以便使用新的加密算法和参数重建 IPsec 隧道。 请确保也使用匹配的算法和密钥强度对本地 VPN 设备进行配置，尽量减少中断。

<a id="can-i-use-different-policies-on-different-connections" class="xliff"></a>

### 是否可以在不同的连接上使用不同的策略？
是的。 自定义策略是在单个连接的基础上应用的。 可以在不同的连接上创建并应用不同的 IPsec/IKE 策略。 也可选择在连接子集上应用自定义策略。 剩余连接将使用 Azure 默认 IPsec/IKE 策略集。

<a id="can-i-use-the-custom-policy-on-vnet-to-vnet-connection-as-well" class="xliff"></a>

### 是否也可在 VNet 到 VNet 连接上使用自定义策略？
是的。可以在 IPsec 跨界连接或 VNet 到 VNet 连接上应用自定义策略。

<a id="do-i-need-to-specify-the-same-policy-on-both-vnet-to-vnet-connection-resources" class="xliff"></a>

### 是否需在两个 VNet 到 VNet 连接资源上指定同一策略？
是的。 VNet 到 VNet 隧道包含 Azure 中的两个连接资源，一个方向一个资源。 需确保两个连接资源的策略相同，否则无法建立 VNet 到 VNet 连接。

<a id="does-custom-ipsecike-policy-work-on-expressroute-connection" class="xliff"></a>

### 能否在 ExpressRoute 连接上使用自定义 IPsec/IKE 策略？
不能。 只能通过 Azure VPN 网关在 S2S VPN 和 VNet 到 VNet 连接上使用 IPsec/IKE 策略。
