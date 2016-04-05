<!-- not suitable for Mooncake -->

<properties 
	pageTitle="如何控制 Azure 环境的入站流量" 
	description="了解如何配置网络安全规则，以控制发往 Azure 环境的入站流量。" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="02/26/2016"
	wacn.date=""/>	

# 如何控制 Azure 环境的入站流量

## 概述 ##
Azure 环境始终在区域性经典“v1”[虚拟网络][virtualnetwork]的子网中创建。创建 Azure 环境时，可以定义新的区域性经典“v1”虚拟网络和新的子网。或者，可以在预先存在的区域性经典“v1”虚拟网络和预先存在的子网中创建 Azure 环境。有关创建 Azure 环境的详细信息，请参阅[如何创建 Azure 环境][HowToCreateAnAppServiceEnvironment]。

**注意：**在“v2”ARM 管理的虚拟网络中无法创建 Azure 环境。

Azure 环境始终必须在子网中创建，由于子网提供网络边界用于锁定上游设备和服务后面的入站流量，因此只接受来自特定上游 IP 地址的 HTTP 和 HTTPS 流量。

使用[网络安全组][NetworkSecurityGroups]可以控制子网上的入站和出站网络流量。控制入站流量时，需要在网络安全组中创建网络安全规则，然后将网络安全组分配给包含 Azure 环境的子网。

将网络安全组分配到子网后，根据网络安全组中定义的允许和拒绝规则，允许/阻止 Azure 环境中应用程序的入站流量。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../includes/app-service-web-to-api-and-mobile.md)]

## Azure 环境中使用的网络端口 ##
使用网络安全组阻止入站网络流量之前，请务必知道 Azure 环境所用的必需和可选网络端口集。意外关闭发往某些端口的流量可能会在 Azure 环境中导致功能丢失。

以下是 Azure 环境使用的端口列表：

- 454：Azure 基础结构用来管理和维护 Azure 环境的**必需端口**。不要阻止发往此端口的流量。
- 455：Azure 基础结构用来管理和维护 Azure 环境的**必需端口**。不要阻止发往此端口的流量。
- 80：用于将入站 HTTP 流量发往 Azure 环境的 App Service 计划中运行的应用的默认端口
- 443：用于将入站 SSL 流量发往 Azure 环境的 App Service 计划中运行的应用的默认端口
- 21：FTP 的控制通道。如果未使用 FTP，则可以安全地阻止此端口。
- 10001-10020：FTP 的数据通道。在控制通道中，如果未使用 FTP，则可以安全地阻止这些端口（**注意：**FTP 数据通道在预览期可能会更改。）
- 4016：用于 Visual Studio 2012 的远程调试。如果未使用该功能，则可以安全地阻止此端口。
- 4018：用于 Visual Studio 2013 的远程调试。如果未使用该功能，则可以安全地阻止此端口。
- 4020：用于 Visual Studio 2015 的远程调试。如果未使用该功能，则可以安全地阻止此端口。

## 出站连接和 DNS 要求 ##
为了使让 Azure 环境正常工作，需要对全球 Azure 存储空间以及相同 Azure 区域中 SQL 数据库的出站访问权限。如果虚拟网络中阻止了出站 Internet 访问，则 Azure 环境将无法访问这些 Azure 终结点。

Azure 环境还要求针对虚拟网络设置的有效 DNS 基础结构。如果 DNS 配置在创建 Azure 环境之后因为任何原因而更改，开发人员可以强制 Azure 环境选择新的 DNS 配置。使用位于 [Azure 管理门户][NewPortal]中“Azure 环境管理”边栏选项卡顶部的“重新启动”图标触发轮流环境重新启动，会导致环境选择新的 DNS 配置。

以下列表详细描述了 Azure 环境所需的连接和 DNS：

-  与全球 Azure 存储空间终结点建立的出站网络连接。这包括位于与 Azure 环境相同区域中的终结点，以及位于**其他** Azure 区域的存储终结点。Azure 存储空间终结点在以下 DNS 域之下解析： table.core.chinacloudapi.cn、blob.core.chinacloudapi.cn、queue.core.chinacloudapi.cn 和 file.core.chinacloudapi.cn。  
-  与位于 Azure 环境相同区域中的 SQL 数据库终结点建立的出站网络连接。Sql 数据库终结点在以下域之下解析：database.chinacloudapi.cn。
-  与 Azure 管理平面终结点（ASM 和 ARM 终结点）建立的出站网络连接。这包括与 management.core.chinacloudapi.cn 和 management.azure.com 建立的出站连接。 
-  与 ocsp.msocsp.com 建立的出站连接。需要此连接才能支持 SSL 功能。
-  虚拟网络的 DNS 设置必须能够解析前面几点所提到的所有终结点和域。如果无法解析这些终结点，Azure 环境创建尝试将失败，并且现有的 Azure 环境标记为状况不正常。
-  如果 VPN 网关的另一端有自定义 DNS 服务器存在，则必须可从包含 Azure 环境的子网连接该 DNS 服务器。 
-  出站网络路径不可经过内部公司代理，也不可使用强制通道发送到本地。否则会更改来自 Azure 环境的出站网络流量的有效 NAT 地址。更改 Azure 环境的出站网络流量的 NAT 地址会导致上述众多终结点的连接失败。这将造成 Azure 环境创建尝试失败，并且前面状况良好的 Azure 环境将标记为状况不正常。  
-  必须根据此[文章](/documentation/articles/app-service-app-service-environment-control-inbound-traffic)中所述，允许对 Azure 环境所需的端口进行入站网络访问。

