# 概述
## [虚拟网络](virtual-networks-overview.md)
## [用户定义的路由和 IP 转发](virtual-networks-udr-overview.md)
## [虚拟网络对等互连](virtual-network-peering-overview.md)
## [业务连续性](virtual-network-disaster-recovery-guidance.md)
## [常见问题](virtual-networks-faq.md)
## [IP 寻址](virtual-network-ip-addresses-overview-arm.md)
## 经典
### [IP 寻址](virtual-network-ip-addresses-overview-classic.md)
### [访问控制列表](virtual-networks-acl.md)

# 入门
## [创建自己的首个虚拟网络](virtual-network-get-started-vnet-subnet.md)

# 如何
## 规划和设计
### [虚拟网络](virtual-network-vnet-plan-design-arm.md)
### [网络安全组](virtual-networks-nsg.md)

## 部署
### [虚拟网络](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [模板](virtual-networks-create-vnet-arm-template-click.md)

### 网络安全组
#### [门户](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [模板](virtual-networks-create-nsg-arm-template.md)
#### 经典
##### [PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [CLI](virtual-networks-create-nsg-classic-cli.md)

### 用户定义路由
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [模板](virtual-network-create-udr-arm-template.md)
#### 经典
##### [PowerShell](virtual-network-create-udr-classic-ps.md)
##### [CLI](virtual-network-create-udr-classic-cli.md)

### 虚拟网络对等互连
#### [同一部署模型 - 同一订阅](virtual-network-create-peering.md)
#### [同一部署模型 - 不同订阅](create-peering-different-subscriptions.md)
#### [不同部署模型 - 同一订阅](create-peering-different-deployment-models.md)
#### [不同部署模型 - 不同订阅](create-peering-different-deployment-models-subscriptions.md)

### 虚拟机
#### 创建具有静态公共 IP 地址的 VM
##### [门户](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [模板](virtual-network-deploy-static-pip-arm-template.md)
##### 经典
###### [PowerShell](virtual-networks-reserved-public-ip.md)

#### 创建具有静态专用 IP 地址的 VM
##### [门户](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### 经典
###### [门户](virtual-networks-static-private-ip-classic-pportal.md)
###### [PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [CLI](virtual-networks-static-private-ip-classic-cli.md)

#### 创建具有多个网络接口的 VM
##### [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### 经典
###### [PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [CLI](virtual-network-deploy-multinic-classic-cli.md)

#### 创建具有多个 IP 地址的 VM
##### [Azure 门户](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [模板](virtual-network-multiple-ip-addresses-template.md)

#### [创建具有加速网络的 VM](virtual-network-create-vm-accelerated-networking.md)

### 连接方案
#### [虚拟网络 (VNet) 到 VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) 到 VNet（经典）](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet 到本地网络 (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet 到本地网络 (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [高可用性混合网络体系结构](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### 安全方案
#### [使用虚拟设备保护网络](virtual-network-scenario-udr-gw-nva.md)
#### [Azure 与 Internet 之间的外围网络](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [云服务和网络安全](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [使用 NSG 的简单外围网络](virtual-networks-dmz-nsg-asm.md)
##### [使用防火墙和 NSG 的外围网络](virtual-networks-dmz-nsg-fw-asm.md)
##### [使用防火墙、UDR 和 NSG 的外围网络](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [示例应用程序](virtual-networks-sample-app.md)

### 经典
#### [虚拟网络](create-virtual-network-classic.md)
##### [门户](virtual-networks-create-vnet-classic-pportal.md)
##### [PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [CLI](virtual-networks-create-vnet-classic-cli.md)

## 配置
### 虚拟机
#### [添加或删除网络接口](virtual-network-network-interface-vm.md)
#### [VM 和云服务的名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [优化网络吞吐量](virtual-network-optimize-network-bandwidth.md)
#### [查看和修改主机名](virtual-networks-viewing-and-modifying-hostnames.md)
### 经典
#### 访问控制列表
##### [门户](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [PowerShell](virtual-networks-acl-powershell.md)

## 管理
### [虚拟网络](virtual-network-manage-network.md)
#### [子网](virtual-network-manage-subnet.md)
#### [对等互连](virtual-network-manage-peering.md)
#### 经典
##### [网络配置文件](virtual-networks-using-network-configuration-file.md)
##### [从地缘组迁移到区域](virtual-networks-migrate-to-regional-vnet.md)
### 网络安全组
#### [门户](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [日志](virtual-network-nsg-manage-log.md)
### 网络接口 (NIC)
#### [创建、更改或删除 NIC](virtual-network-network-interface.md)
#### [添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)
### 虚拟机
#### [将 VM 移到其他子网](virtual-networks-move-vm-role-to-subnet.md)
### [公共 IP 地址](virtual-network-public-ip-address.md)

## 故障排除
### 网络安全组
#### [门户](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### 路由
#### [门户](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [吞吐量测试](virtual-network-bandwidth-testing.md)
### [无法删除虚拟网络](virtual-network-troubleshoot-cannot-delete-vnet.md)

# 引用
## [代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=virtual-network)
## [PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [PowerShell（经典）](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST（经典）](https://msdn.microsoft.com/library/jj157182.aspx)


# 相关内容
## [虚拟机](/azure/virtual-machines/)
## [应用程序网关](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [流量管理器](/azure/traffic-manager/)
## [负载均衡器](/azure/load-balancer/)
## [VPN 网关](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=networking)
## [网络博客](http://azure.microsoft.com/blog/topics/networking)
## [网络论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [定价](https://azure.microsoft.com/pricing/details/virtual-network)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-virtual-network)
