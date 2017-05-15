# 概述
## [关于 Azure 网络](networking-overview.md)
## 体系结构
### [非对称路由与多个网络路径](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [安全网络设计](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [网络安全最佳实践](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [高度可用的网络虚拟设备](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [结合使用负载均衡方法](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 规划和设计
### [虚拟网络](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [流量筛选](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [本地连接](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
##  概念
### [虚拟网络](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [网络负载均衡](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [应用程序负载均衡](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [基于 DNS 的流量分配](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [本地连接 - VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [本地连接 - 专用](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)

# 入门
## [创建自己的首个虚拟网络](../virtual-network/virtual-network-get-started-vnet-subnet.md?toc=%2fazure%2fnetworking%2ftoc.json)

# 如何
## Internet 连接
### [网络负载均衡公共服务器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [应用程序负载均衡公共服务器](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [保护 Web 应用程序](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [跨位置分配流量](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 内部连接
### [网络负载均衡专用服务器](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [应用程序负载均衡专用服务器](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [连接虚拟网络（在同一位置）](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [连接虚拟网络（在不同位置）](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 本地连接
### [连接到本地网络（专用）](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [连接到本地网络 (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 管理
### [查看网络拓扑](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [管理数据包捕获](../network-watcher/network-watcher-packet-capture-manage-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [确定路由下一跃点](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [验证 VM 的 IP 流](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## 示例脚本
### [Azure CLI](cli-samples.md)
## 教程
### [对 VM 进行负载均衡](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [将计算机连接到虚拟网络](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)


# 引用
## [Azure CLI](https://docs.microsoft.com/cli/azure/network)
## [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# 资源
## [网络博客](http://azure.microsoft.com/blog/topics/networking)
## [创作模板](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [社区模板](https://azure.microsoft.com/resources/templates/)
## [定价](https://azure.microsoft.com/pricing)
## [区域可用性](https://azure.microsoft.com/regions/services/)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [视频](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

