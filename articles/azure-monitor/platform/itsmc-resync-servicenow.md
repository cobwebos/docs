---
title: 如何手动修复 ServiceNow 同步问题
description: 重置与 ServiceNow 的连接，以便 Microsoft Azure 中的警报可以再次调用 ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 9d32b66b7e3ab27012ee671ba8d70735dce73884
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274271"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>如何手动修复 ServiceNow 同步问题

Azure 监视器可以连接到第三方 IT 服务管理 （ITSM） 提供程序。 ServiceNow 是这些提供商之一。 

出于安全原因，您可能需要刷新用于与 ServiceNow 连接的身份验证令牌。
使用以下同步过程重新激活连接并刷新令牌： 


1. 在顶部搜索横幅中搜索解决方案，然后选择相关解决方案

    ![新建连接](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 在解决方案屏幕中，在订阅筛选器中选择"全部"，然后按"服务台"进行筛选

    ![新建连接](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. 选择 ITSM 连接的解决方案。
1. 在左侧横幅中选择 ITSM 连接。

    ![新建连接](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 从列表中选择每个连接器。 
    1. 单击连接器名称以对其进行配置
    1. 删除不再使用的任何连接器

    1. 根据合作伙伴类型根据[这些定义](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)更新字段

    1. 单击同步

       ![新建连接](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 单击"保存"

        ![新建连接](media/itsmc-resync-servicenow/save-8bit.png)

f.    查看通知以查看流程是否成功 

## <a name="next-steps"></a>后续步骤

了解有关[IT 服务管理连接](itsmc-connections.md)的更多
