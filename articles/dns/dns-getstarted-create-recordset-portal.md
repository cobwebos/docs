---
title: "使用 Azure 门户为 DNS 区域创建记录集和记录 | Microsoft Docs"
description: "如何使用 Azure 门户为 Azure DNS 创建主机记录和创建记录集和记录"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f93905f4-e82e-45db-b490-878d318e6aba
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 2752579b6277a27ca1e932fbd2c402ac3135e80c
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>使用 Azure 门户创建 DNS 记录集和记录

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-recordset-cli.md)

本文将指导你通过使用 Azure 门户完成创建记录和记录集过程。 若要执行此操作，首先需要了解 DNS 记录和记录集。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

此页上所有示例都使用“A”DNS 记录类型。 其他记录类型的过程类似。

如果新记录与现有记录具有相同的名称和类型，则需将其添加到现有记录集 &mdash;请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。 如果新记录与所有现有记录的名称和类型都不同，则需要创建新的记录集，如下所述。

## <a name="create-records-in-a-new-record-set"></a>在新的记录集中创建记录

本文将指导你通过使用 Azure 门户完成创建记录和记录集过程。

1. 登录到门户。
2. 转到要在其中创建记录集的“**DNS 区域**”边栏选项卡。
3. 在“DNS 区域”边栏选项卡顶部，选择“+ 记录集”以打开“添加记录集”边栏选项卡。

    ![新建记录集](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. 在“**添加记录集**”边栏选项卡中，命名你的记录集。 例如，可将你的记录集命名为“**www**”。

    ![添加记录集](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 选择想要创建的记录的类型。 例如，选择“**A**”。
6. 设置 **TTL**。 门户中的默认生存时间为一小时。
7. 添加记录集中每条记录的详细信息。 在此例中，由于记录类型为“A”，因此需要添加 A 记录 IP 地址，每行一个 IP 地址。
8. 完成添加 IP 地址后，选择边栏选项卡底部的“**确定**”。 将创建 DNS 记录集。

### <a name="verify-name-resolution"></a>验证名称解析

可使用 DNS 工具（如 nslookup、dig 或 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)）测试 DNS记录是否在 Azure DNS 服务器中。

如果尚未委托域在 Azure DNS 中使用新的区域，则需要[直接向区域的其中一个名称服务器提出 DNS 查询](dns-getstarted-create-dnszone.md#test-name-servers)。 确保将记录区域的正确值替换到下面命令中。

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>后续步骤

了解如何[将域名委托到 Azure DNS 名称服务器](dns-domain-delegation.md)

若要管理记录集和记录，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。

