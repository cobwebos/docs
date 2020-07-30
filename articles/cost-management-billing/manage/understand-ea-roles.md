---
title: 了解 Azure 中企业的管理员角色
description: 了解 Azure 中的企业管理员角色。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: banders
ms.openlocfilehash: 2cd786304180369fee39c7b362f27a44728f3ad8
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87124334"
---
# <a name="managing-azure-enterprise-roles"></a>管理 Azure 企业角色

签署了企业协议 (EA) 的 Azure 客户可以分配五个不同的管理角色，协助管理组织的使用情况和开支：

- 企业管理员
- 企业管理员（只读）<sup>1</sup>
- 部门管理员
- 部门管理员（只读）
- 帐户所有者<sup>2</sup>

<sup>1</sup> EA 合同的“收票方”联系人将在此角色下。

<sup>2</sup> 不能在 Azure EA 门户中添加或更改“收票方”联系人，并且将根据在协议级别上设置为“收票方”联系人的用户将其添加到 EA 注册中。 若要更改“收票方”联系人，需要通过合作伙伴/软件顾问向区域运营中心 (ROC) 发出请求。

由注册预配期间设置的第一位注册管理员确定“收票方”联系人帐户的身份验证类型。 当“收票方”联系人作为只读管理员添加到 EA 门户时，将进行 Microsoft 帐户身份验证。 

例如，如果初始身份验证类型设置为“混合”，则 EA 将被添加为 Microsoft 帐户，而“收票方”联系人将具有只读 EA 管理员权限。 如果 EA 管理员未批准现有“收票方”联系人的 Microsoft 帐户授权，EA 管理员可能会删除相关用户，并要求客户将用户重新添加为只读管理员，只在 EA 门户中的注册级别设置工作或学校帐户。

这些角色专用于管理 Azure 企业协议，是在 Azure 用于控制资源访问权限的内置角色的基础上添加的。 有关详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

以下部分介绍每个角色的限制和功能。

## <a name="user-limit-for-admin-roles"></a>管理员角色的用户限制

|角色| 用户限制|
|---|---|
|企业管理员|无限制|
|企业管理员（只读）|无限制|
|部门管理员|无限制|
|部门管理员（只读）|无限制|
|帐户所有者|每个帐户 1 个<sup>3</sup>|

<sup>3</sup> 每个帐户需要唯一的 Microsoft 帐户，或者工作或学校帐户。

## <a name="organization-structure-and-permissions-by-role"></a>组织结构和权限（按角色）

|任务| 企业管理员|企业管理员（只读）|部门管理员|部门管理员（只读）|帐户所有者| Partner|
|---|---|---|---|---|---|---|
|查看企业管理员|✔|✔|✘|✘|✘|✔|
|添加或删除企业管理员|✔|✘|✘|✘|✘|✘|
|查看通知联系人<sup>4</sup> |✔|✔|✘|✘|✘|✔|
|添加或删除通知联系人<sup>4</sup> |✔|✘|✘|✘|✘|✘|
|创建并管理部门 |✔|✘|✘|✘|✘|✘|
|查看部门管理员|✔|✔|✔|✔|✘|✔|
|添加或删除部门管理员|✔|✘|✔|✘|✘|✘|
|查看注册的帐户 |✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|将帐户添加到注册并更改帐户所有者|✔|✘|✔<sup>5</sup>|✘|✘|✘|
|创建并管理订阅和订阅权限|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> 将会向通知联系人发送有关 Azure 企业协议的电子邮件通信。
- <sup>5</sup> 任务局限于部门中的帐户。

## <a name="add-a-new-enterprise-administrator"></a>添加新的企业管理员

