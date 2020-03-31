---
title: 监控私有云活动
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述 Azure VMware 解决方案（按云简单环境）中可用的活动信息，包括警报、事件、任务和审核。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019666"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>按云简单活动监控 VMware 解决方案

CloudSimple 活动日志提供了对云简单门户上执行的操作的洞察。  该列表包括警报、事件、任务和审核。  使用活动日志确定执行操作的人员、时间以及执行什么操作。  活动日志不包括用户完成的任何读取操作。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[云简单门户](access-cloudsimple-portal.md)。

## <a name="activity-information"></a>活动信息

要访问"活动"页面，请在侧菜单上选择 **"活动**"。

![活动页面概述](media/activity-page-overview.png)

要查看活动页面上任何活动的详细信息，请选择该活动。 右侧将打开详细信息面板。 面板中的操作取决于活动类型。 单击**X**关闭面板。

单击列标题对显示进行排序。  您可以筛选要查看的特定值的列。  单击"**下载为 CSV"** 图标下载活动报告。

## <a name="alerts"></a>警报

警报是云简单环境中任何重要活动的通知。  警报包括影响计费或用户访问的事件。

要确认警报并将其从列表中删除，请从列表中选择一个或多个警报，然后单击"**确认**"。

以下信息列可用于警报。 单击 **"编辑"列**并选择要查看的列。

| 列 | 描述 |
------------ | ------------- |
| 警报类型 | 警报类别。|
| 时间 | 发生警报的时间。 |
| 严重性 | 警报的重要性。|
| 资源名称 | 分配给资源的名称，如私有云名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 资源 ID | 资源的标识符。 |
| 描述 | 警报触发的原因的说明。 |
| 已确认 | 指示是否确认警报。 |

## <a name="events"></a>事件

事件在云简单门户上显示用户和系统活动。 "事件"页列出了与特定资源关联的活动以及影响的严重性。

以下信息列可用于警报。 单击 **"编辑"列**并选择要查看的列。

| 列 | 描述 |
------------ | ------------- |
| 时间 | 事件发生的日期和时间。 |
| 事件类型 | 标识事件的数字代码。 |
| 严重性 | 事件严重性。|
| 资源名称 | 分配给资源的名称，如私有云名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 描述 | 警报触发的原因的说明。 |

## <a name="tasks"></a>任务

任务是私有云活动，预计需要 30 秒或更长时间才能完成。 （预期需要少于 30 秒的活动仅报告为事件。打开"任务"页面，跟踪私有云的任务进度。

以下信息列可用于警报。 单击 **"编辑"列**并选择要查看的列。

| 列 | 描述 |
------------ | ------------- |
| 任务 ID | 任务的唯一标识符。 |
| Operation | 任务执行的操作。 |
| 用户 | 分配给完成任务的用户。 |
| 资源名称 | 分配给资源的名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 资源 ID | 资源的标识符。 |
| 开始 | 任务的开始时间。 |
| 结束 | 任务的结束时间。 |
| 状态 | 当前任务状态。 |
| 已使用的时间 | 任务完成（如果已完成）或当前正在执行的时间（如果正在进行）。 |
| 描述 | 任务说明。 |

## <a name="audit"></a>审核

审核日志跟踪用户活动。 您可以使用审核日志监视所有用户的用户活动。

以下信息列可用于警报。 单击 **"编辑"列**并选择要查看的列。

| 列 | 描述 |
------------ | ------------- |
| 时间 | 审核条目的时间。 |
| Operation | 任务执行的操作。 |
| 用户 | 分配给任务的用户。 |
| 资源名称 | 分配给资源的名称。 |
| 资源类型 | 资源类别：私有云、云机架。 |
| 资源 ID | 资源的标识符。 |
| 结果 | 活动的结果，如**成功**。 |
| 所走的时间 | 完成任务的时间。 |
| 描述 | 操作的说明。 |

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的更多
