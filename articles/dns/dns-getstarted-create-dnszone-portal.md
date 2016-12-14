---
title: "如何在 Azure 门户中创建和管理 DNS 区域 | Microsoft Docs"
description: "了解如何为 Azure DNS 创建 DNS 区域。 这是有关创建和管理你的第一个 DNS 并使用 Azure 门户开始托管 DNS 域的分步指南。"
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 42d47741e414b2de177f1fd75b3e1ac3fde96579
ms.openlocfilehash: d70c412f82b019cec164ec45e93e472feb20a3fc

---

# <a name="create-a-dns-zone-in-the-azure-portal"></a>在 Azure 门户中创建 DNS 区域

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

本文将逐步引导你完成使用 Azure 门户创建 DNS 区域的步骤。 你还可以使用 PowerShell 或 CLI 创建 DNS 区域。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

### <a name="about-tags-for-azure-dns"></a>关于 Azure DNS 的标记

标记是名称/值列表，Azure Resource Manager 利用它们来标记资源，进行计费和分组。 有关标记的详细信息，请参阅[使用标记来组织 Azure 资源](../resource-group-using-tags.md)一文。

可以通过使用“**设置**”边栏选项卡在 Azure 门户中为 DNS 区域添加标记。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户
2. 在“中心”菜单上，单击“**新建 > 网络**”，然后单击“**DNS 区域**”以打开“DNS 区域”边栏选项卡。

    ![DNS 区域](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. 在“**DNS 区域**”边栏选项卡上，单击底部的“**创建**”。 这将打开“**创建 DNS 区域**”边栏选项卡。

    ![创建区域](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. 在“**创建 DNS 区域**”边栏选项卡上，命名你的 DNS 区域。 例如，*contoso.com*。 请参阅上面部分中的[关于 DNS 区域名称](#names)。
5. 接下来，指定你想要使用的资源组。 可以创建新的资源组，也可以选择已存在的资源组。
6. 从“**位置**”下拉列表中，指定资源组的位置。 请注意，此设置指的是资源组的位置，不是 DNS 区域的位置。 实际 DNS 区域资源自动成为“全局”资源，而不是你可以（或需要）在门户中指定的资源。
7. 如果想要在仪表板上轻松找到新区域，则可以选中“**固定到仪表板**”。 然后单击“创建” 。

    ![固定到仪表板](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. 单击“创建”后，你将看到仪表板上正在配置的新区域。

    ![正在创建](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. 创建新区域后，将在仪表板上打开新区域的边栏选项卡。

## <a name="view-records"></a>查看记录

创建 DNS 区域还会创建以下记录：

* “起始授权机构”(SOA) 记录。 SOA 位于每个 DNS 区域的根目录下。
* 授权名称服务器 (NS) 记录。 此记录显示托管该区域的名称服务器。 Azure DNS 使用名称服务器池，因此可能会将不同的名称服务器分配给 Azure DNS 中的不同区域。 有关详细信息，请参阅 [向 Azure DNS 委托域](dns-domain-delegation.md) 。

可以从 Azure 门户查看记录

1. 从“**DNS 区域**”边栏选项卡，单击“**所有设置**”以打开 DNS 区域的“**设置**”边栏选项卡。

    ![区域](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)

2. 在“概要”窗格的下半部分，可以查看 DNS 区域的记录集。

    ![区域](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## <a name="test"></a>测试

可以使用 nslookup、DIG 或 [Resolve-DnsName PowerShell cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具测试 DNS 区域。

如果尚未委托域在 Azure DNS 中使用新的区域，则需要直接向区域的其中一个名称服务器提出 DNS 查询。 区域的名称服务器在 NS 记录中给出，如上面的 `Get-AzureRmDnsRecordSet` 所列。 确保将你的区域的正确值替换到下面的命令中。

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)

## <a name="delete-a-dns-zone"></a>删除 DNS 区域

可以直接从门户中删除 DNS 区域。 在 Azure DNS 中删除 DNS 区域之前，你需要删除所有记录集（创建区域时自动创建的区域根目录下的 NS 和 SOA 记录除外）。

1. 找到想要删除的区域的“**DNS 区域**”边栏选项卡，然后单击边栏选项卡顶部的“**删除**”。
2. 此时将显示一条消息，告诉你必须删除自动创建的 NS 和 SOA 记录之外的所有记录集。 如果已删除记录集，请单击“**是**”。 请注意，当从门户中删除 DNS 区域时，不会删除 DNS 区域所关联的资源组。

## <a name="next-steps"></a>后续步骤

创建 DNS 区域后，创建 [记录集和记录](dns-getstarted-create-recordset-portal.md) ，开始解析 Internet 域的名称。




<!--HONumber=Nov16_HO3-->


