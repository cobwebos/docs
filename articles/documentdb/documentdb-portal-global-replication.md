---
title: "DocumentDB 全局数据库复制 | Microsoft Docs"
description: "了解如何通过 Azure 门户管理 DocumentDB 帐户的全局复制。"
services: documentdb
keywords: "全局数据库, 复制"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 691e2e9156a825e64890f035b23cb2526d502107
ms.openlocfilehash: c7d8cea9a15ec79356c89f628ef9d8e8ccbaaec3


---
# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>如何使用 Azure 门户执行 DocumentDB 全局数据库复制

了解如何使用 Azure 门户复制多个区域中的数据，使 Azure DocumentDB 中的数据具有全局可用性。

有关 DocumentDB 中全局数据库复制工作原理的信息，请参阅[使用 DocumentDB 全局分发数据](documentdb-distribute-data-globally.md)。 有关以编程方式执行全局数据库复制的信息，请参阅[使用多区域 DocumentDB 帐户进行开发](documentdb-developing-with-multiple-regions.md)。

> [!NOTE]
> DocumentDB 数据库全局分发功能已正式推出，所有新建的 DocumentDB 帐户将自动启用该功能。 我们正在努力为所有现有帐户启用全局分发，但在此之前，如果要为帐户启用全局分发，请[与支持部门联系](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，我们将会帮助你启用。
> 
> 

## <a name="a-idaddregionaadd-global-database-regions"></a><a id="addregion"></a>添加全局数据库区域
DocumentDB 已在大部分 [Azure 区域][azureregions]中可用。 为数据库帐户选择默认的一致性级别后，可以关联一个或多个区域（具体取决于所选的默认一致性级别和全局分发需求）。

1. 在 [Azure 门户](https://portal.azure.com/)的跳转栏中，单击“NoSQL (DocumentDB)”。
2. 在“NoSQL (DocumentDB)”边栏选项卡中，选择要修改的数据库帐户。
3. 在“帐户”边栏选项卡上的菜单中单击“全局复制数据”。
4. 在“全局复制数据”边栏选项卡中，选择要添加或删除的区域，然后单击“保存”。 添加区域会产生费用，有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/documentdb/)或[使用 DocumentDB 全局分发数据](documentdb-distribute-data-globally.md)。
   
    ![单击图中的区域可以添加或删除区域][1]
    
添加第二个区域后，在门户中的“本地复制数据”边栏选项卡中启用“手动故障转移”选项。 此选项可用于测试故障转移过程。 添加第三个区域后，在同一边栏选项卡上启用“故障转移优先级”选项，以便更改读取的故障转移顺序。  

### <a name="selecting-global-database-regions"></a>选择全局数据库区域
在配置两个或更多个区域时，建议按照[业务连续性和灾难恢复 (BCDR)：Azure 配对区域][bcdr] 一文中所述，根据区域对选择区域。

具体而言，在配置多个区域时，请确保从每个配对区域列中选择相同数目的区域（使用 +/-1 可更改为奇数/偶数）。 例如，如果你要部署到四个美国区域，请从左列中选择两个美国区域，再从右列中选择两个美国区域。 因此，下面是适当的设置：美国西部、美国东部、美国中北部和美国中南部。

如果只为灾难恢复方案配置两个区域，请务必遵循本指南。 如果要配置两个以上的区域，则遵循本指南是不错的做法，但只要某些选定的区域遵守这种配对，则就没有必要遵循本指南。

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **NoSQL (DocumentDB)** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a name="a-idnextanext-steps"></a><a id="next"></a>后续步骤
阅读 [DocumentDB 中的一致性级别](documentdb-consistency-levels.md)，了解如何管理全局复制帐户的一致性。

有关 DocumentDB 中全局数据库复制工作原理的信息，请参阅[使用 DocumentDB 全局分发数据](documentdb-distribute-data-globally.md)。 有关以编程方式复制多个区域中数据的信息，请参阅[使用多区域 DocumentDB 帐户进行开发](documentdb-developing-with-multiple-regions.md)。

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[服务]: https://azure.microsoft.com/en-us/pricing/details/documentdb/



<!--HONumber=Nov16_HO4-->