此外，还建议事先在 VNet 上设置任何自定义 DNS 服务器，然后创建 Azure 环境。如果在创建 Azure 环境时更改虚拟网络的 DNS 配置，将会导致 Azure 环境创建过程失败。同样地，如果自定义 DNS 服务器存在于 VPN 网关的另一端，且 DNS 服务器无法连接或使用，则 Azure 环境创建过程也会失败。

## 创建网络安全组 ##
有关网络安全组工作原理的完整详情，请参阅以下[信息][NetworkSecurityGroups]。以下详细信息涉及到了网络安全组的要点，着重讲解如何配置网络安全组并其应用到包含 Azure 环境的子网。

**注意：**网络安全组只能使用如下所述的 Powershell cmdlet 来配置。由于 [Azure 管理门户](portal.azure.com)仅允许使用与“v2”虚拟网络关联的 NSG 图形配置，因此你无法使用管理门户以图形方式配置网络安全组。但是，Azure 环境目前仅支持使用经典“v1”虚拟网络。因此，仅可使用 Powershell cmdlet 将网络安全组配置为与“v1”虚拟网络关联。

网络安全组最初创建为与订阅关联的独立实体。由于网络安全组是在 Azure 区域中创建的，因此请确保在与 Azure 环境相同的区域中创建网络安全组。

下面演示了如何创建网络安全组：

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "China East" -Label "Example network security group for an app service environment"

创建网络安全组后，将一个或多个网络安全规则添加到其中。由于规则集会随时改变，因此建议对规则优先级使用单独的编号方案，以便陆续插入其他规则。

以下示例显示的规则可显式授予 Azure 基础结构管理和维护 Azure 环境所需的管理端口的访问权限。请注意，所有管理流量都通过 SSL 传送并受客户端证书的保护，因此即使端口已打开，Azure 管理基础结构以外的任何实体也无法访问这些端口。


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

阻止对端口 80 和 443 的访问以便“隐藏”上游设备或服务后面的 Azure 环境时，你必须知道上游 IP 地址。例如，如果你使用 Web 应用程序防火墙 (WAF)，则 WAF 将有自己的 IP 地址，以便在将流量代理到下游 Azure 环境时使用。你必须在网络安全规则的 SourceAddressPrefix 参数中使用此 IP 地址。

在以下示例中，显式允许来自特定上游 IP 地址的入站流量。地址1.2.3.4用作上游 WAF 的 IP 地址占位符。请更改此值，以便与上游设备或服务使用的地址匹配。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
如果需要 FTP 支持，可以使用以下规则作为模板，以授予对 FTP 控制端口和数据通道端口的访问权限。由于 FTP 是有状态协议，你无法通过传统 HTTP/HTTPS 防火墙或代理设备路由 FTP 流量。在此情况下，你需要将 SourceAddressPrefix 设为其他值 - 例如，FTP 客户端运行所在的开发人员或部署计算机的 IP 地址范围。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

（**注意：**数据通道端口范围在预览期可能会更改。）

以下规则演示了在使用 Visual Studio 远程调试时，如何授予访问权限。由于每个支持的 Visual Studio 版本使用不同的端口进行远程调试，因此每个版本有不同的规则。对于 FTP 访问，远程调试流量可能不会通过传统 WAF 或代理设备正确传送。可以改为将 SourceAddressPrefix 设置成运行 Visual Studio 的开发人员计算机的 IP 地址范围。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## 将网络安全组分配给子网 ##
网络安全组包含拒绝访问所有外部流量的默认安全规则。将上述网络安全规则与用于阻止入站流量的默认安全规则相结合后，只有来自与“允许”操作关联的源地址范围的流量能够发送到 Azure 环境中运行的应用。

在网络安全组中填充安全规则后，需要将该组分配到包含 Azure 环境的子网。分配命令将引用 Azure 环境所在的虚拟网络的名称，以及 Azure 环境创建所在的子网的名称。

以下示例显示了要分配给子网和虚拟网络的网络安全组：


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

网络安全组分配成功后（分配是一项长时间运行的操作，可能需要几分钟才能完成），只有符合“允许”规则的入站流量才能成功抵达 Azure 环境中的应用。

出于完整性考虑，以下示例演示了如何删除网络安全组，从而取消它与子网之间的关联：


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## 有关显式 IP-SSL 的特殊注意事项 ##
如果使用显式 IP 地址而不是 Azure 环境的默认 IP 地址配置了应用，则 HTTP 和 HTTPS 流量将通过一组非 80 和 443 的端口发往子网。

若要查找每个 IP-SSL 的地址所用的每个端口对，可在 Azure 环境的用户界面边栏选项卡中，依次单击“所有设置”-->“IP 地址”。“IP 地址”边栏选项卡显示 Azure 环境的所有显式配置的 IP-SSL 地址表，以及用于路由与每个 IP-SSL 地址关联的 HTTP 和 HTTPS 流量的特殊端口对。在网络安全组中配置规则时，需要针对 DestinationPortRange 参数使用此端口对。

## 入门

若要开始使用 Azure 环境，请参阅 [Azure 环境简介][IntroToAppServiceEnvironment]

有关安全地从 Azure 环境连接到后端资源的详细信息，请参阅[从 Azure 环境安全连接到后端资源][SecurelyConnecttoBackend]

有关 Azure 平台的详细信息，请参阅 [Azure Web 应用][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: /documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: /documentation/articles/virtual-networks-nsg/
[AzureAppService]: /documentation/services/web-sites/
[IntroToAppServiceEnvironment]: /documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]: /documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]: https://manage.windowsazure.cn

<!-- IMAGES -->
 

<!---HONumber=Mooncake_0328_2016-->