企业管理员拥有管理 Azure EA 注册的大部分特权。 设置 EA 协议时，会创建初始的 Azure EA 管理员。 但是，你随时可以添加或删除新管理员。 新管理员只能由现有的管理员添加。 有关添加更多企业管理员的详细信息，请参阅[创建另一个企业管理员](ea-portal-get-started.md#create-another-enterprise-administrator)。有关计费配置文件角色和任务的详细信息，请参阅[计费配置文件角色和任务](understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="update-account-owner-state-from-pending-to-active"></a>将帐户所有者状态从“挂起”更新为“活动”

首次将新的帐户所有者 (AO) 添加到 Azure EA 注册时，其状态将显示为“挂起”。 当新帐户所有者收到激活欢迎电子邮件时，他们可以登录以激活其帐户。 激活帐户后，其帐户状态将从“挂起”更新为“可用” 。 帐户所有者需要阅读“警告”消息，然后选择“继续”。 系统可能会提示新用户输入其名字和姓氏来创建商务帐户。 如果出现此提示，他们必须添加所需的信息以继续，然后其帐户将会激活。

## <a name="add-a-department-admin"></a>添加部门管理员

Azure EA 管理员创建部门后，Azure 企业管理员可以添加部门管理员并将每个管理员关联到某个部门。 部门管理员可以创建新帐户。 需要添加新帐户才能创建 Azure EA 订阅。

有关添加部门管理员的详细信息，请参阅[创建 Azure EA 部门管理员](ea-portal-get-started.md#add-a-department-administrator)。

## <a name="usage-and-costs-access-by-role"></a>按角色访问使用情况和成本

|任务| 企业管理员|企业管理员（只读）|部门管理员|部门管理员（只读） |帐户所有者| Partner|
|---|---|---|---|---|---|---|
|查看额度余额（包括 Azure 预付款）|✔|✔|✘|✘|✘|✔|
|查看部门支出配额|✔|✔|✘|✘|✘|✔|
|设置部门支出配额|✔|✘|✘|✘|✘|✘|
|查看组织的 EA 价目表|✔|✔|✘|✘|✘|✔|
|查看使用情况和成本详细信息|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|管理 Azure 门户中的资源|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> 要求企业管理员在企业门户中启用“DA 视图费用”策略。 然后，部门管理员就可以查看部门的成本详细信息。
- <sup>7</sup> 要求企业管理员在企业门户中启用“AO 视图费用”策略。 然后，帐户所有者就可以查看帐户的成本详细信息。

## <a name="see-pricing-for-different-user-roles"></a>请参阅不同用户角色的定价

可能会在 Azure 门户中看到不同的定价，具体取决于你的管理角色，以及企业管理员设置的视图费用策略。 企业门户中有下述两个策略，这两个策略影响你在 Azure 门户中看到的定价：

- DA 视图费用
- AO 视图费用

若要了解如何设置这些策略，请参阅[管理 Azure 账单信息的访问权限](manage-billing-access.md)。

下表显示了企业协议管理角色之间的关系、视图费用策略、Azure 门户中基于角色的访问控制 (RBAC) 角色，以及在 Azure 门户中看到的定价。 企业管理员始终可以查看基于组织的 EA 定价的使用情况详细信息。 但是，部门管理员和帐户所有者看到的定价视图并不相同，具体取决于视图费用策略以及二者的 RBAC 角色。 下表中列出的部门管理员角色是指部门管理员角色和部门管理员（只读）角色。

|企业协议管理员角色|角色的视图费用策略|RBAC 角色|定价视图|
|---|---|---|---|
|帐户所有者或部门管理员|✔ 已启用|所有者|组织的 EA 定价|
|帐户所有者或部门管理员|✘ 已禁用|所有者|零售定价|
|帐户所有者或部门管理员|✔ 已启用 |none|无定价|
|帐户所有者或部门管理员|✘ 已禁用 |none|无定价|
|无|不适用 |所有者|零售定价|

在企业门户中设置企业管理员角色和视图费用策略。 RBAC 角色可以在 Azure 门户中更新。 有关详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../../role-based-access-control/role-assignments-portal.md)。



## <a name="next-steps"></a>后续步骤

- [管理对 Azure 账单信息的访问权限](manage-billing-access.md)
- [使用 RBAC 和 Azure 门户管理访问权限](../../role-based-access-control/role-assignments-portal.md)
- [Azure 内置角色](../../role-based-access-control/built-in-roles.md)
