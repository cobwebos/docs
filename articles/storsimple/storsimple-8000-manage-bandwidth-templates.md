---
title: "管理 StorSimple 8000 系列的带宽模板 | Microsoft Docs"
description: "介绍如何管理 StorSimple 带宽模板，以便控制带宽消耗。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 50d0a920bef097013feddc828d2c37133b9057b0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>使用 StorSimple 设备管理器服务管理 StorSimple 带宽模板

## <a name="overview"></a>概述

使用带宽模板可以根据多个时间计划配置网络带宽用量，以便在云中将 StorSimple 设备的数据分层。

使用带宽限制计划可以：

* 根据工作负荷网络用量指定自定义带宽计划。
* 以简单无缝的方式在多个设备中集中管理和重复使用计划。

> [!NOTE]
> 此功能仅适用于 StorSimple 物理设备（8100 和 8600 型号），而不适用于 StorSimple 云设备（8010 和 8020 型号）。


## <a name="the-bandwidth-templates-blade"></a>“带宽模板”边栏选项卡

“带宽模板”边栏选项卡以表格格式显示服务的所有带宽模板，其中包含以下信息：

* **名称** – 创建带宽模板时分配给带宽模板的唯一名称。
* **计划** – 给定带宽模板中包含的计划数。
* **使用者** – 使用带宽模板的卷数。

还可以在以下主题中找到可帮助配置带宽模板的其他信息：

