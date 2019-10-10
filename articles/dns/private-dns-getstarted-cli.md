---
title: 快速入门 - 使用 Azure CLI 创建 Azure 专用 DNS 区域
description: 在本快速入门中，将在 Azure DNS 中创建并测试专用 DNS 区域和记录。 这是有关使用 Azure CLI 创建和管理你的第一个专用 DNS 区域和记录的分步指南。
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: 6f5f2263736e1317ca40e6c27a909be2aa106270
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960434"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure 专用 DNS 区域

本快速入门将引导你完成使用 Azure CLI 创建第一个专用 DNS 区域和记录的步骤。

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 若要向虚拟网络发布专用 DNS 区域，请指定一个列表，其中包含允许在区域中解析记录的虚拟网络。  这些虚拟网络称为链接的虚拟网络。  启用自动注册后，Azure DNS 还会在创建虚拟机、更改其 IP 地址或删除虚拟机时更新区域记录。

此快速入门介绍如何：

> [!div class="checklist"]
> * 创建专用 DNS 区域
> * 创建测试虚拟机
> * 创建额外的 DNS 记录
> * 测试专用区域

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果你愿意，可以使用 [Azure PowerShell](private-dns-getstarted-powershell.md) 完成本快速入门中的步骤。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>创建资源组

首先，创建一个资源组，使之包含 DNS 区域： 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-private-dns-zone"></a>创建专用 DNS 区域

以下示例创建一个名为“myAzureVNet”的虚拟网络  。 然后，它在 **MyAzureResourceGroup** 资源组中创建一个名为 **private.contoso.com** 的 DNS 区域，将该 DNS 区域链接到 **MyAzureVnet** 虚拟网络，并启用自动注册。

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

如果要创建仅用于名称解析的区域（不自动注册主机名），则可以使用 `-e false` 参数。

### <a name="list-dns-private-zones"></a>列出 Azure DNS 专用区域

若要枚举 DNS 区域，请使用 `az network private-dns zone list`。 有关帮助，请参阅 `az network dns zone list --help`。

指定资源组仅列出资源组内的区域：

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

省略资源组可列出订阅中的所有区域：

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>创建测试虚拟机

现在，创建两台虚拟机，以便可以测试专用 DNS 区域：

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

完成此设置可能需要几分钟时间。

## <a name="create-an-additional-dns-record"></a>创建额外的 DNS 记录

若要创建 DNS 记录，请使用 `az network private-dns record-set [record type] add-record` 命令。 例如，如果在添加 A 记录时需要帮助，请参阅 `az network private-dns record-set A add-record --help`。

 下面的示例在 DNS 区域 **private.contoso.com** 的资源组 **MyAzureResourceGroup** 中创建相对名称为 **db** 的一个记录。 记录集的完全限定名称为 **db.private.contoso.com**。 记录类型为“A”，IP 地址为“10.2.0.4”。

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>查看 DNS 记录

若要列出区域中的 DNS 记录，请运行：

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>测试专用区域

现在，可以测试 **private.contoso.com** 专用区域的名称解析。

### <a name="configure-vms-to-allow-inbound-icmp"></a>将 VM 配置为允许入站 ICMP

可以使用 ping 命令来测试名称解析。 因此，在两台虚拟机上都将防火墙配置为允许入站 ICMP 数据包。

1. 连接到 myVM01，使用管理员权限打开 Windows PowerShell 窗口。
2. 运行以下命令：

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

针对 myVM02 重复上述操作。

### <a name="ping-the-vms-by-name"></a>按名称对 VM 执行 ping 命令

1. 从 myVM02 Windows PowerShell 命令提示符下，使用自动注册的主机名对 myVM01 执行 ping 命令：

   ```
   ping myVM01.private.contoso.com
   ```

   应当会看到与以下内容类似的输出：

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. 现在，对之前创建的 **db** 名称执行 ping 命令：

   ```
   ping db.private.contoso.com
   ```

   应当会看到与以下内容类似的输出：

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>删除所有资源

不再需要时，可以通过删除 **MyAzureResourceGroup** 资源组来删除在本快速入门中创建的资源。

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure DNS 专用区域方案](private-dns-scenarios.md)

