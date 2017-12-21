---
title: "通过 Azure 门户开始使用 Azure DNS | Microsoft 文档"
description: "了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 Azure 门户创建和管理第一个 DNS 区域和记录的分步指南。"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: b2a9a7dbd1fa7da7ebe479ac166602245cdaefde
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2017
---
# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>通过 Azure 门户开始使用 Azure DNS

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

本文将逐步引导完成使用 Azure 门户创建第一个 DNS 区域和记录的步骤。 也可以使用 Azure PowerShell 或跨平台 Azure CLI 执行这些步骤。

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后会在此 DNS 区域内为每个 DNS 记录创建域。 最后，要将 DNS 区域发布到 Internet，需要为域配置名称服务器。 以下步骤介绍每个此类步骤。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户
2. 在“中心”菜单上，单击“新建”>“网络”，然后单击“DNS 区域”以打开“创建 DNS 区域”页。

    ![DNS 区域](./media/dns-getstarted-portal/openzone650.png)

4. 在“创建 DNS 区域”页上，输入以下值，并单击“创建”：


   | **设置** | **值** | **详细信息** |
   |---|---|---|
   |**Name**|contoso.com|DNS 区域的名称|
   |**订阅**|[订阅]|选择要在其中创建 DNS 区域的订阅。|
   |**资源组**|**新建：**contosoDNSRG|创建资源组。 资源组名称必须在所选订阅中唯一。 若要详细了解资源组，请阅读 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 这篇概述文章。|
   |**位置**|美国西部||

> [!NOTE]
> 资源组指的是资源组的位置，对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

## <a name="create-a-dns-record"></a>创建 DNS 记录

以下示例指导完成创建新的 A 记录的过程。 若要了解其他记录类型并修改现有记录，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。 

1. 创建 DNS 区域以后，在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”页中单击 **contoso.com** DNS 区域。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“contoso.com”， 轻松访问 DNS 区域。

1. 在“DNS 区域”页顶部，选择“+ 记录集”以打开“添加记录集”页。

1. 在“添加记录集”页中，输入以下值，并单击“确定”。 在此示例中，将创建一个 A 记录。

   |**设置** | **值** | **详细信息** |
   |---|---|---|
   |**Name**|www|记录的名称|
   |**类型**|A| 要创建的 DNS 记录的类型，可接受的值为 A、AAAA、CNAME、MX、NS、SRV、TXT 和 PTR。  有关记录类型的详细信息，请访问 [DNS 区域和记录](dns-zones-records.md)|
   |**TTL**|1|DNS 请求的生存时间。|
   |**TTL 单位**|小时|TTL 值的时间度量。|
   |**IP 地址**|ipAddressValue| 此值是 DNS 记录解析的 IP 地址。|

## <a name="view-records"></a>查看记录

在“DNS 区域”页的下半部分，可看到 DNS 区域的记录。 应当会看到默认的 DNS 和 SOA 记录（在每个区域中都会创建这些记录）以及创建的任何新记录。

![区域](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>更新名称服务器

正确设置 DNS 区域和记录后，需要将域名配置为使用 Azure DNS 名称服务器。 这样，Internet 上的其他用户便可以找到 DNS 记录。

区域的名称服务器在 Azure 门户中指定：

![区域](./media/dns-getstarted-portal/viewzonens500.png)

这些名称服务器应当配置有域名注册机构（向其购买域名的机构）。 域名注册机构提供选项来为域设置名称服务器。 有关详细信息，请参阅[将域委派给 Azure DNS](dns-domain-delegation.md)。

## <a name="delete-all-resources"></a>删除所有资源

若要删除在本文中创建的所有资源，请完成以下步骤：

1. 在 Azure 门户的“收藏夹”窗格中单击“所有资源”。 在“所有资源”页中单击 **MyResourceGroup** 资源组。 如果所选订阅中已包含多个资源，则可在“按名称筛选…”框中输入“MyResourceGroup”， 轻松访问资源组。
1. 在 **MyResourceGroup** 页中，单击“删除”按钮。
1. 门户会要求用户键入资源组的名称，以便确认用户需要删除它。 单击“删除”，键入“MyResourceGroup”作为资源组名称，并单击“删除”。 删除资源组会删除资源组中的所有资源，因此在删除资源组之前，请确保始终确认其内容。 门户会先删除包含在资源组中的所有资源，然后删除资源组本身。 此过程需要几分钟。


## <a name="next-steps"></a>后续步骤

若要了解 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。

若要了解有关在 Azure DNS 中管理 DNS 记录的详细信息，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。

