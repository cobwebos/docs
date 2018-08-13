---
title: 教程 - 为 Web 应用创建自定义 Azure DNS 记录
description: 在本教程中，将使用 Azure DNS 为 Web 应用创建自定义域 DNS 记录。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 7/20/2018
ms.author: victorh
ms.openlocfilehash: b39c2c672869bb446e58134a85130d10491fe047
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621107"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>教程：为 Web 应用在自定义域中创建 DNS 记录 

可以配置 Azure DNS 来托管 Web 应用的自定义域。 例如，可以创建一个 Azure Web 应用，并让用户使用 www.contoso.com 或 contoso.com 作为完全限定的域名 (FQDN) 访问它。

> [!NOTE]
> 本教程通篇都使用 Contoso.com 作为示例。 请将 contoso.com 替换为你自己的域名。

为此，必须创建三条记录：

* 指向 contoso.com 的根 A 记录
* 用于验证的根 TXT 记录
* 指向 A 记录的 www 名称的 CNAME 记录

请记住，如果在 Azure 中为 Web 应用创建 A 记录，当 Web 应用的基础 IP 地址更改时，则必须手动更新 A 记录。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为自定义域创建 A 记录和 TXT 记录
> * 创建自定义域的 CNAME 记录
> * 测试新记录
> * 为 Web 应用添加自定义主机名
> * 测试自定义主机名


如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>先决条件

- [创建应用服务应用](../app-service/app-service-web-get-started-html.md)，或使用为另一教程创建的应用。

- 在 Azure DNS 中创建一个 DNS 区域，并将注册机构中的区域委派给 Azure DNS。

   1. 若要创建 DNS 区域，请按照[创建 DNS 区域](dns-getstarted-create-dnszone.md)中的步骤执行操作。
   2. 若要将区域委派给 Azure DNS，请按照 [DNS 域委派](dns-domain-delegation.md)中的步骤执行操作。

在创建区域并将它委派给 Azure DNS 之后，可以为自定义域创建记录。

## <a name="create-an-a-record-and-txt-record"></a>创建一个 A 记录和 TXT 记录

A 记录可用于将名称映射到其 IP 地址。 在下面的示例中，使用你的 Web 应用 IPv4 地址将“\@”分配为 A 记录。 \@ 通常表示根域。

### <a name="get-the-ipv4-address"></a>获取 IPv4 地址

在 Azure 门户中，在应用服务页的左侧导航窗格中，选择“自定义域”。 

![自定义域菜单](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

在“自定义域”页中，复制应用的 IPv4 地址：

![在门户中导航到 Azure 应用](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>创建 A 记录

```powershell
New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>创建 TXT 记录

应用服务仅在配置时使用此记录来验证你是否拥有自定义域。 自定义域经过验证并且在应用服务中配置后，可以删除此 TXT 记录。

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name `"@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>创建 CNAME 记录

如果域已由 Azure DNS 管理（请参阅 [DNS 域委派](dns-domain-delegation.md)），可以使用下面的示例，创建 contoso.azurewebsites.net 的 CNAME 记录。

打开 Azure PowerShell 并创建一条新的 CNAME 记录。 此示例使用 Web 应用 contoso.azurewebsites.net 的别名在名为“contoso.com”的 DNS 区域中创建一个“生存时间”为 600 秒的记录集类型 CNAME。

### <a name="create-the-record"></a>创建记录

```powershell
New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzureRmDnsRecordConfig -cname "contoso.azurewebsites.net")
```

以下示例是响应：

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>测试新记录

可以通过使用 nslookup 查询“www.contoso.com”和“contoso.com”来验证记录是否已正确创建，如下所示：

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>添加自定义主机名

现在，可以向 Web 应用添加自定义主机名：

```powershell
set-AzureRmWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>测试自定义主机名

打开浏览器并浏览到 `http://www.<your domainname>` 和 `http://<you domain name>`。

> [!NOTE]
> 请务必包括 `http://` 前缀，否则浏览器可能会尝试替你预测 URL！

对于上述两个 URL，应当会看到相同的页面。 例如：

![Contoso 应用服务](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>清理资源

当不再需要本教程中创建的资源时，可以删除 **myresourcegroup** 资源组。

## <a name="next-steps"></a>后续步骤

了解如何创建 Azure DNS 专用区域。

> [!div class="nextstepaction"]
> [Azure DNS 专用区域入门（使用 PowerShell）](private-dns-getstarted-powershell.md)
