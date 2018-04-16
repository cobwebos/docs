---
title: 如何在 Privileged Identity Management 中对 Azure 资源执行访问评审 | Microsoft Docs
description: 本文档介绍如何在 PIM 中对 Azure 资源执行访问评审。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: f1bcf114b997c3056016b84cafc28253ea1af28e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="resource-dashboards"></a>资源仪表板

“管理员视图”仪表板包含四个主要组件。 过去七天执行的资源角色激活操作的图形表示形式。 此数据对应于选定的资源，显示最常见角色（所有者、参与者、用户访问管理员）以及所有角色的激活情况。

在激活图形的右侧，有两个图表按分配类型显示了用户和组的角色分配分布情况。 选择图表中的切片会将值更改为百分比（或相反）。

![](media/azure-pim-resource-rbac/rbac-overview-top.png)

在图表下方，可以看到在过去 30 天获得了新角色分配的用户和组数（左侧），以及按分配总数排序的角色列表（降序）。

![](media/azure-pim-resource-rbac/role-settings.png)
