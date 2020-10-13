---
title: 控制入站流量 v1
description: 了解如何控制到应用服务环境的入站流量。 本文档仅供使用旧版 v1 ASE 的用户使用。
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962054"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>如何控制应用服务环境的入站流量
## <a name="overview"></a>概述
可以 **在 Azure** 资源管理器虚拟网络 **或** 经典部署模型 [虚拟网络][virtualnetwork]中创建应用服务环境。  创建应用服务环境时，可以定义新的虚拟网络和新的子网。 相反，可以在预先存在的虚拟网络和预先存在的子网中创建应用服务环境。  截至2016年6月，还可以将 Ase 部署到使用公用地址范围或 RFC1918 地址空间 (专用地址) 的虚拟网络中。  有关详细信息，请参阅 [如何创建应用服务环境][HowToCreateAnAppServiceEnvironment]。

始终在子网中创建应用服务环境。 子网提供网络边界，可用于锁定上游设备和服务后面的入站流量。 此设置仅允许特定上游 IP 地址接受 HTTP 和 HTTPS 流量。

使用[网络安全组][NetworkSecurityGroups]可以控制子网上的入站和出站网络流量。 若要控制入站流量，请在网络安全组中创建网络安全规则。 然后，将网络安全组分配给包含应用服务环境的子网。

将网络安全组分配给子网后，根据网络安全组中定义的允许和拒绝规则，允许或阻止到应用服务环境中的应用的入站流量。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>应用服务环境中使用的入站网络端口
锁定包含网络安全组的入站网络流量之前，请了解应用服务环境所用的必需和可选网络端口集。  意外关闭发往某些端口的流量可能会在应用服务环境中导致功能丢失。

以下列表包含应用服务环境使用的端口。 所有端口都是 **TCP**，除非另有明确的说明：

* 454： Azure 基础结构用于通过 TLS 管理和维护应用服务环境的  **必需端口** 。  不要阻止流量流向此端口。  此端口始终绑定到 ASE 的公共 VIP。
* 455： Azure 基础结构用于通过 TLS 管理和维护应用服务环境的  **必需端口** 。  不要阻止流量流向此端口。  此端口始终绑定到 ASE 的公共 VIP。
* 80：用于将入站 HTTP 流量发往应用服务环境的应用服务计划中运行的应用的默认端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 443：用于将入站 TLS 流量发送到应用服务环境中的应用服务计划中运行的应用的默认端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 21：FTP 的控制通道。  如果未使用 FTP，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口可以绑定到 ASE 的 ILB 地址。
* 990：FTPS 的控制通道。  如果未使用 FTPS，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口可以绑定到 ASE 的 ILB 地址。
* 10001-10020：FTP 的数据通道。  与控制通道一样，如果未使用 FTP，则可以安全地阻止这些端口。  在启用 ILB 的 ASE 中，此端口可以绑定到 ASE 的 ILB 地址。
* 4016：用于 Visual Studio 2012 的远程调试。  如果未使用该功能，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 4018：用于 Visual Studio 2013 的远程调试。  如果未使用该功能，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 4020：用于 Visual Studio 2015 的远程调试。  如果未使用该功能，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。

## <a name="outbound-connectivity-and-dns-requirements"></a>出站连接和 DNS 要求
若要让应用服务环境正常运行，还需具有对各个终结点的出站访问权限。 [Network Configuration for ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)（ExpressRoute 的网络配置）一文的“所需的网络连接”部分中提供了 ASE 所用外部终结点的完整列表。

应用服务环境要求为虚拟网络配置有效 DNS 基础结构。  如果 DNS 配置在创建应用服务环境之后发生更改，则开发人员可以强制应用服务环境选取新的 DNS 配置。  如果使用 " **重新启动** " 图标触发滚动环境重启，环境将选取新的 DNS 配置。  (" **重新启动** " 图标位于 "应用服务环境管理" 边栏选项卡顶部的 " [Azure 门户][NewPortal]" 中。 ) 

还建议提前设置 vnet 中的任何自定义 DNS 服务器，然后再创建应用服务环境。  如果在创建应用服务环境过程中更改虚拟网络的 DNS 配置，应用服务环境创建过程将失败。  同样，如果自定义 DNS 服务器在 VPN 网关的另一端无法访问或不可用，则应用服务环境创建过程也会失败。

## <a name="creating-a-network-security-group"></a>创建网络安全组
有关网络安全组工作原理的完整详情，请参阅以下[信息][NetworkSecurityGroups]。  以下 Azure 服务管理示例涉及网络安全组的要点。 该示例将网络安全组配置和应用到包含应用服务环境的子网。

