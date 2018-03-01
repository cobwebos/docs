---
title: "缩放 Power BI Embedded 容量 | Microsoft Docs"
description: "本文演练如何在 Microsoft Azure 中缩放 Power BI Embedded 容量。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/19/2018
ms.author: asaxton
ms.openlocfilehash: 9b7d0bc31946d6022e9bfae463f4a22eb12c2a58
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="scale-your-power-bi-embedded-capacity"></a>缩放 Power BI Embedded 容量

本文演练如何在 Microsoft Azure 中缩放 Power BI Embedded 容量。 通过缩放，可增加或减小容量的大小。

本文假定已创建了 Power BI Embedded 容量。 如果尚未创建，请参阅[在 Azure 门户中创建 Power BI Embedded 容量](create-capacity.md)以开始创建。

> [!NOTE]
> 缩放操作可能需要大约一分钟。 在此期间，容量将不可用。 嵌入的内容可能无法加载。

## <a name="scale-a-capacity"></a>扩展容量

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“所有服务” > “Power BI Embedded”以查看容量。

    ![Azure 门户中的“所有服务”](media/scale-capacity/azure-portal-more-services.png)

3. 选择要缩放的容量。

    ![Azure 门户中的 Power BI Embedded 容量列表](media/scale-capacity/azure-portal-capacity-list.png)

4. 在该容量内，选择“缩放”下的“定价层”。

    ![“缩放”下的“定价层”选项](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    当前定价层以蓝色框出。

    ![以蓝色框出的当前定价层](media/scale-capacity/azure-portal-current-tier.png)

5. 若要提升或降低，请选择要迁移到的新层。 选择新层会在所选项周围显示蓝色虚线边框。 选择“选择”以扩展到新层。

    ![选择新层](media/scale-capacity/azure-portal-select-new-tier.png)

    缩放容量可能需要一两分钟才能完成。

6. 通过查看概述选项卡来确认层。会列出当前定价层。

    ![确认当前层](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>后续步骤

若要暂停或启动容量，请参阅[在 Azure 门户中暂停和启动 Power BI Embedded 容量](pause-start.md)。

若要开始在应用程序中嵌入 Power BI 内容，请参阅[如何嵌入 Power BI 仪表板、报表和磁贴](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有更多问题？ [尝试在 Power BI 社区中提问](http://community.powerbi.com/)
