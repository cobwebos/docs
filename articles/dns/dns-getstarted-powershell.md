---
title: 快速入门：创建 Azure DNS 区域和记录 - Azure PowerShell
titleSuffix: Azure DNS
description: 了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 Azure PowerShell 创建和管理你的第一个 DNS 区域和记录的分步快速入门。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: e33f6fdba1a15032d76b94804d610e292f663d59
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "76937164"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建 Azure DNS 区域和记录

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在本快速入门中，你将使用 Azure PowerShell 创建你的第一个 DNS 区域和记录。 也可以使用 [Azure 门户](dns-getstarted-portal.md)或 [Azure CLI](dns-getstarted-cli.md) 执行这些步骤。 

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 最后，要将 DNS 区域发布到 Internet，需要为域配置名称服务器。 以下描述了上述每一个步骤。

Azure DNS 还支持创建专用域。 有关如何创建第一个专用 DNS 区域和记录的分步说明，请参阅 [Azure DNS 专用区域入门（使用 PowerShell）](private-dns-getstarted-powershell.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-resource-group"></a>创建资源组

在创建 DNS 区域之前，创建一个资源组来包含 DNS 区域：

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>创建 DNS 区域

通过使用 `New-AzDnsZone` cmdlet 创建 DNS 区域。 以下示例在名为 MyResourceGroup  的资源组中创建名为 contoso.xyz  的 DNS 区域。 使用该示例创建 DNS 区域，将相应的值替换成自己的值。

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>创建 DNS 记录

可以使用 `New-AzDnsRecordSet` cmdlet 创建记录集。 下面的示例在资源组“MyResourceGroup”的 DNS 区域“contoso.xyz”中创建相对名称为“www”的一个记录。 记录集的完全限定名称为“www.contoso.xyz”。 记录类型为“A”，IP 地址为“10.10.10.10”，TTL 为 3600 秒。

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>查看记录

若要列出区域中的 DNS 记录，请使用：

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>测试名称解析

现在，你已有一个测试 DNS 区域且其中有一个测试“A”记录，可以使用名为 *nslookup* 的工具来测试名称解析了。 

**测试 DNS 名称解析：**

1. 运行以下 cmdlet 以获取区域的名称服务器列表：

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. 从上一步的输出中复制其中一个名称服务器名称。

1. 打开一个命令提示符，并运行以下命令：

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   例如：

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   应当会看到类似以下屏幕的内容：

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

主机名 www\.contoso.xyz 解析为 10.10.10.10，正如你配置的那样   。 此结果表明名称解析正常工作。

## <a name="delete-all-resources"></a>删除所有资源

当不再需要时，可以通过删除资源组来删除本快速入门中创建的所有资源：

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

现在，你已使用 Azure PowerShell 创建了你的第一个 DNS 区域和记录，可以在自定义域中为 Web 应用创建记录了。

> [!div class="nextstepaction"]
> [在自定义域中为 web 应用创建 DNS 记录](./dns-web-sites-custom-domain.md)

