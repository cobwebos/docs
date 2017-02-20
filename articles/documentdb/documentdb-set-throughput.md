---
title: "预配 Azure DocumentDB 的吞吐量 | Microsoft 文档"
description: "了解如何为 DocumentDB 集合设置预配吞吐量。"
services: documentdb
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f98def7f-f012-4592-be03-f6fa185e1b1e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 9919f9430626b02635272599e7d1f3f09e0068cd
ms.openlocfilehash: ae83dc01113e72576a35aa57acbac3b71d87e9f8


---

# <a name="set-throughput-for-azure-documentdb-collections"></a>为 Azure DocumentDB 集合设置吞吐量

可以在 Azure 门户中或使用客户端 SDK 为 DocumentDB 集合设置吞吐量。 

## <a name="to-set-the-throughput-by-using-the-azure-portal"></a>使用 Azure 门户设置吞吐量

1. 在新窗口中，打开 [Azure 门户](https://portal.azure.com)。
2. 在左侧栏中单击“NoSQL (DocumentDB)”，或者单击底部的“更多服务”，滚动到“数据库”，然后单击“NoSQL (DocumentDB)”。
3. 选择 DocumentDB 帐户。
4. 在新窗口中，在“集合”下单击“缩放”，如以下屏幕截图中所示。
5. 在新窗口中，从下拉列表中选择你的集合，更改**吞吐量**值，然后单击“保存”。

    ![显示如何在 Azure 门户中通过导航到帐户，然后单击“缩放”来更改集合的吞吐量的屏幕截图](./media/documentdb-set-throughput/azure-documentdb-change-throughput-value.png)

## <a name="to-set-the-throughput-by-using-the-net-sdk"></a>使用 .NET SDK 设置吞吐量

```C#
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
        .Where(r => r.ResourceLink == collection.SelfLink)    
        .AsEnumerable()
        .SingleOrDefault();

    // Set the throughput to the new value, for example 12,000 request units per second
    offer = new OfferV2(offer, 12000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

## <a name="next-steps"></a>后续步骤

若要了解有关使用 DocumentDB 进行预配和全球扩展的详细信息，请参阅[使用 DocumentDB 进行分区和缩放](documentdb-partition-data.md)。


<!--HONumber=Feb17_HO2-->


