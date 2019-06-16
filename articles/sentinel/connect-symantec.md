---
title: 将 Symantec ICDX 数据连接到 Azure Sentinel 预览版 |Microsoft Docs
description: 了解如何将 Symantec ICDX 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: rkarlin
ms.openlocfilehash: 3b21371d6321b208b19ca8b2524308736c3ceca9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244340"
---
# <a name="connect-your-symantec-icdx-appliance"></a>连接你的 Symantec ICDX 设备 

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Symantec ICDX 连接器，可轻松地连接所有到 Symantec 安全解决方案日志在 Azure Sentinel，若要查看的仪表板、 创建自定义警报和改进的调查。 这为您提供了更详细地了解您组织的网络，并提高你的安全操作功能。 Symantec ICDX 和 Azure Sentinel 之间的集成，可以使用 REST api。


> [!NOTE]
> 数据将存储在其运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-symantec-icdx"></a>配置和连接 Symantec ICDX 

Symantec ICDX 可以集成并将日志导出 Azure Sentinel 到直接。

1. 打开 ICDX 管理控制台来添加 Microsoft Azure Sentinel (Log Analytics) 转发器。
2. 在 ICDx 导航栏中，单击**配置**。 
3. 在顶部**配置**屏幕上，单击**转发器**。
4. 下**转发器**，旁边 Microsoft Azure Sentinel (Log Analytics)，单击**添加**。 
4. 在中**Microsoft Azure Sentinel (Log Analytics)** 窗口中，单击**显示高级**。 
5. 在已展开的顶部到 Microsoft Azure Sentinel (Log Analytics) 窗口中，执行以下操作：
    -   **名称**：键入转发器具有不超过 30 个字符的名称。 选择一个唯一且有意义的名称。 此名称将显示在列表中的转发器上**配置**屏幕上的仪表板在**仪表板**屏幕。 例如：Microsoft Azure 日志分析东部。 此字段是必填的。
    -   **说明**：键入转发器的说明。 此说明也会出现在列表中的转发器在**配置**屏幕。 包括详细信息，例如正在转发的事件类型和需要检查的数据的组。
    -   **启动类型**:选择转发器配置的启动的方法。 你的选项是手动和自动。<br>默认值为自动。 
6. 下**事件**，执行以下操作： 
    - **源**：选择要从中转发事件的一个或多个存档。 您可以选择活动收集器存档文件 （包括常用的存档）、 孤立的收集器存档 （即，已删除收集器的存档）、 ICDx 接收方存档或系统存档。 <br>默认值是常见的存档。
      > [!NOTE]
      > 按名称的单独列出 ICDx 接收方存档。 
 
    - **筛选器**：添加筛选器，以指定要转发的事件的子集。 执行下列操作之一：
        - 若要选择筛选器条件，请单击类型、 属性、 运算符和值。 
        - 在筛选器字段中，查看您的筛选器条件。 可以直接在字段中对其进行编辑，或根据需要将其删除。
        - 单击和或将添加到筛选条件。
        - 您还可以单击已保存的查询以将应用保存的查询。
    - **包含属性**:键入要在转发数据中包含特性以逗号分隔的列表。 包括的属性优先于排除的属性。
    - **排除的属性**:键入要排除在转发数据的特性以逗号分隔列表。
    - **批大小**:选择要发送每个批处理的事件数。 你的选项是 10，50、 100、 500 和 1000年。<br>默认值为 100。 
    - **速率限制**:选择事件转发时，表示为每秒事件数的速率。 选项包括无限制，500、 1000、 5000、 10000。 <br> 默认值为 5000。 
7. 下**Azure 目标**，执行以下操作： 
    - **工作区 ID**:从下面粘贴工作区 ID。 此字段是必填的。
    - **主键**:从下面将主键。 此字段是必填的。
    - **自定义日志名称**:在 Microsoft Azure 门户 Log Analytics 工作区中向其访问将事件转发键入自定义日志名称。 默认值为 SymantecICDx。 此字段是必填的。
8. 单击*保存*完成转发器配置。 
9. 若要在启动该转发器，**选项**，单击**详细**，然后**启动**。
10. 若要使用 Log Analytics 中的 Symantec ICDX 事件相关的架构，搜索**SymantecICDX_CL**。


## <a name="validate-connectivity"></a>验证连接

它可能需要 1-2 20 分钟，直到你的日志开始在 Log Analytics 中显示。 



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Symantec ICDX 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。

