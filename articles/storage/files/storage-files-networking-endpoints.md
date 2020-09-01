---
title: 配置 Azure 文件存储网络终结点 | Microsoft Docs
description: Azure 文件存储的网络选项概述。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a38528e32061f57e3239ef4be26cdd437f4f8746
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078586"
---
# <a name="configuring-azure-files-network-endpoints"></a>配置 Azure 文件存储网络终结点

Azure 文件存储提供两种主要类型的终结点用于访问 Azure 文件共享： 
- 公共终结点：使用公共 IP 地址，可从全球任意位置访问。
- 专用终结点：位于某个虚拟网络中，并使用该虚拟网络的地址空间内部的专用 IP 地址。

公共和专用终结点位于 Azure 存储帐户中。 存储帐户是代表共享存储池的管理结构，你可以在其中部署多个文件共享以及其他存储资源（例如，Blob 容器或队列）。

本文重点介绍如何配置存储帐户的终结点，以便直接访问 Azure 文件共享。 本文档中提供的大部分详细信息也适用于 Azure 文件同步与存储帐户公共和专用终结点的互操作方式，但有关 Azure 文件同步部署网络注意事项的详细信息，请参阅[配置 Azure 文件同步代理和防火墙设置](storage-sync-files-firewall-and-proxy.md)。

在阅读本操作指南之前，我们建议先阅读 [Azure 文件存储的网络注意事项](storage-files-networking-overview.md)。

## <a name="prerequisites"></a>先决条件

- 本文假设已创建一个 Azure 订阅。 如果还没有订阅，则请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本文假设你已在要从本地连接到的存储帐户中创建了 Azure 文件共享。 若要了解如何创建 Azure 文件共享，请参阅[创建 Azure 文件共享](storage-how-to-create-file-share.md)。
- 如果你打算使用 Azure PowerShell，请[安装最新版本](https://docs.microsoft.com/powershell/azure/install-az-ps)。
- 如果你打算使用 Azure CLI，请[安装最新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="endpoint-configurations"></a>终结点配置

你可以配置终结点，以限制对你的存储帐户的网络访问。 可通过两种方法来仅限虚拟网络访问存储帐户：

- [为存储帐户创建一个或多个专用终结点](#create-a-private-endpoint)，并限制对公共终结点的所有访问。 这可以确保只有源自所需虚拟网络内部的流量才能访问存储帐户中的 Azure 文件共享。
- [将公共终结点限制为一个或多个虚拟网络](#restrict-public-endpoint-access)。 为此，可以使用称作“服务终结点”的虚拟网络功能。 当你通过服务终结点将流量限制到存储帐户时，你仍然可以通过公共 IP 地址访问存储帐户，但只能从你在配置中指定的位置访问。

### <a name="create-a-private-endpoint"></a>创建专用终结点

为存储帐户创建专用终结点会部署以下 Azure 资源：

- **一个专用终结点**：表示存储帐户专用终结点的 Azure 资源。 可将此资源视为连接存储帐户和网络接口的资源。
- **一个网络接口 (NIC)** ：在指定的虚拟网络/子网中保留专用 IP 地址的网络接口。 此网络接口是部署虚拟机时部署的同一个资源，但它不会分配到 VM，而是由专用终结点拥有。
- **一个专用 DNS 区域**：如果你以前从未为此虚拟网络部署过专用终结点，系统将为虚拟网络部署新的专用 DNS 区域。 此外，将为此 DNS 区域中的存储帐户创建 DNS A 记录。 如果已在此虚拟网络中部署了专用终结点，则会将存储帐户的新 A 记录添加到现有 DNS 区域。 部署 DNS 区域的操作是可选的，但我们强烈建议执行此操作；如果使用 AD 服务主体或 FileREST API 装载 Azure 文件共享，则此操作是必需的。

> [!Note]  
> 本文将使用 Azure 公共区域的存储帐户 DNS 后缀 `core.windows.net`。 此段注释也适用于 Azure 主权云（例如 Azure 美国政府云和 Azure 中国云）- 只需根据环境替换相应的后缀即可。 

# <a name="portal"></a>[门户](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

如果你在虚拟网络中有一个虚拟机，或者已按[配置 Azure 文件存储的 DNS 转发](storage-files-networking-dns.md)所述配置了 DNS 转发，则可以通过在 PowerShell、命令行或终端（适用于 Windows、Linux 或 macOS）中运行以下命令，来测试是否已正确设置专用终结点。 必须将 `<storage-account-name>` 替换为相应的存储帐户名称：

```
nslookup <storage-account-name>.file.core.windows.net
```

如果一切成功进行，则应会看到以下输出，其中 `192.168.0.5` 是虚拟网络中专用终结点的专用 IP 地址（Windows 中显示的输出）：

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

如果你在虚拟网络中有一个虚拟机，或者已按[配置 Azure 文件存储的 DNS 转发](storage-files-networking-dns.md)所述配置了 DNS 转发，则可以使用以下命令测试是否已正确设置专用终结点：

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

如果一切成功进行，则应会看到以下输出，其中 `192.168.0.5` 是虚拟网络中专用终结点的专用 IP 地址：

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

如果你在虚拟网络中有一个虚拟机，或者已按[配置 Azure 文件存储的 DNS 转发](storage-files-networking-dns.md)所述配置了 DNS 转发，则可以使用以下命令测试是否已正确设置专用终结点：

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

如果一切成功进行，则应会看到以下输出，其中 `192.168.0.5` 是虚拟网络中专用终结点的专用 IP 地址。 你仍应使用 storageaccount.file.core.windows.net 来装载文件共享，而不是 `privatelink` 路径。

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="restrict-public-endpoint-access"></a>限制公共终结点访问

限制公共终结点访问首先要求禁用公共终结点的常规访问权限。 禁用对公共终结点的访问不会影响私有终结点。 禁用公共终结点后，可以选择可以继续访问的特定网络或 IP 地址。 通常，存储帐户的大多数防火墙策略限制了对一个或多个虚拟网络的网络访问。

#### <a name="disable-access-to-the-public-endpoint"></a>禁止对公共终结点的访问

禁止对公共终结点的访问时，仍可通过存储帐户的专用终结点来访问该存储帐户。 否则，将拒绝对存储帐户的公共终结点的有效请求，除非它们来自 [特定允许的源](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)。 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>仅限从特定的虚拟网络访问公共终结点

如果仅限从特定的虚拟网络访问存储帐户，则会允许从指定的虚拟网络内部对公共终结点发出请求。 为此，可以使用称作“服务终结点”的虚拟网络功能。 在具有或没有专用终结点的情况下都可以使用此功能。

# <a name="portal"></a>[门户](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>另请参阅

- [Azure 文件存储的网络注意事项](storage-files-networking-overview.md)
- [配置 Azure 文件存储的 DNS 转发](storage-files-networking-dns.md)
- [配置 Azure 文件存储的 S2S VPN](storage-files-configure-s2s-vpn.md)
