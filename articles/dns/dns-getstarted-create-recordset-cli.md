---
title: 使用 CLI 为 DNS 区域创建记录集和记录 | Microsoft Docs
description: 如何为 Azure DNS 创建主机记录。使用 CLI 设置记录集和记录
services: dns
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: cherylmc

---
# 通过使用 CLI 创建 DNS 记录集和记录
> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)
> 
> 

本文将指导你通过使用 CLI 完成创建记录和记录集过程。在创建 DNS 区域后，需要添加域的 DNS 记录。若要执行此操作，首先需要了解 DNS 记录和记录集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## 创建记录集和记录
在此部分中，我们将介绍如何创建记录集和记录。在此示例中，将在 DNS 区域“contoso.com”中创建具有相对名称“www”的记录集。记录的完全限定名称为“www.contoso.com”。记录类型是“A”，并且生存时间 (TTL) 为 60 秒。在完成此步骤之后，将创建一个空的记录集。

若要在区域顶点中创建记录集（在这种情况下为“contoso.com”），使用记录名称“@”，包括引号。这是常见的 DNS 约定。

### 1\.创建记录集
若要创建记录集，请使用 `azure network dns record-set create`。指定资源组、区域名称、记录集、相对名称、记录类型和 TTL。如果未定义 `--ttl` 参数，则该值默认为 4（以秒为单位）。在完成此步骤之后，将创建一个空的“www”记录集。

*用法：network dns record-set create <资源组> <DNS 区域名称> <名称> <类型><ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\.添加记录
若要使用新创建的“www”记录集，需要为其添加记录。通过使用 `azure network dns record-set add-record` 将记录添加到记录集。

添加记录到记录集的参数会因记录集的类型而有所变化。例如，在使用类型“A”记录集时，将只能使用参数 `-a <IPv4 address>` 指定记录。

可以通过使用以下命令将 IPv4 *A* 记录添加到“www”记录集：

*用法：network dns record-set add-record <资源组> <DNS 区域名称> <记录集名称> <类型>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## 其他记录类型示例
以下示例演示如何创建每个记录类型的记录集。每个记录集只包含一个单一记录。

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## 后续步骤
若要管理记录集和记录，请参阅[使用 CLI 管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。

有关 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。

<!---HONumber=AcomDC_0921_2016-->