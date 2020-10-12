---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: b62e5057d8f144fc56d0e35927d17de27a1c8863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91255227"
---
虽然此功能很容易设置，但这并不意味着你的体验不会遇到任何问题。 如果在访问所需终结点时遇到问题，可以使用某些实用程序来测试从应用控制台发出的连接。 可以使用两种控制台。 一种是 Kudu 控制台，另一种是 Azure 门户中的控制台。 若要访问应用中的 Kudu 控制台，请转到“工具” > “Kudu”。 此外，还可以通过 [sitename].scm.azurewebsites.net 访问 Kudo 控制台。 打开网站负载后，转到“调试控制台”选项卡。若要从应用访问 Azure 门户托管的控制台，请转到“工具” > “控制台”。

#### <a name="tools"></a>工具
在本机 Windows 应用程序中，工具 **ping**、 **nslookup**和 **tracert** 不会通过控制台工作，因为它们 (它们在 [自定义 Windows 容器](../articles/app-service/quickstart-custom-container.md)) 中工作。 为了填补此空白，我们添加了两个单独的工具。 我们添加了名为 nameresolver.exe 的工具，用于测试 DNS 功能。 语法为：

```console
nameresolver.exe hostname [optional: DNS Server]
```

可以使用 nameresolver 来检查应用所需的主机名。 可以通过这种方式来测试 DNS 是否配置错误，或者测试你是否有权访问 DNS 服务器。 若要了解可供应用在控制台中使用的 DNS 服务器，请查看环境变量 WEBSITE_DNS_SERVER 和 WEBSITE_DNS_ALT_SERVER。

> [!NOTE]
> nameresolver.exe 当前不适用于自定义 Windows 容器。
>

可以使用下一工具测试与主机的 TCP 连接情况，以及端口组合情况。 该工具名为 **tcpping**，语法为：

```console
tcpping.exe hostname [optional: port]
```

**tcpping** 实用程序会告知是否可访问特定主机和端口。 只有满足以下条件才会显示成功：存在侦听主机和端口组合的应用程序，且可从应用对指定主机和端口进行网络访问。

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>调试对虚拟网络托管的资源的访问
许多因素可能会阻止应用访问特定的主机和端口。 大多数情况下为以下因素之一：

* **存在防火墙。** 如果存在防火墙，则会发生 TCP 超时。 本例中的 TCP 超时为 21 秒。 使用 **tcpping** 工具测试连接性。 除了防火墙外，还有多种原因可能导致 TCP 超时。
* **DNS 不可访问。** 每个 DNS 服务器的 DNS 超时为 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 不能使用 nslookup，因为它不使用为虚拟网络配置的 DNS。 如果无法访问，则表明可能有防火墙或 NSG 在阻止对 DNS 的访问，或者该 DNS 可能已停机。

如果这些方法未解决问题，请首先检查以下因素：

**区域 VNet 集成**
* 你的目标是不是 RFC1918 的地址，并且未将 WEBSITE_VNET_ROUTE_ALL 设置为1？
* 你的集成子网是否有 NSG 阻止出口？
* 如果要跨越 Azure ExpressRoute 或 VPN，是否配置了本地网关，以将流量重新路由到 Azure？ 如果可以访问虚拟网络中的终结点，但不能访问本地的终结点，请检查路由。
* 你是否有足够的权限在集成子网上设置委派？ 在区域 VNet 集成配置期间，集成子网将委托给 Microsoft。 VNet 集成 UI 会自动将子网委托给 Microsoft。 如果你的帐户没有足够的网络权限来设置委派，你将需要可设置集成子网中的属性的用户来委派子网。 若要手动委派集成子网，请参阅 Azure 虚拟网络子网 UI，并设置 Microsoft 的委派。

**需要网关的 VNet 集成**
* 点到站点地址范围是否在 RFC 1918 范围内 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255)？
* 网关在门户中是否显示为已启动？ 如果网关处于关闭状态，则将其重新启动。
* 证书是否显示正在同步？或者，你是否怀疑网络配置已更改？  如果证书未同步，或者你怀疑对虚拟网络配置做出了与 ASP 不同步的更改，请选择“同步网络”。
* 如果通过 VPN 传输，本地网关是否配置为将流量路由回 Azure？ 如果可以访问虚拟网络中的终结点，但不能访问本地的终结点，请检查路由。
* 你是否正在尝试使用一个既支持点到站点连接，又支持 ExpressRoute 的共存网关？ VNet 集成不支持共存网关。

调试网络问题很有难度，因为你看不到哪些因素在阻止访问特定的“主机:端口”组合。 部分原因包括：

* 在主机上开启了防火墙，导致无法从点到站点 IP 范围访问应用程序端口。 跨子网通常需要公共访问权限。
* 目标主机已关闭。
* 应用程序已关闭。
* IP 或主机名错误。
* 应用程序所侦听的端口与你预期的端口不同。 可以使用终结点主机上的“netstat -aon”匹配进程 ID 和侦听端口。
* 网络安全组的配置方式导致无法从点到站点 IP 范围访问应用程序主机和端口。

你不知道应用实际使用的地址。 它可能是集成子网中或点到站点地址范围内的任意地址，因此你需要允许从整个地址范围进行访问。

其他调试步骤包括：

* 连接到虚拟网络中的某个 VM，尝试在该处访问资源主机:端口。 若要针对 TCP 访问权限进行测试，请使用 PowerShell 命令 test-netconnection。 语法为：

```powershell
test-netconnection hostname [optional: -Port]
```

* 在某个 VM 中启动应用程序，然后使用 tcpping 测试能否在应用的控制台中访问该主机和端口。

#### <a name="on-premises-resources"></a>本地资源 ####

如果应用无法访问本地资源，请检查是否能够通过虚拟网络访问该资源。 请使用 test-netconnection PowerShell 命令来针对 TCP 访问权限进行测试。 如果 VM 无法访问本地资源，原因可能是未正确配置 VPN 或 ExpressRoute 连接。

如果虚拟网络托管的 VM 能够访问本地系统但应用无法访问，则可能是由于以下某个原因：

* 在本地网关中未使用子网或点到站点地址范围配置路由。
* 网络安全组阻止点到站点 IP 范围的访问。
* 本地防火墙阻止来自点到站点 IP 范围的流量。
* 正在尝试使用区域 VNet 集成功能访问非 RFC 1918 地址。