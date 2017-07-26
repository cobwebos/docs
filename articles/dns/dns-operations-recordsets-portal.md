---
title: "使用 Azure DNS 管理 DNS 记录集和记录 | Microsoft Docs"
description: "Azure DNS 在托管域时具有管理 DNS 记录集和记录的功能。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 18ed44a1-7bfe-454f-964e-922ad978264a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 001b80ccba43beab44f6a598f820df65a85a345f
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017

---

# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>使用 Azure 门户管理 DNS 记录和记录集

> [!div class="op_single_selector"]
> * [在 Azure 应用服务中创建 Java Web 应用](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

本文演示如何通过使用 Azure 门户来管理 DNS 区域的记录集和记录。

请务必了解 DNS 记录集和单独的 DNS 记录之间的差异。 记录集是区域中具有相同名称和相同类型的记录的集合。 有关详细信息，请参阅[使用 Azure 门户创建 DNS 记录集和记录](dns-getstarted-create-recordset-portal.md)。

## <a name="create-a-new-record-set-and-record"></a>创建新的记录集和记录

若要在 Azure 门户中创建记录集，请参阅[使用 Azure 门户创建 DNS 记录](dns-getstarted-create-recordset-portal.md)。

## <a name="view-a-record-set"></a>查看记录集

1. 在 Azure 门户中，转到“**DNS 区域**”边栏选项卡。
2. 搜索记录集，然后选择它。 这将打开记录集属性。

    ![搜索记录集](./media/dns-operations-recordsets-portal/searchset500.png)

## <a name="add-a-new-record-to-a-record-set"></a>将新记录添加到记录集

可以将最多 20 条记录添加到任何记录集。 记录集不能包含两个相同的记录。 可以创建空记录集（具有零个记录），但它不会出现在 Azure DNS 名称服务器中。 CNAME 类型的记录集最多可以包含一条记录。

1. 在 DNS 区域的“**记录集属性**”边栏选项卡中，单击你想要将记录添加到的记录集。

    ![选择记录集](./media/dns-operations-recordsets-portal/selectset500.png)

2. 过填写字段指定记录集的属性。

    ![添加记录](./media/dns-operations-recordsets-portal/addrecord500.png)

3. 单击边栏选项卡顶部的“**保存**”以保存你的设置。 然后关闭该边栏选项卡。
4. 将在角落中看到正在保存该记录。

    ![保存记录集](./media/dns-operations-recordsets-portal/saving150.png)

保存记录集后，“**DNS 区域**”边栏选项卡中的这些值将反映新记录。

## <a name="update-a-record"></a>更新记录

更新现有记录集的记录时，可以更新的字段取决于你正在使用的记录类型。

1. 在记录集的“**记录集属性**”边栏选项卡中，搜索记录。
2. 修改记录。 修改记录时，可以更改记录的可用设置。 在下面的示例中，已选择“**IP 地址**”字段，并且 IP 地址正在修改中。

    ![修改记录](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. 单击边栏选项卡顶部的“**保存**”以保存你的设置。 在右上角中，你将看到该记录已保存的通知。

    ![已保存记录集](./media/dns-operations-recordsets-portal/saved150.png)

保存记录后，记录集在“**DNS 区域**”边栏选项卡中的值将反映更新的记录。

## <a name="remove-a-record-from-a-record-set"></a>从记录集中删除记录

Azure 门户可用于从记录集中删除记录。 请注意，从记录集中删除最后一条记录不会删除记录集。

1. 在记录集的“**记录集属性**”边栏选项卡中，搜索记录。
2. 单击你想要删除的记录。 然后选择“**删除**”。

    ![删除记录](./media/dns-operations-recordsets-portal/removerecord500.png)

3. 单击边栏选项卡顶部的“**保存**”以保存你的设置。
4. 删除记录后，记录在“**DNS 区域**”边栏选项卡中的值将反映删除操作。

## <a name="delete"></a>删除记录集

1. 在记录集的“**记录集属性**”边栏选项卡中，搜单击“**删除**”。

    ![删除记录集](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. 将出现一条消息，询问你是否想要删除记录集。
3. 验证该名称与要删除的记录集是否匹配，然后单击“**是**”。
4. 在“**DNS 区域**”边栏选项卡中，确认记录集不再出现。

## <a name="work-with-ns-and-soa-records"></a>使用 NS 和 SOA 记录

自动创建的 NS 和 SOA 记录的管理方式与其他记录类型不同。

### <a name="modify-soa-records"></a>修改 SOA 记录

不能在区域顶点（名称 =“@”）从自动创建的 SOA 记录集中添加或删除记录。 但是，可以修改 SOA 记录和记录集 TTL 中的任何参数（“Host”除外）。

### <a name="modify-ns-records-at-the-zone-apex"></a>修改区域顶点处的 NS 记录

在每个 DNS 区域自动创建区域顶点处的 NS 记录集。 其中包含分配给该区域的 Azure DNS 名称服务器名称。

可向此 NS 记录集添加其他名称服务器，从而支持与多个 DNS 提供商共同托管域。 还可修改此记录集的 TTL 和元数据。 但是，无法删除或修改预填充的 Azure DNS 名称服务器。

请注意，这仅适用于区域顶点处的 NS 记录集。 区域中的其他 NS 记录集（用于委派子区域）不受约束，可进行修改。

### <a name="delete-soa-or-ns-record-sets"></a>删除 SOA 或 NS 记录集

不能在创建区域时自动创建的区域顶点处（名称 =“@”）删除 SOA 和 NS 记录集。 删除该区域时，将自动删除这些记录集。

## <a name="next-steps"></a>后续步骤

* 有关 Azure DNS 的详细信息，请参阅 [Azure DNS 概述](dns-overview.md)。
* 有关自动执行 DNS 的详细信息，请参阅[使用 .NET SDK 创建 DNS 区域和记录集](dns-sdk.md)。
* 有关反向 DNS 记录的详细信息，请参阅 [Azure 中的反向 DNS 和支持概述](dns-reverse-dns-overview.md)。

