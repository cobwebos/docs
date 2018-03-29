# [VPN 网关文档](index.md)

# 概述
## [关于 VPN 网关](vpn-gateway-about-vpngateways.md)
## [VPN 网关常见问题](vpn-gateway-vpn-faq.md)
## [订阅和服务限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# 入门
## [规划和设计 VPN 网关](vpn-gateway-plan-design.md)
## [关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md)
## [关于 VPN 设备](vpn-gateway-about-vpn-devices.md)
## [关于加密要求](vpn-gateway-about-compliance-crypto.md)
## [关于 BGP 和 VPN 网关](vpn-gateway-bgp-overview.md)
## [关于高可用连接](vpn-gateway-highlyavailable.md)
## [关于点到站点连接](point-to-site-about.md)

# 如何
## 配置站点到站点连接
### [Azure 门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Azure 门户（经典）](vpn-gateway-howto-site-to-site-classic-portal.md)

## [下载 VPN 设备配置脚本](vpn-gateway-download-vpndevicescript.md)

## 配置点到站点连接 - 本机 Azure 证书身份验证
### 配置 P2S VPN
#### [Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Azure 门户（经典）](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### 生成自签名证书
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md)
### [安装客户端证书](point-to-site-how-to-vpn-client-install-azure-cert.md)

## 配置点到站点连接 - RADIUS 身份验证
### 配置 P2S VPN
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-radius.md)
### [将 P2S VPN RADIUS 身份验证与 NPS 服务器集成](vpn-gateway-radiuis-mfa-nsp.md)

## 配置 VNet 到 VNet 连接
### [Azure 门户](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [Azure 门户（经典）](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## 配置部署模型之间的 VNet 到 VNet 连接
### [Azure 门户](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## 配置站点到站点和 ExpressRoute 的共存连接
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## 配置多个站点到站点连接
### [Azure 门户](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell（经典）](vpn-gateway-multi-site.md)
## 连接多个基于策略的 VPN 设备
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## 在连接上配置 IPsec/IKE 策略
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## 配置高可用的主动-主动连接
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## 为 VPN 网关配置 BGP
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [Azure CLI](bgp-how-to-cli.md)
## 配置强制隧道
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell（经典）](vpn-gateway-about-forced-tunneling.md)
## 修改本地网络网关设置
### [Azure 门户](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [验证 VPN 网关连接](vpn-gateway-verify-connection-resource-manager.md)
## [重置 VPN 网关](vpn-gateway-resetgw-classic.md)
## 删除 VPN 网关
### [Azure 门户](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell（经典）](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [配置 VPN 网关（经典）](vpn-gateway-configure-vpn-gateway-mp.md)
## [网关 SKU（旧版）](vpn-gateway-about-skus-legacy.md)
## 配置第三方 VPN 设备
### [概述和 Azure 配置](vpn-gateway-3rdparty-device-config-overview.md)
### [示例：Cisco ASA 设备（IKEv2/无 BGP）](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [从经典部署模型迁移到资源管理器部署模型](vpn-gateway-classic-resource-manager-migration.md)
## [故障排除](vpn-gateway-troubleshoot.md)
### [验证到达 VNet 的 VPN 吞吐量](vpn-gateway-validate-throughput-to-vnet.md)
### [社区建议的 VPN 或防火墙设备设置](vpn-gateway-third-party-settings.md)
### [点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [点到站点连接问题 - Mac OS X VPN 客户端](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### [站点到站点连接问题](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [站点到站点连接间歇性地断开](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [配置和验证 VNet 或 VPN 连接](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)

# 引用
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell（经典）](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST（经典）](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# 相关内容
## [虚拟网络](/azure/virtual-network/)
## [应用程序网关](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [流量管理器](/azure/traffic-manager/)
## [负载均衡器](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=networking)
## [博客](https://azure.microsoft.com/blog/topics/networking)
## [论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [定价](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