**注意：** 可以使用 [Azure 门户](https://portal.azure.com) 或 Azure PowerShell 以图形方式配置网络安全组。

网络安全组最初创建为与订阅关联的独立实体。 由于网络安全组是在 Azure 区域中创建的，因此请在与应用服务环境相同的区域中创建网络安全组。

以下命令演示了如何创建网络安全组：

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

创建网络安全组后，将一个或多个网络安全规则添加到其中。  由于规则集可能会随时间而改变，因此应缩小用于规则优先级的编号方案的间距。 这种做法可以轻松地随着时间推移插入其他规则。

在下面的示例中，规则显式授予 Azure 基础结构管理和维护应用服务环境所需的管理端口的访问权限。  所有管理流量流经 TLS，并受客户端证书的保护。 即使端口已打开，Azure 管理基础结构以外的任何实体也无法访问这些端口。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

当你锁定对端口80和443的访问以 "隐藏" 上游设备或服务后面的应用服务环境时，请记住上游 IP 地址。  例如，如果你使用 web 应用程序防火墙 (WAF) ，则 WAF 将有自己的 IP 地址。 WAF 在将流量代理到下游应用服务环境时使用它们。  需要在网络安全规则的 *SourceAddressPrefix* 参数中使用此 IP 地址。

在以下示例中，显式允许来自特定上游 IP 地址的入站流量。  地址 *1.2.3.4* 用作上游 WAF 的 IP 地址占位符。  请更改此值，以便与上游设备或服务使用的地址匹配。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

如果需要 FTP 支持，请使用以下规则作为模板，以授予对 FTP 控制端口和数据通道端口的访问权限。  由于 FTP 是有状态协议，因此你可能无法通过传统 HTTP/HTTPS 防火墙或代理设备路由 FTP 流量。  在这种情况下，需要将 *SourceAddressPrefix* 设置为不同的值，例如运行 FTP 客户端的开发人员或部署计算机的 IP 地址范围。 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

（**注意：** 数据通道端口范围在预览期可能会更改。）

以下规则演示了在使用 Visual Studio 远程调试时，如何授予访问权限。  对于每个受支持的 Visual Studio 版本，都有一个单独的规则，因为每个版本使用不同的端口进行远程调试。  对于 FTP 访问，远程调试流量可能不会通过传统 WAF 或代理设备正确传送。  可以改为将 *SourceAddressPrefix* 设置成运行 Visual Studio 的开发人员计算机的 IP 地址范围。

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>将网络安全组分配给子网
网络安全组具有拒绝访问所有外部流量的默认安全规则。 将此规则与上述网络安全规则结合使用时，只有来自与 *允许* 操作关联的源地址范围的流量才能将流量发送到在应用服务环境中运行的应用。

为网络安全组填充安全规则后，将其分配给包含应用服务环境的子网。  赋值命令引用两个名称：应用服务环境所在的虚拟网络的名称，以及在其中创建应用服务环境的子网的名称。  

以下示例显示了要分配给子网和虚拟网络的网络安全组：

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

分配是一个长时间运行的操作，可能需要几分钟才能完成。 网络安全组分配成功后，只有符合 *允许* 规则的入站流量才能成功访问应用服务环境中的应用。

为完整起见，以下示例显示了如何从子网中删除网络安全组并解除关联：

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>有关显式 IP-SSL 的特殊注意事项
如果使用显式 IP-SSL 地址配置了应用 (*仅* 适用于具有公共 VIP) 的 ase，而不使用应用服务环境的默认 IP 地址，则 HTTP 和 HTTPS 流量将通过端口80和443以外的端口流过子网。

若要查找每个 IP-SSL 地址使用的单独端口对，请前往门户并查看应用服务环境的 "详细信息" 边栏选项卡。  选择 "**所有设置**" "  >  **IP 地址**"。  " **IP 地址** " 边栏选项卡显示了应用服务环境的所有明确配置的 IP-SSL 地址的表。 该边栏选项卡还显示了用于路由与每个 IP SSL 地址相关联的 HTTP 和 HTTPS 流量的特殊端口对。  在网络安全组中配置规则时，对 DestinationPortRange 参数使用此端口对。

当 ASE 上的应用配置为使用 IP SSL 时，外部客户不会看到或需要考虑特殊端口对映射。  发往应用程序的流量正常情况下将流向已配置的 IP-SSL 地址。  在将流量的最后一段路由到包含 ASE 的子网中时，会在内部自动进行到特殊端口对的转换。 

## <a name="getting-started"></a>入门
若要开始应用服务环境，请参阅 [应用服务环境简介][IntroToAppServiceEnvironment]。

有关详细信息，请参阅 [从应用服务环境安全连接到后端资源][SecurelyConnecttoBackend]。

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->