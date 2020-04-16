---
title: Azure 事件网格中的系统主题
description: 在 Azure 事件网格中描述系统主题。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393152"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure 事件网格中的系统主题
在为 Azure 事件源创建第一个事件订阅时，Azure 事件网格服务将创建系统主题。 目前，事件网格不会为 2020 年 3 月 15 日之前创建的主题源创建系统主题。 对于在此日期或之后创建的所有主题源，事件网格会自动创建系统主题。 本文介绍 Azure 事件网格中的**系统主题**。

> [!NOTE]
> 此功能当前未为 Azure 政府云启用。 

## <a name="overview"></a>概述
当您为 Azure 事件源（如 Azure 存储帐户）创建第一个事件订阅时，订阅的预配过程将创建**Microsoft**类型的其他资源。 删除 Azure 事件源的最后一个事件订阅时，系统主题将自动删除。

系统主题不适用于自定义主题方案，即事件网格主题和事件网格域。 

## <a name="location"></a>位置
对于位于特定区域/位置中的 Azure 事件源，系统主题将创建与 Azure 事件源相同的位置。 例如，如果为美国东部的 Azure Blob 存储创建事件订阅，则系统主题将在美国东部创建。 对于全局 Azure 事件源（如 Azure 订阅、资源组或 Azure 地图），事件网格将在**全局**位置创建系统主题。 

## <a name="resource-group"></a>资源组 
通常，系统主题是在 Azure 事件源位于的同一资源组中创建的。 对于在 Azure 订阅范围内创建的事件订阅，系统主题将在资源组 **"默认事件网格**"下创建。 如果资源组不存在，Azure 事件网格会在创建系统主题之前创建它。 

当您尝试删除具有存储帐户的资源组时，您将在受影响的资源列表中看到系统主题。  

![删除资源组](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [自定义主题](custom-topics.md)
- [域](event-domains.md)