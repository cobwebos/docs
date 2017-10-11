> [!div class="op_single_selector"]
> * [门户](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI 2.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
> * [CLI 1.0](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli-nodejs.md)
> * [模板](../articles/virtual-network/virtual-network-multiple-ip-addresses-template.md)
>

Azure 虚拟机 (VM) 具有一个或多个网络接口 (NIC) 附加到它。 任何 NIC 可以具有一个或多个静态或动态公用和专用 IP 地址分配给它。 将多个 IP 地址分配给 VM 启用以下功能：

* 承载多个网站或服务具有不同的 IP 地址和一台服务器上的 SSL 证书。
* 充当网络虚拟设备，例如防火墙或负载平衡器。
* 能够将任何的专用 IP 地址的任何 Nic 添加到 Azure 负载平衡器后端池。 在过去，仅主 IP 地址的主 NIC 无法添加到后端池。 若要了解有关如何进行负载平衡的详细多个 IP 配置，读取[负载平衡多个 IP 配置](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json)文章。

每个附加到 VM 的 NIC 具有与之关联的一个或多个 IP 配置。 每个配置分配一个的静态或动态专用 IP 地址。 每个配置还可能有与之关联的一个公共 IP 地址资源。 公共 IP 地址资源具有任一动态或静态公共 IP 地址分配给它。 若要了解有关 Azure 中的 IP 地址的详细信息，请阅读[在 Azure 中的 IP 地址](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)文章。 

没有限于多少专用 IP 地址可以分配到 nic。 此外，还有多少可用 Azure 订阅中的公共 IP 地址限制。 请参阅[Azure 限制](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)有关详细信息的文章。
