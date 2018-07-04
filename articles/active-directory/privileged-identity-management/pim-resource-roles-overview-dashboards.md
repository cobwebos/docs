---
title: 概述：在 Privileged Identity Management 中对 Azure 资源执行访问评审 | Microsoft 文档
description: 本文档介绍如何在 PIM 中对 Azure 资源执行访问评审。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b53ae66d927dfacd1048a6bd0fa9abdb6f2a7862
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751146"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>使用资源仪表板执行访问评审

可以使用资源仪表板在 Privileged Identity Management (PIM) 中对 Azure 资源执行访问评审。 “管理员视图”仪表板包含三个主要组件：

- 资源角色激活操作的图形表示形式。
- 两个图表按分配类型显示了角色分配分布情况。
- 与新角色分配相关的数据区域。

![管理员视图仪表板的屏幕截图，显示图形和图表](media/azure-pim-resource-rbac/rbac-overview-top.png)

![显示数据列表的“管理员视图”仪表板屏幕截图](media/azure-pim-resource-rbac/role-settings.png)

过去七天执行的资源角色激活操作的图形表示形式。 此数据对应于选定的资源，显示最常见角色（所有者、参与者、用户访问管理员）以及所有角色的激活情况。

在激活图形的右侧，有两个图表按分配类型显示了用户和组的角色分配分布情况。 选择图表中的切片会将值更改为百分比（或相反）。

在图表下方，可以看到在过去 30 天获得了新角色分配的用户和组数，以及按分配总数排序的角色列表（以降序排序）。


