---
title: 用 Azure Sentinel Preview 调查事件 |Microsoft Docs
description: 使用本教程了解如何使用 Azure Sentinel 调查事件。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780383"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>教程：用 Azure Sentinel Preview 调查事件

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本教程可帮助你检测 Azure Sentinel 的威胁。

将[数据源连接](quickstart-onboard.md)到 Azure Sentinel 后, 需要在出现可疑情况时收到通知。 为了使你能够执行此操作, Azure Sentinel 使你能够创建高级警报规则, 这些规则可生成可分配的事件, 并可用于深入调查环境中的异常和威胁。 

> [!div class="checklist"]
> * 创建事件
> * 调查事件
> * 应对威胁

## <a name="investigate-incidents"></a>调查事件

事件可包含多个警报。 它是用于特定调查的所有相关证据的聚合。 事件是根据你在 "**分析**" 页中定义的警报创建的。 与警报相关的属性, 如严重性和状态在事件级别设置。 当你允许 Azure Sentinel 知道要查找的威胁类型以及如何查找它们后, 可以通过调查事件监视检测到的威胁。 

1. 选择 "**事件**"。 使用 "**事件**" 页, 您可以了解有多少事件、打开的事件数、已设置为**正在进行**的事件数以及已关闭的事件数。 对于每个事件, 可以查看其发生时间以及事件的状态。 查看严重性, 确定首先处理的内容。 在 "**事件**" 页中, 单击 "**警报**" 选项卡以查看与事件相关的所有警报。 可以在 "**实体**" 选项卡中查看作为事件的一部分映射的实体。你可以根据需要筛选事件, 例如按状态或严重性。 查看 "**事件**" 选项卡时, 你将看到 "打开的事件", 其中包含在**分析**中定义的检测规则触发的警报。 在顶部, 你将看到活动事件、新事件和正在进行的事件。 你还可以按严重性查看所有事件的概述。

   ![警报仪表板](./media/tutorial-investigate-cases/cases.png)

2. 若要开始调查, 请单击特定事件。 在右侧, 可以查看事件的详细信息, 包括其严重性、所涉及的实体数摘要 (基于映射)。 每个事件都具有唯一的 ID。 事件的严重性取决于事件中包含的最严重警报。  

1. 若要查看有关事件中的警报和实体的更多详细信息, 请单击 "事件" 页中的 "**查看全部详细信息**", 并查看汇总事件信息的相关选项卡。  完整事件视图合并了警报中的所有证据、相关的警报和实体。

1. 在 "**警报**" 选项卡中, 查看警报本身 (触发警报时) 和超过设置的阈值。 您可以查看有关警报的所有相关信息-触发警报的查询、每个查询返回的结果数, 以及对警报运行行动手册的能力。 若要进一步向下钻取事件, 请单击命中次数。 这会打开生成结果的查询以及触发警报的结果 Log Analytics。

3. 在 "**实体**" 选项卡中, 可以看到作为警报规则定义的一部分映射的所有实体。 

4. 如果正在积极调查事件, 最好将事件状态设置为 "**正在进行**", 直到将其关闭。 你还可以关闭事件, 其中**关闭的解决**是指出事件已被处理的事件的状态, 而**关闭消除**是不需要处理的事件的状态。 需要说明用于关闭事件的理由。

5. 可以将事件分配给特定用户。 对于每个事件, 可以通过设置 "事件**所有者**" 字段来分配一个所有者。 所有事件都作为 "未分配" 启动。 你可以进入事件并按你的名称筛选, 以查看你拥有的所有事件。 

5. 单击 "**调查**", 查看通过修正步骤进行的调查映射和违规的范围。 



## <a name="respond-to-threats"></a>应对威胁

Azure Sentinel 提供了两个主要选项, 用于使用行动手册来响应威胁。 你可以将操作手册设置为在触发警报时自动运行, 或者可以手动运行操作手册以响应警报。

- 你可以将操作手册设置为在你配置操作手册时触发警报时自动运行。 

- 可以通过单击 "**查看行动手册**", 然后选择要运行的操作手册, 手动从警报内运行操作手册。




## <a name="next-steps"></a>后续步骤
在本教程中, 已学习如何使用 Azure Sentinel 来调查事件。 继续学习有关[如何使用自动行动手册来响应威胁](tutorial-respond-threats-playbook.md)的教程。
> [!div class="nextstepaction"]
> [应对威胁](tutorial-respond-threats-playbook.md)以自动应对威胁。

