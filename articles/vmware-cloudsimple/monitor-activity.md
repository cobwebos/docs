---
title: 监视私有云活动
titleSuffix: Azure VMware Solution by CloudSimple
description: 介绍 Azure VMware 解决方案中通过 CloudSimple 环境在活动中提供的信息，包括警报、事件、任务和审核。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1228f3a54e02d4fe7a5133e2bfba55c38e34718a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459198"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>按 CloudSimple 活动监视 VMware 解决方案

CloudSimple 活动日志提供对 CloudSimple 门户上完成的操作的见解。  此列表包括警报、事件、任务和审核。  使用活动日志来确定执行时间和执行哪些操作。  活动日志不包括用户完成的任何读取操作。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

访问[CloudSimple 门户](access-cloudsimple-portal.md)。

## <a name="activity-information"></a>活动信息

若要访问活动页，请选择侧菜单上的 "**活动**"。

![活动页概述](media/activity-page-overview.png)

若要查看有关活动页上的任何活动的详细信息，请选择该活动。 "详细信息" 面板将在右侧打开。 面板中的操作取决于活动的类型。 单击 " **X** " 关闭面板。

单击列标题可对显示进行排序。  可以筛选要查看的特定值的列。  单击 "**下载为 CSV** " 图标，下载活动报告。

## <a name="alerts"></a>警报

警报是 CloudSimple 环境中任何重要活动的通知。  警报包括影响计费或用户访问的事件。

若要确认警报并将其从列表中删除，请从列表中选择一个或多个，然后单击 "**确认**"。

以下信息列可用于警报。 单击 "**编辑列**"，然后选择要查看的列。

| 柱形图​​ | Description |
------------ | ------------- |
| 警报类型 | 警报的类别。|
| 时间 | 警报发生的时间。 |
| 严重性 | 警报的重要性。|
| 资源名称 | 分配给资源的名称，如私有云名称。 |
| 资源类型 | 资源类别：私有云、云架。 |
| 资源 ID | 资源的标识符。 |
| Description | 触发警报的说明。 |
| 已确认 | 指示是否确认了警报。 |

## <a name="events"></a>活动

事件显示 CloudSimple 门户上的用户和系统活动。 "事件" 页列出与特定资源关联的活动和影响的严重性。

以下信息列可用于警报。 单击 "**编辑列**"，然后选择要查看的列。

| 柱形图​​ | Description |
------------ | ------------- |
| 时间 | 事件发生的日期和时间。 |
| 事件类型 | 标识事件的数值代码。 |
| 严重性 | 事件严重性。|
| 资源名称 | 分配给资源的名称，如私有云名称。 |
| 资源类型 | 资源类别：私有云、云架。 |
| Description | 触发警报的说明。 |

## <a name="tasks"></a>任务

任务是需要30秒或更长时间才能完成的私有云活动。 （预计时间不到30秒的活动仅作为事件报告。）打开 "任务" 页面以跟踪私有云的任务进度。

以下信息列可用于警报。 单击 "**编辑列**"，然后选择要查看的列。

| 柱形图​​ | Description |
------------ | ------------- |
| 任务 ID | 任务的唯一标识符。 |
| 操作 | 任务执行的操作。 |
| 用户 | 用户已分配完成任务。 |
| 资源名称 | 分配给资源的名称。 |
| 资源类型 | 资源类别：私有云、云架。 |
| 资源 ID | 资源的标识符。 |
| 开始 | 任务的开始时间。 |
| 结束 | 任务的结束时间。 |
| 状态 | 当前任务状态。 |
| 已用时间 | 完成任务所用的时间（如果已完成）或当前正在进行（如果正在进行）。 |
| Description | 任务说明。 |

## <a name="audit"></a>审核

审核日志跟踪用户活动。 你可以使用审核日志来监视所有用户的用户活动。

以下信息列可用于警报。 单击 "**编辑列**"，然后选择要查看的列。

| 柱形图​​ | Description |
------------ | ------------- |
| 时间 | 审核条目的时间。 |
| 操作 | 任务执行的操作。 |
| 用户 | 分配给任务的用户。 |
| 资源名称 | 分配给资源的名称。 |
| 资源类型 | 资源类别：私有云、云架。 |
| 资源 ID | 资源的标识符。 |
| 结果 | 活动的结果，如**Success**。 |
| Time Taken | 完成任务的时间。 |
| Description | 操作的说明。 |

## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的详细信息
