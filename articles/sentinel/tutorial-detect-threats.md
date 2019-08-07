---
title: 用 Azure Sentinel Preview 调查警报 |Microsoft Docs
description: 使用本教程了解如何使用 Azure Sentinel 调查警报。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2cc33a9ac55ae9e906d88b72476d4b5ee244d2c8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780428"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>教程：用 Azure Sentinel Preview 检测威胁

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

将[数据源连接](quickstart-onboard.md)到 Azure Sentinel 后, 需要在出现可疑情况时收到通知。 为了使你能够执行此操作, Azure Sentinel 使你能够创建高级警报规则, 这些规则可生成可分配的事件, 并可用于深入调查环境中的异常和威胁。 

本教程可帮助你检测 Azure Sentinel 的威胁。
> [!div class="checklist"]
> * 创建检测规则
> * 自动响应威胁

## <a name="create-detection-rules"></a>创建检测规则

若要调查事件, 首先必须创建检测规则。 

> [!NOTE]
> Azure Sentinel 中生成的警报通过[Microsoft Graph 安全机制](https://aka.ms/securitygraphdocs)提供。 请参阅[Microsoft Graph 安全警报文档](https://aka.ms/graphsecurityreferencebetadocs), 以了解更多详细信息和集成合作伙伴。

检测规则基于你希望立即了解的环境中可能可疑的威胁类型和异常情况, 确保它们出现、调查和修正。 

1. 在 Azure Sentinel 下的 Azure 门户中, 选择 "**分析**"。

   ![分析](./media/tutorial-detect-threats/alert-rules.png)

2. 在顶部菜单栏中, 单击 " **+ 添加**"。  

   ![创建警报规则](./media/tutorial-detect-threats/create-alert-rule.png)

3. 在 "**创建警报规则**" 下, 提供描述性名称, 并根据需要设置**严重性**。 

4. 在 Log Analytics 中创建查询, 然后将其粘贴到 "**设置警报规则**" 字段中。 下面是一个示例查询, 当在 Azure 活动中创建异常数量的资源时, 该查询将向你发出警报。

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > 查询长度应介于1到10000个字符之间, 并且不能包含 "search *" 和 "union *"。


5. 在 "**实体映射**" 部分中, 使用 "**实体类型**" 下的字段将查询中的列映射到 Azure Sentinel 识别的实体字段。 对于每个字段, 将在 Log Analytics 中创建的查询中的相关列映射到相应的实体字段。 选择**属性**下的相关列名称。 每个实体都包含多个字段, 例如 SID、GUID 等。您可以根据任何字段映射实体, 而不只是较高级别实体。

6. 定义**警报触发器**下的警报触发器条件。 这会定义触发警报的条件。 

7. 设置运行查询的**频率, 频率**为每5分钟一次, 或每天不经常出现一次。 

8. 设置时间**段**来控制运行查询的数据的时间范围, 例如, 它可以每小时在60分钟的数据上运行。

9. 还可以设置**抑制**。 当您想要停止为同一事件触发的重复警报时, 禁止显示很有用。 通过这种方式, 您可以阻止在特定期间触发的警报。 这可以帮助你避免相同事件的重复警报, 并允许你在一段时间内取消连续警报。 例如, 如果将**警报计划** **频率**设置为60分钟, 并且**警报计划期间**设置为两小时, 并且查询结果超过定义的阈值, 则会触发警报两次 (一次检测到)过去的60分钟, 并且在采样数据的2小时内的前60分钟内再次。 我们建议, 如果触发警报, 则抑制应为在警报周期内设置的时间量。 在我们的示例中, 你可能想要将抑制设置为60分钟, 以便仅对最近一小时内发生的事件触发警报。

8. 将查询粘贴到 "**设置警报规则**" 字段之后, 可以立即在 "**逻辑警报模拟**" 下看到警报模拟, 以便您了解将在警报的特定时间间隔内生成的数据量已创建。 这将取决于你为**频率**和**阈值**设置的内容。 如果你发现, 你的警报会过于频繁地触发, 你需要将结果数设置得更高, 使其高于平均基线。

9. 单击 "**创建**" 初始化警报规则。 创建警报后, 会创建一个包含该警报的事件。 可以在 "**安全分析**" 选项卡中看到定义的检测规则作为行。你还可以查看每个规则的匹配项的数目-触发的警报。 从此列表中, 你可以启用、禁用或删除每个规则。 您还可以右键选择每个警报行末尾的省略号 (...), 以编辑、禁用、克隆、显示匹配项或删除规则。 "**分析**" 页是所有活动警报规则的库, 包括你启用的模板以及基于模板创建的警报规则。

1. 可以在 "**事件**" 页中查看警报规则的结果, 你可以在其中进行会审、[调查事件](tutorial-investigate-cases.md)并修正威胁。



## <a name="automate-threat-responses"></a>自动响应威胁

SIEM/SOC 团队可能会定期收到海量的安全警报。 生成的警报量如此之大，以致于现有的安全管理员不堪重负。 这往往会导致以下情况：无法调查很多警报，使得组织易于在未注意到的情况下遭受攻击。 

这些警报中的许多（如果不是大多数）警报都符合定期模式，可通过特定的修正操作和定义的修正操作进行处理。 Azure Sentinel 已允许在 Playbook 中定义修正操作。 此外，还可以将实时自动化设置为 Playbook 定义的一部分，以便能够完全自动化对特定安全警报的定义响应。 使用实时自动化，响应团队可以大幅减少其工作量，因为他们可以完全自动化对重复出现的警报类型的常规响应，从而让你可以更专注于处理独特警报，分析模式和搜寻威胁等活动。

若要实现自动响应，请执行以下操作：

1. 选择要对其实现自动响应的警报。
1. 从 Azure Sentinel 工作区导航菜单中，选择“分析”。
1. 选择要自动响应的警报。 
1. 在“编辑警报规则”页的“实时自动化”下，选择要在匹配此警报规则时运行的“触发的 Playbook”。
1. 选择**保存**。

   ![实时自动化](./media/tutorial-detect-threats/rt-configuration.png)


此外, 还可以通过从警报内运行操作手册手动修正警报, 方法是单击 "**查看行动手册**", 然后选择要运行的操作手册。 若要了解如何创建新的操作手册或编辑现有的操作手册, 请参阅使用[Azure Sentinel 中的行动手册](tutorial-respond-threats-playbook.md)。



## <a name="next-steps"></a>后续步骤
在本教程中, 已学习如何使用 Azure Sentinel 来检测威胁。 

若要了解如何自动响应威胁,[如何使用自动行动手册应对威胁](tutorial-respond-threats-playbook.md)。
> [!div class="nextstepaction"]
> [应对威胁](tutorial-respond-threats-playbook.md)以自动应对威胁。

