---
title: 如何手动修复 ServiceNow 同步问题
description: 重置到 ServiceNow 的连接，以便 Microsoft Azure 中的警报可以再次调用 ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087928"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>如何手动修复 ServiceNow 同步问题

Azure Monitor 可以连接到第三方 IT 服务管理（ITSM）提供商。 ServiceNow 是这些提供商之一。

出于安全原因，可能需要刷新用于与 ServiceNow 连接的身份验证令牌。
使用以下同步过程重新激活连接并刷新令牌：


1. 在顶部搜索横幅中搜索解决方案，然后选择相关解决方案

    ![新建连接](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 在 "解决方案" 屏幕中，依次选择 "订阅" 筛选器中的 "全选" 和 "ServiceDesk"

    ![新建连接](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. 选择 ITSM 连接的解决方案。
1. 在左侧的横幅中选择 "ITSM 连接"。

    ![新建连接](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 从列表中选择每个连接器。 
    1. 单击连接器名称以对其进行配置
    1. 删除不再使用的任何连接器

    1. 根据你的合作伙伴类型根据[这些定义](./itsmc-connections.md)更新字段

    1. 单击 "同步"

       ![新建连接](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 单击 "保存"

        ![新建连接](media/itsmc-resync-servicenow/save-8bit.png)

f.    查看通知，查看过程是否成功完成 

## <a name="next-steps"></a>后续步骤

了解有关[IT 服务管理连接](itsmc-connections.md)的详细信息
