---
title: "通过 Azure 门户开始使用 Azure DNS | Microsoft 文档"
description: "了解如何在 Azure DNS 中创建 DNS 区域和记录。 这是有关使用 Azure 门户创建和管理你的第一个 DNS 区域和记录的分步指南。"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 79f0c9297c4be70f705f325274f3d9241ea4bc3f
ms.lasthandoff: 03/30/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>通过 Azure 门户开始使用 Azure DNS

> [!div class="op_single_selector"]
> * [Azure 门户](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

本文将逐步引导你完成使用 Azure 门户创建你的第一个 DNS 区域和记录的步骤。 也可以使用 Azure PowerShell 或跨平台 Azure CLI 执行这些步骤。

DNS 区域用来托管某个特定域的 DNS 记录。 若要开始在 Azure DNS 中托管域，需要为该域名创建 DNS 区域。 随后将在此 DNS 区域内为每个 DNS 记录创建域。 最后，若要将 DNS 区域发布到 Internet，需要为域配置名称服务器。 以下描述了上述每一个步骤。

## <a name="create-a-dns-zone"></a>创建 DNS 区域

1. 登录到 Azure 门户
2. 在“中心”菜单上，单击“新建”>“网络”，然后单击“DNS 区域”以打开“创建 DNS 区域”边栏选项卡。

    ![DNS 区域](./media/dns-getstarted-portal/openzone650.png)

4. 在“**创建 DNS 区域**”边栏选项卡上，命名你的 DNS 区域。 例如，*contoso.com*。
 
    ![创建区域](./media/dns-getstarted-portal/newzone250.png)

5. 接下来，指定你想要使用的资源组。 可以创建新的资源组，也可以选择已存在的资源组。 如果选择创建新的资源组，则使用“位置”下拉列表来指定资源组的位置。 请注意，此设置指的是资源组的位置，对 DNS 区域没有影响。 DNS 区域位置始终是“全局”，并且不会显示。

6. 如果想要在仪表板上轻松找到新区域，则可以选中“**固定到仪表板**”。 然后单击“创建” 。

    ![固定到仪表板](./media/dns-getstarted-portal/pindashboard150.png)

7. 单击“创建”后，你将看到仪表板上正在配置的新区域。

    ![正在创建](./media/dns-getstarted-portal/creating150.png)

8. 创建新区域后，将在仪表板上打开新区域的边栏选项卡。


## <a name="create-a-dns-record"></a>创建 DNS 记录

以下示例将指导你完成创建新的 A 记录的过程。 若要了解其他记录类型并修改现有记录，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。 


1. 在“DNS 区域”边栏选项卡顶部，选择“+ 记录集”以打开“添加记录集”边栏选项卡。

    ![新建记录集](./media/dns-getstarted-portal/newrecordset500.png)

4. 在“**添加记录集**”边栏选项卡中，命名你的记录集。 例如，可将你的记录集命名为“**www**”。

    ![添加记录集](./media/dns-getstarted-portal/addrecordset500.png)

5. 选择想要创建的记录的类型。 对于此示例，请选择“A”。
6. 设置 **TTL**。 默认生存时间为一小时。
7. 添加记录的 IP 地址。
8. 单击边栏选项卡底部的“确定”以创建 DNS 记录。


## <a name="view-records"></a>查看记录

在 DNS 区域边栏选项卡的下半部分，可以看到 DNS 区域的记录。 你应当会看到默认的 DNS 和 SOA 记录（在每个区域中都会创建这些记录）以及你创建的任何新记录。

![区域](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>更新名称服务器

正确设置 DNS 区域和记录后，需要将域名配置为使用 Azure DNS 名称服务器。 这使得 Internet 上的其他用户可以发现你的 DNS 记录。

区域的名称服务器在 Azure 门户中指定：

![区域](./media/dns-getstarted-portal/viewzonens500.png)

这些名称服务器应当配置有域名注册机构（你向其购买域名的机构）。 域名注册机构将提供选项来为域设置名称服务器。 有关详细信息，请参阅[将域委派给 Azure DNS](dns-domain-delegation.md)。


## <a name="next-steps"></a>后续步骤

若要了解 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。

若要了解有关在 Azure DNS 中管理 DNS 记录的详细信息，请参阅[使用 Azure 门户管理 DNS 记录和记录集](dns-operations-recordsets-portal.md)。


