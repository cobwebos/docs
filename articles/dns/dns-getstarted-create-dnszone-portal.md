---
title: "如何在 Azure 门户中创建和管理 DNS 区域 | Microsoft Docs"
description: "了解如何在 Azure DNS 中创建 DNS 区域。 这是有关使用 Azure 门户创建和管理第一个 DNS 区域的分步指南。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f44c5ea1-4c85-469e-888e-5f5b34451664
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 7119ce18e04b427c268d4d8636b1a5fa02ed0cc3
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>在 Azure 门户中创建 DNS 区域

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

本文将逐步引导完成使用 Azure 门户创建 DNS 区域的步骤。 你还可以使用 PowerShell 或 CLI 创建 DNS 区域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户
2. 在“中心”菜单上，单击“新建”>“网络”，然后单击“DNS 区域”以打开“创建 DNS 区域”边栏选项卡。

    ![DNS 区域](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

4. 在“**创建 DNS 区域**”边栏选项卡上，命名你的 DNS 区域。 例如，*contoso.com*。
 
    ![创建区域](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

5. 接下来，指定你想要使用的资源组。 可以创建新的资源组，也可以选择已存在的资源组。 如果选择创建新的资源组，则使用“位置”下拉列表来指定资源组的位置。 请注意，此设置指的是资源组的位置，对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

6. 如果想要在仪表板上轻松找到新区域，则可以选中“**固定到仪表板**”。 然后单击“创建” 。

    ![固定到仪表板](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

7. 单击“创建”后，你将看到仪表板上正在配置的新区域。

    ![正在创建](./media/dns-getstarted-create-dnszone-portal/creating150.png)

8. 创建新区域后，将在仪表板上打开新区域的边栏选项卡。

## <a name="view-records"></a>查看记录

创建 DNS 区域还会创建以下记录：

* “起始授权机构”(SOA) 记录。 SOA 位于每个 DNS 区域的根目录下。
* 授权名称服务器 (NS) 记录。 此记录显示托管该区域的名称服务器。 Azure DNS 使用名称服务器池，因此可能会将不同的名称服务器分配给 Azure DNS 中的不同区域。 有关详细信息，请参阅 [向 Azure DNS 委托域](dns-domain-delegation.md) 。

在“DNS 区域”边栏选项卡的下半部分，可以查看 DNS 区域的记录集。

![区域](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test-name-servers"></a>测试名称服务器

可使用 DNS 工具（如 nslookup、dig 或 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx)）测试 DNS 区域是否在 Azure DNS 名称服务器中。

如果尚未委托域而在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。 区域的名称服务器在 Azure 门户中指定：
    
![区域](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

确保将区域的正确名称服务器替换到下面的命令中。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = azuredns-hostmaster.microsoft.com
            serial  = 1
            refresh = 3600 (1 hour)
            retry   = 300 (5 mins)
            expire  = 2419200 (28 days)
            default TTL = 300 (5 mins)

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，[创建记录集和记录](dns-getstarted-create-recordset-portal.md)为 Internet 域创建 DNS 记录。


