---
title: "为 Web 应用创建自定义 DNS 记录 | Microsoft Docs"
description: "如何使用 Azure DNS 为 Web 应用创建自定义域 DNS 记录。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: a2a429873c30f526a0de05d4018f53f3a83bbe28

---

# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>在自定义域中为 Web 应用创建 DNS 记录

你可以使用 Azure DNS 为 Web 应用托管自定义域。 例如，你正在创建一个 Azure Web 应用，并希望用户通过将 contoso.com 或 www.contoso.com 用作 FQDN 来访问它。

为此，必须创建两条记录：

* 指向 contoso.com 的根 A 记录
* 指向 A 记录的 www 名称的 CNAME 记录

请记住，如果在 Azure 中为 Web 应用创建 A 记录，当 Web 应用的基础 IP 地址更改时，则必须手动更新 A 记录。

## <a name="before-you-begin"></a>开始之前

开始之前，必须先在 Azure DNS 中创建一个 DNS 区域，并将注册机构中的区域委派给 Azure DNS。

1. 若要创建 DNS 区域，请按照[创建 DNS 区域](dns-getstarted-create-dnszone.md)中的步骤执行操作。
2. 若要将你的 DNS 委派给 Azure DNS，请按照 [DNS 域委派](dns-domain-delegation.md)中的步骤执行操作。

在创建区域并将它委派给 Azure DNS 之后，可以为自定义域创建记录。

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1.为自定义域创建 A 记录

A 记录可用于将名称映射到其 IP 地址。 在下面的示例中，我们会将 @ 作为 A 记录分配给 IPv4 地址：

### <a name="step-1"></a>步骤 1

创建一条 A 记录并将其分配给变量 $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>步骤 2

使用分配的 $rs 变量将 IPv4 值添加到以前创建的记录集 "@"。 分配的 IPv4 值将是 Web 应用的 IP 地址。

若要查找 Web 应用的 IP 地址，请按照[在 Azure App Service 中配置自定义域名](../app-service-web/web-sites-custom-domain-name.md#vip)中的步骤执行操作。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>步骤 3

确认对记录集的更改。 使用 `Set-AzureRMDnsRecordSet` 将对记录集的变更上载到 Azure DNS：

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2.创建自定义域的 CNAME 记录

如果域已由 Azure DNS 管理（请参阅 [DNS 域委派](dns-domain-delegation.md)），可以使用下面的示例，创建 contoso.azurewebsites.net 的 CNAME 记录。

### <a name="step-1"></a>步骤 1

打开 PowerShell，新建 CNAME 记录集，然后将其分配给变量 $rs。 本示例会将在名为“contoso.com”的 DNS 区域中创建“生存时间”为 600 秒的记录集类型 CNAME。

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

以下示例是响应。

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>步骤 2

创建 CNAME 记录集后，需要创建将指向 Web 应用的别名值。

使用以前分配的变量 "$rs"，你可以使用下面的 PowerShell 命令来创建 Web 应用 contoso.azurewebsites.net 的别名。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

以下示例是响应。

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

### <a name="step-3"></a>步骤 3

使用 `Set-AzureRMDnsRecordSet` cmdlet 确认更改：

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

可以通过使用 nslookup 验证由查询“www.contoso.com”正确创建的记录，如下所示：

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
```

## <a name="create-an-awverify-record-for-web-apps"></a>创建 Web 应用的 "awverify" 记录

如果你决定使用 Web 应用的 A 记录，则必须经过验证流程，以确保你拥有自定义域。 此验证步骤是通过创建名为 "awverify" 的特殊 CNAME 记录来完成的。 本部分仅适用于 A 记录。

### <a name="step-1"></a>步骤 1

创建 "awverify" 记录。 在下面的示例中，我们将为 contoso.com 创建 "awverify" 记录，以验证自定义域的所有权。

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

以下示例是响应。

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>步骤 2

创建 "awverify" 记录集后，分配 CNAME 记录集别名。 在下面的示例中，我们会将 CNAME 记录集别名分配给 awverify.contoso.azurewebsites.net。

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

以下示例是响应。

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>步骤 3

使用 `Set-AzureRMDnsRecordSet cmdlet` 确认更改，如下面的命令中所示。

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>后续步骤

请按照 [为 App Service 配置自定义域名](../app-service-web/web-sites-custom-domain-name.md)中的步骤来配置 Web 应用，以便使用自定义域。



<!--HONumber=Feb17_HO2-->


