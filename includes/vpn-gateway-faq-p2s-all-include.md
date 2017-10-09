### <a name="supportedclientos"></a>点到站点连接可以用于哪些客户端操作系统？

支持以下客户端操作系统：

* Windows 7（32 位和 64 位）
* Windows Server 2008 R2（仅 64 位）
* Windows 8（32 位和 64 位）
* Windows 8.1（32 位和 64 位）
* Windows Server 2012（仅 64 位）
* Windows Server 2012 R2（仅 64 位）
* Windows 10
* 适用于 Mac 的 OSX 版本 10.11 (El Capitan)
* 适用于 Mac 的 macOS 版本 10.12 (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>在我的点到站点配置中，可以有多少 VPN 客户端终结点？

我们最多支持 128 个 VPN 客户端可同时连接到一个虚拟网络。

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>能否使用点到站点功能穿越代理和防火墙？

Azure 支持两种类型的点到站点 VPN 选项：

* 安全套接字隧道协议 (SSTP)。 SSTP 是 Microsoft 专有的基于 SSL 的解决方案，它可以穿透防火墙，因为大多数防火墙都打开了 443 SSL 使用的 TCP 端口。

* IKEv2 VPN。 IKEv2 VPN 是一个基于标准的 IPsec VPN 解决方案，它使用 UDP 端口 500 和 4500 以及 IP 协议号  50。 防火墙并非始终打开这些端口，因此，IKEv2 VPN 有可能无法穿过代理和防火墙。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>如果重新启动进行过点到站点配置的客户端计算机，是否会自动重新连接 VPN？

默认情况下，客户端计算机将不自动重新建立 VPN 连接。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>点到站点在 VPN 客户端上是否支持自动重新连接和 DDNS？

点到站点 VPN 中当前不支持自动重新连接和 DDNS。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>对于同一虚拟网络，站点到站点和点到站点配置能否共存？

是的。 对于资源管理器部署模型，必须为网关使用 RouteBased VPN 类型。 对于经典部署模型，需要一个动态网关。 不支持将点到站点配置用于静态路由 VPN 网关或 PolicyBased VPN 网关。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>能否将点到站点客户端配置为同时连接到多个虚拟网络？

不会。 点到站点客户端只能连接到虚拟网络网关所在的 VNet 中的资源。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>预计通过站点到站点连接或点到站点连接的吞吐量有多少？

很难维持 VPN 隧道的准确吞吐量。 IPsec 和 SSTP 是重重加密的 VPN 协议。 本地网络与 Internet 之间的延迟和带宽也限制了吞吐量。 对于仅具有 IKEv2 点到站点 VPN 连接的 VPN 网关，期望可以实现的总吞吐量取决于网关 SKU。 有关吞吐量的详细信息，请参阅[网关 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)。

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>是否可以将任何软件 VPN 客户端用于支持 SSTP 和/或 IKEv2 的点到站点配置？

不会。 只能将 Windows 上的本机 VPN 客户端用于 SSTP，只能将 Mac 上的本机 VPN 客户端用于 IKEv2。 请参阅支持的客户端操作系统的列表。

### <a name="can-i-access-the-internet-when-i-am-connected-over-p2s-vpn"></a>通过 P2S VPN 连接时是否可以访问 Internet？

是的，在 P2S VPN 中可以访问 Internet。

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure 是否支持使用 Windows 的 IKEv2 VPN？

用户可以使用支持 IKEv2 的内置 Windows VPN 客户端连接到 Azure。 但是，在以下情况下，从 Windows 设备建立的 IKEv2 连接不起作用：

  如果用户的设备包含大量的受信任根证书，在 IKE 交换期间，消息有效负载会很大，导致 IP 层碎片化。 碎片会在 Azure 端被拒绝，导致连接失败。 导致此问题的确切证书数目很难估算。 因此，无法保证从 Windows 设备建立的 IKEv2 连接可行。 在混合环境（包括 Windows 和 Mac 设备）中配置 SSTP 和 IKEv2 时，Windows VPN 配置文件始终会先尝试 IKEv2 隧道。 如果由于此处所述的问题而导致这种尝试失败，将会回退到 SSTP。

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>除了 Windows 和 Mac 以外，Azure 还支持在其他哪些平台上使用 P2S VPN？

Azure 仅支持在 Windows 和 Mac 上使用 P2S VPN。

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enabled-radius-andor-ikev2-vpn-on-it"></a>我已部署 Azure VPN 网关。 是否可在该网关上启用 RADIUS 和/或 IKEv2 VPN？

是的，可以通过 Powershell 和 Azure 门户，在已部署的网关启用这些新功能。