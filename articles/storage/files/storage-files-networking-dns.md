---
title: 配置 Azure 文件存储的 DNS 转发 | Microsoft Docs
description: Azure 文件存储的网络选项概述。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6404115e64ba0ac1f65ba1cfc8d26604f1ce9cfa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509959"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>配置 Azure 文件存储的 DNS 转发
使用 Azure 文件存储可为包含文件共享的存储帐户创建专用终结点。 专用终结点可在许多不同的应用场合下发挥作用，而且特别适合用于通过专用对等互连使用 VPN 或 ExpressRoute 连接从本地网络连接到 Azure 文件共享。 

若要连接到存储帐户以通过网络隧道传输数据，存储帐户的完全限定域名 (FQDN) 必须解析为专用终结点的专用 IP 地址。 若要实现此目的，必须将存储终结点后缀（公有云区域的 `core.windows.net`）转发到可从虚拟网络内部访问的 Azure 专用 DNS 服务。 本指南将介绍如何设置和配置 DNS 转发，以正确解析为存储帐户的专用终结点 IP 地址。

在完成本文中所述的步骤之前，我们强烈建议先阅读[规划 Azure 文件存储的部署](storage-files-planning.md)和 [Azure 文件存储网络注意事项](storage-files-networking-overview.md)。

## <a name="overview"></a>概述
Azure 文件存储提供两种主要类型的终结点用于访问 Azure 文件共享： 
- 公共终结点：使用公共 IP 地址，可从全球任意位置访问。
- 专用终结点：位于某个虚拟网络中，并使用该虚拟网络的地址空间内部的专用 IP 地址。

公共和专用终结点位于 Azure 存储帐户中。 存储帐户是代表共享存储池的管理结构，你可以在其中部署多个文件共享以及其他存储资源（例如，Blob 容器或队列）。

每个存储帐户具有完全限定的域名 (FQDN)。 对于公有云区域，此 FQDN 遵循 `storageaccount.file.core.windows.net` 模式，其中，`storageaccount` 是存储帐户的名称。 对此名称发出请求时（例如，使用 SMB 在工作站上装载共享），操作系统将执行 DNS 查找，以将完全限定的域名解析为 IP 地址，然后，操作系统可以使用此 IP 地址来发送 SMB 请求。

默认情况下，`storageaccount.file.core.windows.net` 解析为公共终结点的 IP 地址。 存储帐户的公共终结点托管在 Azure 存储群集上，该群集托管存储帐户的其他许多公共终结点。 创建专用终结点时，专用 DNS 区域将使用与存储帐户专用终结点专用 IP 地址的 A 记录条目建立的 CNAME 记录映射 `storageaccount.file.core.windows.net`，链接到该专用终结点所添加到的虚拟网络。 这样，你便可以在虚拟网络中使用 `storageaccount.file.core.windows.net` FQDN，并使其解析为专用终结点的 IP 地址。

由于我们的最终目标是使用 VPN 或 ExpressRoute 连接等网络隧道从本地访问存储帐户中托管的 Azure 文件共享，因此，必须将本地 DNS 服务器配置为向 Azure 专用 DNS 服务转发对 Azure 文件存储服务发出的请求。 为此，需要设置为将 `*.core.windows.net`（或美国政府、德国或中国国家云的相应存储终结点后缀）条件转发到 Azure 虚拟网络中托管的 DNS 服务器。  然后，此 DNS 服务器以递归方式将请求转发到 Azure 的专用 DNS 服务，后者将存储帐户的完全限定域名解析为相应的专用 IP 地址。

为 Azure 文件存储配置 DNS 转发需要运行一个虚拟机来托管用于转发请求的 DNS 服务器，但是，对于虚拟网络中托管的所有 Azure 文件共享，只需执行此步骤一次。 此外，这并不是 Azure 文件存储特有的要求 - 支持你要从本地访问的专用终结点的任何 Azure 服务都可以利用在本指南中配置的 DNS 转发：Azure Blob 存储、SQL Azure、Cosmos DB 等。 

本指南将介绍为 Azure 存储终结点配置 DNS 转发的步骤，因此除了 Azure 文件存储以外，针对所有其他 Azure 存储服务（Azure Blob 存储、Azure 表存储、Azure 队列存储等）的 DNS 名称解析请求将转发到 Azure 的专用 DNS 服务。 如果需要，还可以添加其他 Azure 服务的其他终结点。 此外，将会配置向本地 DNS 服务器的反向 DNS 转发，使虚拟网络中的云资源（例如 DFS-N 服务器）能够解析本地计算机名。 

## <a name="prerequisites"></a>先决条件
在设置向 Azure 文件存储的 DNS 转发之前，需要完成以下步骤：

