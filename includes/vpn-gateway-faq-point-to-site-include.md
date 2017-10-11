### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>使用点到站点可以使用哪些客户端操作系统？

支持以下客户端操作系统：

* Windows 7 （32 位和 64 位）
* Windows Server 2008 R2 （仅限 64 位）
* Windows 8 （32 位和 64 位）
* Windows 8.1 （32 位和 64 位）
* Windows Server 2012 （仅限 64 位）
* Windows Server 2012 R2 （仅限 64 位）
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>可以为点到站点支持 SSTP 的使用任何软件 VPN 客户端？

否。 支持仅仅限于上面列出的 Windows 操作系统版本。

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>在我的点到站点配置中可以有多少 VPN 客户端终结点？

我们支持最多 128 个 VPN 客户端能够在同一时间连接到虚拟网络。

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>能否使用我自己的内部 PKI 根 CA 用于点到站点连接？

是。 以前，无法使用仅自签名的根证书。 你仍可上载 20 的根证书。

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>可以遍历代理和防火墙使用点到站点功能？

是。 作为隧道穿越防火墙，我们使用 SSTP （安全套接字隧道协议）。 此隧道将显示为 HTTPs 连接。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>如果重新启动配置用于点到站点的客户端计算机，将自动重新连接 VPN？

默认情况下，客户端计算机将不重新建立 VPN 连接自动。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>没有点到站点支持自动重新连接和 DDNS VPN 客户端上的？

自动重新连接和 DDNS 是目前不支持点到站点 Vpn。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>我是否可以拥有站点到站点和点到站点配置共存为同一虚拟网络？

是。 如果你在为网关 RouteBased VPN 类型，这两种这些解决方案将起作用。 对于经典部署模型中，你需要动态网关。 我们做不支持点到站点的静态路由 VPN 网关或使用的网关`-VpnType PolicyBased`cmdlet。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>可以配置点到站点客户端同时连接到多个虚拟网络？

是，可能。 虚拟网络不能具有重叠 IP 前缀，但在虚拟网络之间的点到站点地址空间不得重叠。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>可以预计通过站点到站点或点到站点连接多少吞吐量？

很难维护 VPN 隧道的准确吞吐量。 IPsec 和 SSTP 是重重加密的 VPN 协议。 吞吐量也受限制的延迟和你的本地和 Internet 之间的带宽。