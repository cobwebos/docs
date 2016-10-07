<properties
   pageTitle="使用 CLI 创建 DNS 区域 | Microsoft Azure"
   description="了解如何逐步为 Azure DNS 创建 DNS 区域，开始使用 CLI 托管 DNS 域"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# 使用 CLI 创建 Azure DNS 区域


> [AZURE.SELECTOR]
- [Azure 门户](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)


本文将逐步引导完成使用 CLI 创建 DNS 区域。还可以使用 PowerShell 或 Azure 门户创建 DNS 区域。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## 开始之前

这些说明使用 Microsoft Azure CLI。请确保更新至最新的 Azure CLI（0.9.8 或更高版本），使用 Azure DNS 命令。键入 `azure -v`检查计算机中当前安装的 Azure CLI 版本。

## 步骤 1 - 设置 Azure CLI

### 1\.安装 Azure CLI

可以在 Windows、Linux 或 Mac 上安装 Azure CLI。使用 Azure CLI 管理 Azure DNS 前，需要完成以下步骤。有关详细信息，请参阅[安装 Azure CLI](../xplat-cli-install.md)。DNS 命令要求使用 Azure CLI 0.9.8 版或更高版本。

CLI 上的所有网络提供程序命令都可以使用以下命令找到：

	Azure network

### 2\.切换 CLI 模式

Azure DNS 使用 Azure Resource Manager。确保切换 CLI 模式，使用 ARM 命令。

	Azure config mode arm

### 3\.登录到 Azure 帐户

系统将提示你使用凭据进行身份验证。记住，只能使用 ORGID 帐户。

    Azure login -u "username"

### 4\.选择订阅

选择要使用的 Azure 订阅。

    Azure account set "subscription name"

### 5\.创建资源组

Azure 资源管理器要求所有资源组指定一个位置。此位置将用作该资源组中的资源的默认位置。但是，由于所有 DNS 资源都是全局性而非区域性的，因此资源组位置的选择不会影响 Azure DNS。

如果使用现有资源组，可跳过此步骤。

    Azure group create -n myresourcegroup --location "West US"


### 6\.注册

Azure DNS 服务由 Microsoft.Network 资源提供程序管理。使用 Azure DNS 前，需要将 Azure 订阅注册为使用此资源提供程序。对每个订阅而言，这都是一次性操作。

	Azure provider register --namespace Microsoft.Network


## 步骤 2 - 创建 DNS 区域

使用 `azure network dns zone create` 命令创建 DNS 区域。可以选择同时创建 DNS 区域和标记。标记是名称/值列表，Azure Resource Manager 利用它们来标记资源，进行计费和分组。有关标记的详细信息，请参阅[使用标记来组织 Azure 资源](../resource-group-using-tags.md)。

在 Azure DNS 中，区域名称的指定不应以指“.”结尾。例如，“contoso.com”是正确的，而 “contoso.com.”是错误的。


### 创建 DNS 区域

下面的示例在名为“MyResourceGroup”的资源组中创建了一个名为“contoso.com”的 DNS 区域。

使用该示例创建 DNS 区域，将相应的值替换为自己的值。

    Azure network dns zone create myresourcegroup contoso.com

### 创建 DNS 区域和标记。

Azure DNS CLI 支持通过可选的 *-Tag* 参数指定的 DNS 区域标记。下面的示例演示了如何使用两个标记（project = demo 和 env = test）来创建 DNS 区域。

使用该示例创建 DNS 区域和标记，将相应的值替换为自己的值。

	Azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## 查看记录

创建 DNS 区域还会创建以下 DNS 记录：

- “授权起始点”(SOA) 记录。此记录位于每个 DNS 区域的根目录。

- 授权名称服务器 (NS) 记录。此记录显示托管该区域的名称服务器。Azure DNS 使用名称服务器池，因此 Azure DNS 中的不同区域可以分配不同的名称服务器。有关详细信息，请参阅[向 Azure DNS 委托域](dns-domain-delegation.md)。

要查看这些记录，请使用 `azure network dns-record-set show`。<BR> *用法：network dns record-set show <资源组> <DNS 区域名称> <名称> <类型>*


在下面的示例中，如果运行的命令带资源组 *myresourcegroup*、记录集名称 *"@"*（针对根记录）以及类型 *SOA*，则将生成以下输出：


	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR>要查看随区域一起创建的 NS 记录，请使用以下命令：

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE] DNS 区域根目录（或*顶点*）的记录集使用 **@** 作为记录集名称。

## 测试

可以使用 DNS 工具测试 DNS 区域，如 nslookup、DIG 或 `Resolve-DnsName` PowerShell cmdlet。

如果尚未委托域而在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。区域的名称服务器在 NS 记录中给出，如上面的“azure network dns record-set show”所列。请确保在下面的命令中将相应的值替换为区域的正确值。

下面的示例使用分配给 DNS 区域的名称服务器，通过 DIG 来查询域 contoso.com。该查询必须通过 DIG 指向使用 *@<区域的名称服务器>* 的名称服务器和区域名称。

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## 后续步骤

创建 DNS 区域后，创建[记录集和记录](dns-getstarted-create-recordset-cli.md)，开始解析 Internet 域的名称。

<!---HONumber=AcomDC_0921_2016-->