* [有关带宽模板问题与解答](#questions-and-answers-about-bandwidth-templates)
* [带宽模板最佳做法](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>添加带宽模板

执行以下步骤可创建新的带宽模板。

#### <a name="to-add-a-bandwidth-template"></a>添加带宽模板

1. 转到 StorSimple 设备管理器服务，依次单击“带宽模板”、“+ 添加带宽模板”。

    ![单击“+ 添加带宽模板”](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. 在“添加带宽模板”边栏选项卡中，执行以下步骤：
   
    1. 为带宽模板指定一个唯一的名称。
    2. 定义带宽计划。 若要创建计划，请执行以下操作：
   
        1. 从下拉列表中，选择要为其配置计划的**星期日期**。 可以选择多个日期。        
        
        2. 以 _hh:mm_ 格式输入**开始时间**。 这是开始实施计划的时间。

        3. 以 _hh:mm_ 格式输入**结束时间**。 这是停止实施计划的时间。
      
           > [!NOTE]
           > 不允许重叠的计划。 如果开始时间和结束时间会导致计划重叠，将显示一条错误消息。

        4. 指定“带宽速率”。 这是指 StorSimple 设备在执行涉及到云的操作时使用的带宽，以每秒兆位数 (Mbps) 表示。 为此字段提供介于 1 和 1,000 之间的数字。

            ![定义带宽计划](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            重复上述步骤为模板定义多个计划，直到完成。

        5. 单击“添加”开始创建带宽模板。 创建的模板会添加到带宽模板列表中。
      

## <a name="edit-a-bandwidth-template"></a>编辑带宽模板

执行以下步骤可编辑带宽模板。

### <a name="to-edit-a-bandwidth-template"></a>编辑带宽模板

1. 转到 StorSimple 设备管理器服务并单击“带宽模板”。
2. 在带宽模板列表中，选择要删除的模板。 单击右键，然后从上下文菜单中选择“删除”。
3. 出现确认提示时，单击“确定”。 随即应会删除该带宽模板。 
4. 带宽模板列表会更新以反映删除后的结果。

> [!NOTE]
> 如果编辑的计划与正在修改的带宽模板中的现有计划重叠，则无法保存更改。

## <a name="delete-a-bandwidth-template"></a>删除带宽模板

执行以下步骤可删除带宽模板。

#### <a name="to-delete-a-bandwidth-template"></a>删除带宽模板

1. 转到 StorSimple 设备管理器服务并单击“带宽模板”。
2. 在带宽模板列表中，选择要删除的模板。 单击右键，并从上下文菜单中选择“删除”。
3. 出现确认提示时，单击“确定”。 随即应会删除该带宽模板。
4. 带宽模板列表会更新以反映删除后的结果。

如果模板被任何卷使用，将不允许删除该模板。 此时将显示一条错误消息，指出该模板正在使用中。 系统将显示一个错误消息对话框，告知应删除对该模板的所有引用。

若要删除对此模板的所有引用，可以访问“卷容器”页，然后修改使用此模板的卷容器，使其使用其他模板，或使用自定义或不受限制的带宽设置。 删除所有引用后，便可以删除该模板。

## <a name="use-a-default-bandwidth-template"></a>使用默认带宽模板

默认情况下，在访问云时，卷容器使用提供的默认带宽模板实施带宽控制。 想要自行创建模板的用户也可以使用现成的默认模板作为参考。 此默认模板的详细信息为：

* **名称** – 夜间或周末不受限制
* **计划** – 从星期一到星期五的单个计划，在 8 AM 到 5 PM 设备时间应用 1 Mbps 带宽速率。 在一周的剩余时间，带宽设置为“无限制”。

可以编辑默认模板。 系统会跟踪此模板（包括编辑的版本）的使用。

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>创建在指定的时间启动的全天带宽模板

遵循以下过程，创建在指定时间启动并全天运行的计划。 例如，计划在 9 AM 启动，一直运行到第二天 9 AM。 请务必注意，给定计划的开始时间和结束时间都必须包含在同一个 24 小时计划中，并且不能跨越多天。 如果需要设置跨越多天的带宽模板，则需要使用多个计划（如示例中所示）。

#### <a name="to-create-an-all-day-bandwidth-template"></a>创建全天带宽模板

1. 创建在上午 9 点启动，一直运行到午夜的计划。
2. 添加另一个计划。 配置从午夜运行到上午 9 点的第二个计划。
3. 保存带宽模板。

然后，复合计划将在所选的时间启动并全天运行。

## <a name="questions-and-answers-about-bandwidth-templates"></a>有关带宽模板问题与解答

**问**： 如果在计划间隔期进行带宽控制会发生什么情况？ （计划已结束，另一个计划尚未启动）。

**答**： 在这种情况下，将不采用任何带宽控制。 换而言之，设备在将数据分层到云时可以使用不受限制的带宽。

**问**： 是否可以在脱机设备上修改带宽模板？

**答**： 如果相应的设备已脱机，则无法修改卷容器上的带宽模板。

**问**： 当关联的卷脱机时，是否可以编辑与卷容器关联的带宽模板？

**答**： 可以修改其卷处于脱机状态的卷容器关联的带宽模板。 请注意，当卷脱机时，不会将任何数据从设备分层到云中。

**问**： 是否可以删除默认模板？

**答**： 虽然可以删除默认模板，但不建议这样做。 系统会跟踪默认模板（包括编辑的版本）的使用。 此外，系统会分析跟踪数据，并使用一段时间内的数据来改善默认模板。

**问**： 如何确定是否需要修改带宽模板？

**答**： 需要修改带宽模板的迹象之一是，网络开始变慢，或者一天内多次阻塞。 如果发生这种情况，请通过观察 I/O 性能与网络吞吐量图表来监视存储和使用网络。

根据网络吞吐量数据识别发生网络瓶颈的时间和所在的卷容器。 如果在将数据分层到云时发生此情况（可以从云中部署的设备的所有卷容器的 I/O 性能了解此信息），则需要修改与卷容器关联的带宽模板。

使用修改的模板后，需要再次监视网络以确定是否存在明显的延迟。 如果仍然存在这些问题，则需要再次访问带宽模板。

**问**： 如果设备上的多个卷容器具有相互重叠并应用不同限制的计划，将会发生什么情况？

**答**： 假设设备上有 3 个卷容器， 与这些容器关联的计划完全重叠。 对于其中的每个容器，使用的带宽限制分别 5、10 和 15 Mbps。 如果所有这些容器同时发生 I/O，可以应用 3 个带宽限制中的最小者：在这种情况下为 5 Mbps，因为这些传出 I/O 请求共享同一个队列。

## <a name="best-practices-for-bandwidth-templates"></a>带宽模板最佳实践

请在 StorSimple 设备上遵循以下最佳实践：

* 在设备上配置带宽模板，使设备在一天的不同时间对网络吞吐量实施不同的限制。 在非高峰期将这些带宽模板与备份计划配合使用时，可以有效利用额外的网络带宽完成云操作。
* 根据部署大小和所需的恢复时间目标 (RTO) 计算特定部署所需的实际带宽。

## <a name="next-steps"></a>后续步骤

了解有关如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)的详细信息。


