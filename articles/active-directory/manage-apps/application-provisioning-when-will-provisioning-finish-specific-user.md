---
title: 找出特定的用户何时可以访问某个应用程序 | Microsoft Docs
description: 如何找出非常重要的用户何时可以访问已使用 Azure AD 配置用户预配的应用程序
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd6b70e7a4542a4cad2ee95fa280ddf8fbe6553
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310037"
---
# <a name="check-the-status-of-user-provisioning"></a>检查用户预配的状态

Azure AD 预配服务针对源系统和目标系统后, 跟定期增量周期运行初始的预配周期。 在配置应用的预配时，可以检查预配服务的当前状态并查看时，用户将能够访问应用。

## <a name="view-the-provisioning-progress-bar-preview"></a>查看预配进度栏 （预览版）

 上**预配**页的应用，可以查看 Azure AD 预配服务的状态。 **当前状态**在页面底部的部分显示了预配周期已开始预配用户帐户。 可以观看周期的进度、 已预配多少用户和组，并查看创建了多少个角色。

在初次配置自动预配**当前状态**在页面底部的部分显示的初始预配周期状态。 本部分将更新每个增量周期运行的时间。 显示以下详细信息：
- 预配周期 （初始或增量） 的类型，当前正在运行或最终完成的。
- 一个**进度栏**显示已完成的预配周期的百分比。 百分比反映预配的页的计数。 请注意，每个页面可以包含多个用户或组，因此所占百分比不直接关联到的用户数、 组或预配的角色。
- 一个**刷新**按钮可用于保持更新的视图。
- 数**用户**并**组**预配，以及创建的角色数。 在初始周期内，**用户**数向上计数按 1 和用户创建或更新，它向下计数按 1 时删除用户时。 在一个增量周期期间，用户更新不会影响**用户**计数; 仅当创建或删除用户时的更改数量。
- 一个**查看审核日志**链接，这会打开 Azure AD 审核日志的用户预配服务，包括预配状态为单个用户运行的所有操作的详细信息 (请参阅[使用审核日志](#use-audit-logs-to-check-a-users-provisioning-status)下面的部分)。

预配周期完成后**迄今为止的统计信息**部分显示的用户和组的已预配到日期，以及完成日期和最后一个周期的持续时间的累计数字。 **活动 ID**唯一标识的最新的预配周期。 **作业 ID**是预配作业的唯一标识符和特定于你的租户中的应用程序。

预配正在进行中可以查看在 Azure 门户中， **Azure Active Directory&gt;企业应用程序&gt;\[应用程序名称\]&gt;预配**选项卡。

![预配页进度栏](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>使用审核日志以检查用户的预配状态

若要查看选定用户的预配状态，请查阅 Azure AD 中的审核日志。 预配服务在用户运行的所有操作都记录在 Azure AD 审核日志。 这包括所有读取和写入操作对源和目标系统和用户数据读取或每个操作过程中写入的。

预配审核日志将可在 Azure 门户中，在**Azure Active Directory&gt;企业应用程序&gt;\[应用程序名称\]&gt;审核日志**选项卡。在“帐户预配”  类别上筛选日志，以仅查看该应用的预配事件。 可根据在属性映射中为用户配置的“匹配 ID”搜索用户。 

例如，如果在 Azure AD 端将“用户主体名称”或“电子邮件地址”配置为匹配属性，并且尚未预配的用户的值为“audrey@contoso.com”，然后在审核日志中搜索“audrey@contoso.com”，并查看返回的条目。

预配审核日志记录了预配服务执行的全部操作，包括：

* 查询 Azure AD 以找到预配作用域中分配的用户
* 查询目标应用以验证是否存在这些用户
* 比较系统之间的用户对象
* 根据比较结果在目标系统中添加、更新或禁用用户帐户

有关如何读取审核日志在 Azure 门户中的详细信息，请参阅[预配报告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>预配用户需要多长时间？
在使用自动用户预配的应用程序时，Azure AD 自动预配并更新根据类似的应用程序中的用户帐户[用户和组分配](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)通常在定期计划时间间隔，每隔 10 分钟。

若要将用户预配的、 给定用户所需的时间主要取决于是否预配作业正在运行初始同步还是增量同步。

- 有关**初始同步**，作业时间取决于许多因素，包括预配，范围内的用户和组的数量以及用户和组在源系统中的总数。 Azure AD 和应用之间的首次同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 本部分中的下文中汇总了影响初始同步性能的因素的完整列表。

- 有关**增量同步**初始同步后，作业时间往往更快 （例如在 10 分钟），因为预配服务已存储在提高性能的初始同步后表示这两种系统状态的水印后续同步。 作业时间取决于在该预配周期中检测到的更改数。 如果有 5,000 个用户或组成员身份更改，可以在单个增量的预配周期内完成作业。 

下表总结了常见的预配方案的同步时间。 在这些方案中，源系统是 Azure AD，目标系统是 SaaS 应用程序。 同步时间派生自 ServiceNow、 工作区、 Salesforce 和 G Suite 的 SaaS 应用程序的同步作业的统计分析。


| 作用域配置 | 作用域中的用户、组和成员 | 初始同步时间 | 增量同步时间 |
| -------- | -------- | -------- | -------- |
| 仅同步已分配的用户和组 |  < 1,000 |  < 30 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |  1,000 - 10,000 | 142 - 708 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |   10,000 - 100,000 | 1,170 - 2,340 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  < 1,000 | < 30 分钟  | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  1,000 - 10,000 | < 30 - 120 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  10,000 - 100,000  | 713 - 1,425 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户|  < 1,000  | < 30 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户 | 1,000 - 10,000  | 43 - 86 分钟 | < 30 分钟 |


对于“仅同步已分配的用户和组”  配置，可以使用以下公式来确定大概的最小和最大预计**初始同步**时间：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影响**初始同步**的完成时间的因素汇总：

- 预配范围内用户和组的总数。

- 源系统 (Azure AD) 中存在的用户、组和组成员的总数。

- 是否中预配范围内的用户与目标应用程序中的现有用户匹配，或者需要创建第一次。 为其所有用户都创建的第一次的同步作业需要在大约*两次长时间*作为同步的作业与现有用户为其匹配所有用户。

- [审核日志](check-status-user-account-provisioning.md)中的错误数。 如果有许多错误，并且预配服务已进入“隔离”状态，则性能较低。    

- 目标系统实现的请求速率限制。 某些目标系统实施请求速率限制和限制，这可能会影响在大规模同步操作期间的性能。 在这些情况下，太快地接收太多请求的应用可能会拖慢其响应速率或关闭连接。 为提高性能，连接器需要进行调整，以不高于应用可以应对的处理速率的速率来向应用发送请求。 Microsoft 构建的预配连接器进行此调整。 

- 已分配的组的数量和大小。 同步已分配的组比同步用户花费的时间要长。 已分配的组的数量和大小都会影响性能。 如果应用程序[为组对象同步启用了映射](customize-application-attributes.md#editing-group-attribute-mappings)，则除了用户之外，还会同步组名称和成员身份等组属性。 这些额外的同步比仅同步用户对象需要花费更长的时间。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 自动化 SaaS 应用程序用户预配和取消预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
