> [!div class="op_single_selector"]
> * [Portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [模板](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

在一个 Azure 虚拟机 (VM) 上可以附加一个或多个网络接口 (NIC)。 可为任何 NIC 分配一个或多个静态或动态的公共与专用 IP 地址。 为 VM 分配多个 IP 地址可实现以下功能：

* 在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。
* 用作网络虚拟设备，例如防火墙或负载均衡器。
* 可将任何 NIC 的任何专用 IP 地址添加到 Azure Load Balancer 后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。 若要详细了解如何对多个 IP 配置进行负载平衡，请阅读[对多个 IP 配置进行负载平衡](../articles/load-balancer/load-balancer-multiple-ip.md)一文。

附加到 VM 的每个 NIC 都具有一个或多个关联的 IP 配置。 每个配置分配有一个静态或动态专用 IP 地址。 每个配置还可以具有一个关联的公共 IP 地址资源。 公共 IP 地址资源分配有动态或静态公共 IP 地址。 若要详细了解 Azure 中的 IP 地址，请阅读 [Azure 中的 IP 地址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)一文。 最多可为每个 NIC 分配 250 个专用 IP 地址。 虽然可以将多个公共 IP 地址分配给每个 NIC，但 Azure 订阅中能够使用的公共 IP 地址存在限制。 有关详细信息，请参阅 [Azure 限制](../articles/azure-subscription-service-limits.md#networking-limits)一文。


<!--HONumber=Jan17_HO3-->


