---
title: 了解 Azure 中企业的管理员角色 | Microsoft Docs
description: 了解 Azure 中的企业管理员角色。
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2018
ms.author: banders
ms.openlocfilehash: 98ed28af8df246549fb521a81f1968e1f5c28cc4
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901558"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>了解 Azure 中的 Azure 企业协议管理角色

签署了企业协议 (EA) 的 Azure 客户可以分配五个不同的管理角色，协助管理组织的使用情况和开支：

- 企业管理员
- 企业管理员（只读）
- 部门管理员
- 部门管理员（只读）
- 帐户所有者
 
这些角色专用于管理 Azure 企业协议，是在 Azure 用于控制资源访问权限的内置角色的基础上添加的。 有关详细信息，请参阅 [Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)。

以下部分介绍每个角色的限制和功能。

## <a name="user-limit-for-admin-roles"></a>管理员角色的用户限制

|角色| 用户限制|
|---|---|
|企业管理员|不受限制|
|企业管理员（只读）|不受限制|
|部门管理员|不受限制|
|部门管理员（只读）|不受限制|
|帐户所有者|每个帐户 1 个<sup>1</sup>|

<sup>1</sup> 每个帐户需要一个唯一的 Microsoft 帐户，或者工作或学校帐户。

## <a name="organization-structure-and-permissions-by-role"></a>组织结构和权限（按角色）

|任务| 企业管理员|企业管理员（只读）|部门管理员|部门管理员（只读）|帐户所有者|
|---|---|---|---|---|---|
|查看企业管理员|✔|✔|✘|✘|✘|
|添加或删除企业管理员|✔|✘|✘|✘|✘|
|查看通知联系人<sup>2</sup> |✔|✔|✘|✘|✘|
|添加或删除通知联系人<sup>2</sup> |✔|✘|✘|✘|✘|
|创建并管理部门 |✔|✘|✘|✘|✘|
|查看部门管理员|✔|✔|✔|✔|✘|
|添加或删除部门管理员|✔|✔|✔|✘|✘|
|查看注册的帐户 |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|将帐户添加到注册并更改帐户所有者|✔|✘|✔<sup>3</sup>|✘|✘|
|创建并管理订阅和订阅权限|✘|✘|✘|✘|✔|

- <sup>2</sup> 将会向通知联系人发送有关 Azure 企业协议的电子邮件通信。
- <sup>3</sup> 任务局限于部门中的帐户。


## <a name="usage-and-costs-access-by-role"></a>按角色访问使用情况和成本

|任务| 企业管理员|企业管理员（只读）|部门管理员|部门管理员（只读） |帐户所有者|
|---|---|---|---|---|---|
|查看信用额度余额（包括货币承诺）|✔|✔|✘|✘|✘|
|查看部门支出配额|✔|✔|✘|✘|✘|
|设置部门支出配额|✔|✘|✘|✘|✘|
|查看组织的 EA 价目表|✔|✔|✘|✘|✘|
|查看使用情况和成本详细信息|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|管理 Azure 门户中的资源|✘|✘|✘|✘|✔|

- <sup>4</sup> 要求企业管理员在企业门户中启用“DA 视图费用”策略。 然后，部门管理员就可以查看部门的成本详细信息。
- <sup>5</sup> 要求企业管理员在企业门户中启用“AO 视图费用”策略。 然后，帐户所有者就可以查看帐户的成本详细信息。


## <a name="pricing-in-azure-portal"></a>Azure 门户中的定价

可能会在 Azure 门户中看到不同的定价，具体取决于你的管理角色，以及企业管理员设置的视图费用策略。 企业门户中有下述两个策略，这两个策略影响你在 Azure 门户中看到的定价：

- DA 视图费用
- AO 视图费用

若要了解如何设置这些策略，请参阅[管理 Azure 账单信息的访问权限](billing-manage-access.md)。

下表显示了企业协议管理角色之间的关系、视图费用策略、Azure 门户中基于角色的访问控制 (RBAC) 角色，以及在 Azure 门户中看到的定价。 企业管理员始终可以查看基于组织的 EA 定价的使用情况详细信息。 但是，部门管理员和帐户所有者看到的定价视图并不相同，具体取决于视图费用策略以及二者的 RBAC 角色。 下表中列出的部门管理员角色是指部门管理员角色和部门管理员（只读）角色。

|企业协议管理员角色|角色的视图费用策略|RBAC 角色|定价视图|
|---|---|---|---|
|帐户所有者或部门管理员|✔ 已启用|所有者|组织的 EA 定价|
|帐户所有者或部门管理员|✘ 已禁用|所有者|零售定价|
|帐户所有者或部门管理员|✔ 已启用 |无|无定价|
|帐户所有者或部门管理员|✘ 已禁用 |无|无定价|
|无|不适用 |所有者|零售定价|

在企业门户中设置企业管理员角色和视图费用策略。 RBAC 角色可以在 Azure 门户中更新。 有关详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤

- [管理对 Azure 账单信息的访问权限](billing-manage-access.md)
- [使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)
- [Azure 资源的内置角色](../role-based-access-control/built-in-roles.md)
