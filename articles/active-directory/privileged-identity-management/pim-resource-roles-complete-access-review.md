---
title: Azure 资源的 Privileged Identity Management - 完成 Azure 资源的访问审阅 | Microsoft Docs
description: 介绍了如何完成 Azure 资源的访问审阅。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - 资源角色 - 完成访问审阅
[开始安全审阅](pim-resource-roles-start-access-review.md)后，特权角色管理员可以审阅特权访问。 Azure 资源的 Privileged Identity Management (PIM) 会自动发送一封电子邮件来提示用户审阅其访问。 如果用户未收到电子邮件，可以向他们发送[如何执行安全审阅](pim-resource-roles-perform-access-review.md)中的说明。

安全审阅期限结束，或者所有用户已完成其自审阅后，按照本文中的步骤管理审阅并查看结果。

## <a name="manage-security-reviews"></a>管理安全审阅
1. 转到 [Azure 门户](https://portal.azure.com/)，并在仪表板上选择“Azure 资源”应用程序。
2. 选择你的资源。
3. 选择仪表板的“访问审阅”部分。
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. 选择要管理的访问审阅。

在访问审阅的详细信息边栏选项卡上，有大量用于管理该审阅的选项。
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>停止
所有访问审阅都有结束日期，但可以使用“停止”按钮提前结束。 如果此时还有未审阅的用户，他们在停止审阅后将无法再得到审阅。 停止后，无法重新开始审阅。

### <a name="reset"></a>重置
你可以重置访问审阅来删除对其所做的所有决策。 一旦重置访问审阅，所有用户都将被重新标记为未审阅。 

### <a name="apply"></a>应用
结束访问审阅后，“应用”按钮将实现审阅结果，因为结束日期已到或已手动停止它。 如果在审阅中拒绝了用户的访问，在此步骤中将删除其角色分配。  

### <a name="delete"></a>删除
如果不想要进一步了解审阅，请将其删除。 “删除”按钮可从 PIM 应用程序中删除审阅。

## <a name="results"></a>结果
在“结果”选项卡上查看和下载审阅结果列表。![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>审阅者
查看现有访问审阅的审阅者以及为其添加审阅者。 提醒审阅者完成其审阅。
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



