---
title: 通过 StorSimple 设备管理器记录支持票证 | Microsoft 文档
description: 介绍 StorSimple Device Manager 诊断功能并说明如何使用它来排查 StorSimple 虚拟阵列问题。
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a0c394df-957b-49b3-a283-38824f8847fd
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 658afbc178814389fefd7941e2ca030741bd08e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23109995"
---
# <a name="use-the-storsimple-device-manager-service-to-log-a-support-request-for-the-storsimple-virtual-array"></a>使用 StorSimple Device Manager 服务记录 StorSimple 虚拟阵列的支持请求

## <a name="overview"></a>概述

StorSimple Device Manager 在“服务摘要”边栏选项卡中提供**记录新支持请求**的功能。 本文介绍如何在门户中记录新支持请求和管理其生命周期。

## <a name="new-support-request"></a>新建支持请求

根据[支持计划](https://azure.microsoft.com/support/plans/)，可以直接从 StorSimple Device Manager 服务摘要边栏选项卡针对 StorSimple 虚拟阵列上的问题创建支持票证。

#### <a name="to-log-a-new-request"></a>记录新请求

1. 转到 StorSimple Device Manager 服务。 在服务摘要边栏选项卡设置中，转到“支持 + 疑难解答”，并单击“新建支持请求”。
   
    ![新建支持请求](./media/storsimple-virtual-array-log-support-ticket/log-support-ticket1.png)

2. 在“基本”边栏选项卡中执行以下操作：

    1. 从“颁发类型”下拉列表中，选择“技术”。 
    
    2. 将自动选择当前**订阅**、**服务**类型和**资源**（StorSimple Device Manager 服务）。 

    3. 指定一个或多个遇到问题的已注册到服务的设备。

    4. 如果有多个计划与订阅相关联，请选择合适的**支持计划**。 需要付费的支持计划才能启用技术支持。

3. 在“步骤 2”中，选择**严重性**并指定问题是否与阵列或 StorSimple Device Manager 服务相关。 此外，请选择此问题的**类别**，并提供有关此问题的更多**详细信息**。
   
    ![新建支持请求](./media/storsimple-virtual-array-log-support-ticket/log-support-ticket2.png)

4. 在“步骤 3”中，提供联系信息。 Microsoft 支持将使用此信息与你联系，以获得进一步信息、诊断和解决方法。
   
    ![新建支持请求](./media/storsimple-virtual-array-log-support-ticket/log-support-ticket3.png)

## <a name="manage-a-support-request"></a>管理支持请求

创建支持请求之后，可以在门户内管理请求的生命周期。

#### <a name="to-manage-your-support-requests"></a>管理支持请求

若要访问“帮助和支持”页，请导航到“浏览”>“帮助 + 支持”。

![管理支持请求](./media/storsimple-virtual-array-log-support-ticket/manage-support-tickets.png)

## <a name="next-steps"></a>后续步骤

了解如何[诊断并解决与 StorSimple 虚拟阵列相关的问题](storsimple-virtual-array-diagnose-problems.md)