- 包含要装载的 Azure 文件共享的存储帐户。 若要了解如何创建存储帐户和 Azure 文件共享，请参阅[创建 Azure 文件共享](storage-how-to-create-file-share.md)。
- 存储帐户的专用终结点。 若要了解如何创建 Azure 文件存储的专用终结点，请参阅[创建专用终结点](storage-files-networking-endpoints.md#create-a-private-endpoint)。
- [最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)的 Azure PowerShell 模块。

> [!Important]  
> 本指南假设在本地环境中的 Windows Server 内使用 DNS 服务器。 可以使用任何 DNS 服务器（而不仅仅是 Windows DNS 服务器）完成本指南中所述的所有步骤。

## <a name="manually-configuring-dns-forwarding"></a>手动配置 DNS 转发
如果已在 Azure 虚拟网络中配置了 DNS 服务器，或者你只是想要通过组织所用的任何方法将自己的虚拟机部署到 DNS 服务器，可以使用内置的 DNS 服务器 PowerShell cmdlet 手动配置 DNS。

在本地 DNS 服务器上，使用 `Add-DnsServerConditionalForwarderZone` 创建条件转发器。 此条件转发器必须部署在所有本地 DNS 服务器上，才能在正确将流量转发到 Azure 时产生效果。 请记得将 `<azure-dns-server-ip>` 替换为环境的相应 IP 地址。

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

在 Azure 虚拟网络中的 DNS 服务器上也需要放置一个转发器，以便将针对存储帐户 DNS 区域的请求定向到保留 IP 地址 `168.63.129.16` 后面的 Azure 专用 DNS 服务。 （如果在不同的 PowerShell 会话中运行这些命令，请记得填充 `$storageAccountEndpoint`。）

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>使用 Azure 文件混合模块配置 DNS 转发
为了尽量简化 DNS 转发的配置，我们在 Azure 文件存储混合模块中提供了自动化功能。 用于在此模块中操作 DNS 的 cmdlet 可帮助你在 Azure 虚拟网络中部署 DNS 服务器，并将本地 DNS 服务器更新为转发到这些服务器。 

如果你未曾使用过 Azure 文件存储混合模块，必须先在工作站上安装此模块。 下载[最新版本](https://github.com/Azure-Samples/azure-files-samples/releases)的 Azure 文件存储混合 PowerShell 模块：

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

部署 DNS 转发解决方案分为两个步骤：创建 DNS 转发规则集（定义要将请求转发到哪些 Azure 服务），以及实际部署 DNS 转发器。 

以下示例将请求转发到存储帐户，包括针对 Azure 文件存储、Azure Blob 存储、Azure 表存储和 Azure 队列存储的请求。 如果需要，可以通过 `New-AzDnsForwardingRuleSet` cmdlet 的 `-AzureEndpoints` 参数在规则中添加针对更多 Azure 服务的转发。 请记得将 `<virtual-network-resource-group>`、`<virtual-network-name>` 和 `<subnet-name>` 替换为适合你的环境的值。

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

此外，你可能发现提供多个附加参数会很有用/有必要：

| 参数名称 | 类型 | 说明 |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | 默认情况下，DNS 服务器将部署到虚拟网络所在的同一资源组中。 如果不需要部署到同一资源组，此参数允许你选择要将 DNS 服务器部署到的备选资源组。 |
| `DnsForwarderRootName` | `string` | 默认情况下，在 Azure 中部署的 DNS 服务器使用名称 `DnsFwder-*`，其中的星号由迭代器填充。 此参数更改该名称的根（即 `DnsFwder`）。 |
| `VmTemporaryPassword` | `SecureString` | 默认情况下，系统会为 VM 在加入域之前使用的临时默认帐户选择一个随机密码。 VM 加入域后，将禁用默认帐户。 |
| `DomainToJoin` | `string` | 要将 DNS VM 加入到的域。 默认情况下，系统会根据运行 cmdlet 所在的计算机的域选择此域。 |
| `DnsForwarderRedundancyCount` | `int` | 要为虚拟网络部署的 DNS VM 数目。 默认情况下，`New-AzDnsForwarder` 会在 Azure 虚拟网络中的某个可用性集内部署两个 DNS 服务器，以确保冗余。 可根据需要修改此数字。 |
| `OnPremDnsHostNames` | `HashSet<string>` | 手动指定的本地 DNS 主机名列表，将基于这些主机名创建转发器。 不想要在所有本地 DNS 服务器上应用转发器时（例如，当一系列客户端使用手动指定的 DNS 名称时），此参数非常有用。 |
| `Credential` | `PSCredential` | 更新 DNS 服务器时要使用的凭据。 用于登录的用户帐户无权修改 DNS 设置时，此参数非常有用。 |
| `SkipParentDomain` | `SwitchParameter` | 默认情况下，DNS 转发器将应用到环境中级别最高的域。 例如，如果 `northamerica.corp.contoso.com` 是 `corp.contoso.com` 的子域，则将为 `corp.contoso.com` 关联的 DNS 服务器创建转发器。 使用此参数会在 `northamerica.corp.contoso.com` 中创建转发器。 |

## <a name="confirm-dns-forwarders"></a>确认 DNS 转发器
在测试是否已成功应用 DNS 转发器之前，我们建议使用 `Clear-DnsClientCache` 清除本地工作站上的 DNS 缓存。 若要测试是否可以成功解析存储帐户的完全限定域名，请使用 `Resolve-DnsName` 或 `nslookup`。

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

如果名称解析成功，应会看到解析的 IP 地址与存储帐户的 IP 地址相匹配。

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

如果已设置 VPN 或 ExpressRoute 连接，则还可以使用 `Test-NetConnection` 来确认是否可以成功地与存储帐户建立 TCP 连接。

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>另请参阅
- [规划 Azure 文件部署](storage-files-planning.md)
- [Azure 文件存储的网络注意事项](storage-files-networking-overview.md)
- [配置 Azure 文件存储网络终结点](storage-files-networking-endpoints.md)