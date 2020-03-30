---
title: 将赛门铁克 ICDx 数据连接到 Azure 哨兵*微软文档
description: 了解如何将赛门铁克 ICDx 数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588087"
---
# <a name="connect-your-symantec-icdx-appliance"></a>连接赛门铁克 ICDx 设备 



赛门铁克 ICDx 连接器允许您轻松地将所有赛门铁克安全解决方案日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 这使您可以更深入地了解组织的网络，并改进您的安全操作功能。 赛门铁克 ICDx 和 Azure 哨兵之间的集成利用了 REST API。


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-symantec-icdx"></a>配置和连接赛门铁克 ICDx 

赛门铁克 ICDx 可以直接集成日志并将其导出到 Azure Sentinel。

1. 打开 ICDx 管理控制台以添加 Microsoft Azure Sentinel（日志分析）转发器。
2. 在 ICDx 导航栏上，单击"**配置**"。 
3. 在 **"配置"** 屏幕顶部，单击 **"转发器**"。
4. 在**转发器**下，在 Microsoft Azure 哨兵（日志分析）旁边，单击"**添加**"。 
4. 在**Microsoft Azure 哨兵（日志分析）** 窗口中，单击"**显示高级**"。 
5. 在扩展到 Microsoft Azure 哨兵（日志分析）窗口的顶部，执行以下操作：
    -   **名称**： 为不超过 30 个字符的转发器键入名称。 选择唯一、有意义的名称。 此名称将显示在 **"配置"** 屏幕上的转发器列表中和**仪表板**屏幕上的仪表板中。 例如：微软 Azure 日志分析东部。 此字段是必填的。
    -   **说明**： 键入转发器的说明。 此说明也显示在 **"配置**"屏幕上的转发器列表中。 包括要转发的事件类型和需要检查数据的组等详细信息。
    -   **启动类型**：为转发器配置选择启动方法。 您的选项是手动和自动的。<br>默认为“自动”。 
6. 在 **"事件"** 下，执行以下操作： 
    - **源**： 选择要转发事件的一个或多个存档。 您可以选择活动收集器存档（包括公共存档）、孤立收集器存档（即已删除的收集器的存档）、ICDx 接收器存档或系统存档。 <br>默认值为"通用存档"。
      > [!NOTE]
      > ICDx 接收器存档按名称单独列出。 
 
    - **筛选器**：添加一个筛选器，指定要转发的事件子集。 执行下列操作之一：
        - 要选择筛选器条件，请单击"类型、属性、运算符"和"值"。 
        - 在"筛选器"字段中，查看筛选器条件。 您可以直接在字段中编辑它，也可以根据需要将其删除。
        - 单击 AND 或 OR 以添加到筛选器条件。
        - 您还可以单击"保存的查询"以应用已保存的查询。
    - **包含属性**：键入要包含在转发数据中的属性的逗号分隔列表。 包含的属性优先于排除的属性。
    - **排除属性**：键入要从转发的数据中排除的属性的逗号分隔列表。
    - **批处理大小**：选择每个批处理要发送的事件数。 您的选项是 10、50、100、500 和 1000。<br>默认值为 100。 
    - **速率限制**：选择转发事件的速率，以每秒事件表示。 您的选项是无限，500，1000，5000，10000。 <br> 默认值为 5000。 
7. 在**Azure 目标**下，执行以下操作： 
    - **工作区 ID**：从下面粘贴工作区 ID。 此字段是必填的。
    - **主键**：从下面粘贴主密钥。 此字段是必填的。
    - **自定义日志名称**：在要转发事件的 Microsoft Azure 门户日志分析工作区中键入自定义日志名称。 默认值为赛门铁克ICDx。 此字段是必填的。
8. 单击 *"保存*"以完成转发器配置。 
9. 要启动转发器，请在 **"选项**"下单击 **"更多**"，然后**开始**。
10. 要在日志分析中为赛门铁克 ICDx 事件使用相关架构，请搜索**SymantecICDx_CL**。


## <a name="validate-connectivity"></a>验证连接

可能需要 20 分钟以上，直到日志开始出现在日志分析中。 



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将赛门铁克 ICDx 连接到 Azure 哨兵。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


