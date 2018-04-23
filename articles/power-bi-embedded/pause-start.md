---
title: 在 Azure 门户中暂停和启动 Power BI Embedded 容量 | Microsoft Docs
description: 本文演练如何在 Microsoft Azure 中暂停和启动 Power BI Embedded 容量。
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: f96c62ea1d4fea6a724cd50cce9b54c02ddebdf5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>在 Azure 门户中暂停和启动 Power BI Embedded 容量

本文演练如何在 Microsoft Azure 中暂停和启动 Power BI Embedded 容量。 本文假定已创建了 Power BI Embedded 容量。 如果尚未创建，请参阅[在 Azure 门户中创建 Power BI Embedded 容量](create-capacity.md)以开始创建。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。

## <a name="pause-your-capacity"></a>暂停容量

暂停容量可阻止进行计费。 如果在一段时间内无需使用容量，则暂停容量会很棒。 使用以下步骤可暂停容量。

> [!NOTE]
> 暂停容量可能会使内容在 Power BI 中不可用。 确保在暂停之前从容量取消分配工作区以防止中断。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“所有服务” > “Power BI Embedded”以查看容量。

    ![Azure 门户中的“所有服务”](media/pause-start/azure-portal-more-services.png)

3. 选择要暂停的容量。

    ![Azure 门户中的 Power BI Embedded 容量列表](media/pause-start/azure-portal-capacity-list.png)

4. 在容量详细信息中选择“暂停”。

    ![暂停容量](media/pause-start/azure-portal-pause-capacity.png)

5. 选择“是”，这可确认要暂停容量。

    ![确认暂停](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>启动容量

通过启动容量来恢复使用。 启动容量也会恢复计费。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“所有服务” > “Power BI Embedded”以查看容量。

    ![Azure 门户中的“所有服务”](media/pause-start/azure-portal-more-services.png)

3. 选择要启动的容量。

    ![Azure 门户中的 Power BI Embedded 容量列表](media/pause-start/azure-portal-capacity-list.png)

4. 在容量详细信息中选择“启动”。

    ![启动容量](media/pause-start/azure-portal-start-capacity.png)

5. 选择“是”，这可确认要启动容量。

    ![确认启动](media/pause-start/azure-portal-confirm-start.png)

如果有任何内容分配给此容量，则会在启动之后可用。

## <a name="next-steps"></a>后续步骤

如果要增加或减少容量，请参阅[缩放 Power BI Embedded 容量](scale-capacity.md)。

若要开始在应用程序中嵌入 Power BI 内容，请参阅[如何嵌入 Power BI 仪表板、报表和磁贴](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有更多问题？ [尝试在 Power BI 社区中提问](http://community.powerbi.com/)