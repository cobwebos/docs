---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671482"
---
虽然此功能容易设置，但这并不意味着你就不会遇到问题。 如果在访问所需终结点时遇到问题，可以使用某些实用程序来测试从应用控制台发出的连接。 可以使用两种控制台。 一种是 Kudu 控制台，另一种是 Azure 门户中的控制台。 若要访问应用中的 Kudu 控制台，请转到“工具”->“Kudu”。 您还可以在 [站点名称]scm.azuresite.net 上访问 Kudo 控制台。 网站加载后，转到调试控制台选项卡。要访问 Azure 门户托管控制台，请从应用转到工具 ->控制台。 

#### <a name="tools"></a>工具
由于安全限制 **，ping、nslook****和** **tracert**的工具无法通过控制台工作。 为了填补空白，添加了两个单独的工具。 为了测试 DNS 功能，我们添加了名为 nameresolver.exe 的工具。 语法为：

    nameresolver.exe hostname [optional: DNS Server]

可以使用 **nameresolver** 来检查应用所依赖的主机名。 可以通过这种方式来测试 DNS 是否配置错误，或者测试你是否无权访问 DNS 服务器。 若要了解可供应用在控制台中使用的 DNS 服务器，请查看环境变量 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER。

下一工具适用于测试与主机的 TCP 连接情况，以及端口组合情况。 该工具名为 **tcpping**，语法为：

    tcpping.exe hostname [optional: port]

**tcpping** 实用程序会告知是否可访问特定主机和端口。 仅满足以下条件才会显示成功：存在侦听主机和端口组合的应用程序，且可从应用对指定主机和端口进行网络访问。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>针对 VNet 托管的资源进行访问权限调试
许多因素会阻止应用访问特定的主机和端口。 大多数情况下为以下三种情况：

* **存在防火墙。** 如果存在防火墙，则会发生 TCP 超时。 本例中的 TCP 超时为 21 秒。 使用 **tcpping** 工具测试连接性。 除了防火墙外，还有多种原因可能导致 TCP 超时。 
* **DNS 不可访问。** DNS 超时时间为每个 DNS 服务器 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 请记住，不能使用 nslookup，因其没有使用为 VNet 配置的 DNS。 如果无法访问，则表明可能有防火墙或 NSG 在阻止对 DNS 的访问，或者该 DNS 可能已停机。

如果这些方法未解决问题，请首先检查以下因素： 

**区域 VNet 集成**
* 您的目的地是否为非 RFC1918 地址，而您没有将WEBSITE_VNET_ROUTE_ALL设置为 1？
* 是否有 NSG 阻止来自集成子网的退出？
* 如果通过 ExpressRoute 或 VPN 传输，本地网关是否配置为将流量路由回 Azure？ 如果可以到达 VNet 中的终结点，但不能到达本地，请检查路由。
* 您是否有足够的权限在集成子网上设置委派？ 在区域 VNet 集成配置期间，集成子网将委派给 Microsoft.Web。 VNet 集成 UI 将自动将子网委派给 Microsoft.Web。 如果您的帐户没有足够的网络权限来设置委派，则需要可以在集成子网上设置属性的人员来委派子网。 要手动委派集成子网，请访问 Azure 虚拟网络子网 UI 并为 Microsoft.Web 设置委派。 

**网关需要 VNet 集成**
* 点到站点地址范围是否在 RFC 1918 范围内 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)？
* 网关在门户中是否显示为已启动？ 如果网关处于关闭状态，则将其重新启动。
* 证书是否显示正在同步，或者，你是否怀疑网络配置已更改？  如果证书未同步，或者你怀疑 VNet 配置存在与 ASP 不同步的更改，请单击“同步网络”。
* 如果穿过 VPN，是否配置本地网关将流量路由回 Azure？ 如果可以到达 VNet 中的终结点，但不能到达本地，请检查路由。
* 您是否正在尝试使用同时支持指向站点和 ExpressRoute 的共存网关？ VNet 集成不支持共存网关。

调试网络问题很有难度，因为你看不到哪些因素正在阻止访问特定的“主机:端口”组合。 部分原因包括：

* 主机上安装了防火墙，阻止从点到站点 IP 范围访问应用程序端口。 跨子网通常需要公共访问权限。
* 目标主机已关闭。
* 您的应用程序已关闭。
* 您拥有错误的 IP 或主机名。
* 您的应用程序侦听的端口与预期不同。 可以使用终结点主机上的“netstat -aon”匹配进程 ID 和侦听端口。 
* 网络安全组的配置方式使网络安全组无法阻止从点到站点 IP 范围访问应用程序主机和端口。

请记住，你并不知道应用实际使用的地址。 它可能是集成子网中或点到站点地址范围内的任意地址，因此你需要允许从整个地址范围进行访问。 

其他调试步骤包括：

* 连接到 VNet 中的 VM 并尝试从那里访问资源主机：端口。 要测试 TCP 访问，请使用 PowerShell 命令**测试网连接**。 语法为：

      test-netconnection hostname [optional: -Port]

* 在 VM 上启动应用程序，并使用**tcpping**从应用的控制台测试对该主机和端口的访问

#### <a name="on-premises-resources"></a>本地资源 ####

如果应用无法访问本地资源，则请检查是否能够通过 VNet 访问该资源。 请使用 test-netconnection PowerShell 命令来针对 TCP 访问权限进行测试****。 如果 VM 无法访问本地资源，原因可能是未正确配置 VPN 或 ExpressRoute 连接。

如果 VNet 托管的 VM 能够访问本地系统但应用无法访问，则可能是由于以下某个原因：

* 您的路由未配置与本地网关中的子网或指向站点地址范围。
* 您的网络安全组正在阻止对站点 IP 范围的访问。
* 本地防火墙正在阻止来自点对点 IP 范围的流量。
* 您正在尝试使用区域 VNet 集成功能访问非 RFC 1918 地址。