---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671482"
---
虽然此功能容易设置，但这并不意味着你就不会遇到问题。 如果在访问所需终结点时遇到问题，可以使用某些实用程序来测试从应用控制台发出的连接。 可以使用两种控制台。 一种是 Kudu 控制台，另一种是 Azure 门户中的控制台。 若要访问应用中的 Kudu 控制台，请转到“工具”->“Kudu”。 你还可以在 [sitename] appname>.azurewebsites.net 上访问 Kudo 控制台。 网站加载后，请切换到 "调试控制台" 选项卡。若要转到 Azure 门户托管的控制台，请转到 "工具"-"> 控制台"。 

#### <a name="tools"></a>工具
由于安全约束， **ping**、 **nslookup**和**tracert**工具无法通过控制台工作。 为了填充 void，添加了两个单独的工具。 为了测试 DNS 功能，我们添加了名为 nameresolver.exe 的工具。 语法为：

    nameresolver.exe hostname [optional: DNS Server]

可以使用 **nameresolver** 来检查应用所依赖的主机名。 可以通过这种方式来测试 DNS 是否配置错误，或者测试你是否无权访问 DNS 服务器。 您可以通过查看环境变量 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER，查看应用程序在控制台中将使用的 DNS 服务器。

下一工具适用于测试与主机的 TCP 连接情况，以及端口组合情况。 该工具名为 **tcpping**，语法为：

    tcpping.exe hostname [optional: port]

**tcpping** 实用程序会告知是否可访问特定主机和端口。 仅满足以下条件才会显示成功：存在侦听主机和端口组合的应用程序，且可从应用对指定主机和端口进行网络访问。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>针对 VNet 托管的资源进行访问权限调试
许多因素会阻止应用访问特定的主机和端口。 大多数情况下为以下三种情况：

* **存在防火墙。** 如果存在防火墙，则会发生 TCP 超时。 本例中的 TCP 超时为 21 秒。 使用 **tcpping** 工具测试连接性。 除了防火墙外，还有多种原因可能导致 TCP 超时。 
* **DNS 不可访问。** DNS 超时时间为每个 DNS 服务器 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 请记住，不能使用 nslookup，因其没有使用为 VNet 配置的 DNS。 如果无法访问，则可能是防火墙或 NSG 阻止对 DNS 的访问，或者它可能已关闭。

如果这些项不能回答您的问题，请先查看以下内容： 

**区域 VNet 集成**
* 您的目标是不是 RFC1918 的地址，并且您没有将 WEBSITE_VNET_ROUTE_ALL 设置为1？
* 你的集成子网是否有 NSG 阻止出口？
* 如果要跨越 ExpressRoute 或 VPN，是否配置了本地网关，以将流量备份到 Azure？ 如果可以访问 VNet 中的终结点，但不能访问本地终结点，请检查路由。
* 你是否有足够的权限在集成子网上设置委派？ 在区域 VNet 集成配置期间，集成子网将委托给 Microsoft。 VNet 集成 UI 会自动将子网委托给 Microsoft。 如果你的帐户没有足够的网络权限来设置委派，你将需要可设置你的集成子网中的属性的用户来委派子网。 若要手动委派集成子网，请参阅 Azure 虚拟网络子网 UI，并设置 Microsoft 的委派。 

**网关所需的 VNet 集成**
* RFC 1918 范围内的点到站点地址范围（10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255）？
* 网关在门户中是否显示为已启动？ 如果网关处于关闭状态，则将其重新启动。
* 证书是否显示为同步或是否怀疑网络配置已更改？  如果你的证书不同步或者你怀疑已对 VNet 配置进行了不同步的更改，则会命中 "同步网络"。
* 如果通过 VPN，则配置了本地网关，以将流量路由到 Azure？ 如果可以访问 VNet 中的终结点，但不能访问本地终结点，请检查路由。
* 是否在尝试使用支持点到站点和 ExpressRoute 的共存网关？ VNet 集成不支持共存网关。

调试网络问题是一项挑战，因为看不到阻止访问特定主机的操作：端口组合。 部分原因包括：

* 主机上有一个防火墙，阻止从点到站点 IP 范围访问应用程序端口。 跨子网通常需要公共访问权限。
* 目标主机已关闭。
* 应用程序已关闭。
* 你的 IP 或主机名不正确。
* 您的应用程序正在侦听的端口与您预期的不同。 可以使用终结点主机上的“netstat -aon”匹配进程 ID 和侦听端口。 
* 你的网络安全组的配置方式使它们阻止从点到站点 IP 范围访问应用程序主机和端口。

请记住，您不知道您的应用程序将实际使用的地址。 它可以是集成子网或点到站点地址范围中的任何地址，因此需要允许从整个地址范围进行访问。 

其他调试步骤包括：

* 连接到 VNet 中的 VM，尝试访问资源主机：端口。 若要针对 TCP 访问权限进行测试，请使用 PowerShell 命令 test-netconnection。 语法为：

      test-netconnection hostname [optional: -Port]

* 使用**tcpping**启动 VM 上的应用程序，并从应用程序中的控制台测试对该主机和端口的访问权限

#### <a name="on-premises-resources"></a>本地资源 ####

如果应用无法访问本地资源，则请检查是否能够通过 VNet 访问该资源。 请使用 test-netconnection PowerShell 命令来针对 TCP 访问权限进行测试。 如果 VM 无法访问本地资源，则 VPN 或 ExpressRoute 连接可能配置不正确。

如果 VNet 托管的 VM 能够访问本地系统但应用无法访问，则可能是由于以下某个原因：

* 你的路由未配置为你的子网或指向本地网关中的站点地址范围。
* 网络安全组阻止访问点到站点 IP 范围。
* 本地防火墙阻止来自点到站点 IP 范围的流量。
* 正在尝试使用区域 VNet 集成功能访问非 RFC 1918 地址。