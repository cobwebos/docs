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
ms.openlocfilehash: bf6190ae35a573ab4b9a37f5cd1f2bd75f06ca46
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667156"
---
# <a name="check-the-status-of-user-provisioning"></a>检查用户设置的状态

Azure AD 预配服务对源系统和目标系统运行初始设置周期, 后跟定期增量循环。 配置应用的预配时, 可以检查预配服务的当前状态, 并查看用户何时可以访问应用。

## <a name="view-the-provisioning-progress-bar"></a>查看设置进度栏

 在应用的 "**设置**" 页上, 你可以查看 Azure AD 预配服务的状态。 页面底部的 "**当前状态**" 部分显示预配周期是否已开始预配用户帐户。 您可以查看周期的进度, 查看已设置的用户和组的数量, 并查看已创建的角色数。

首次配置自动预配时, 页面底部的 "**当前状态**" 部分会显示初始预配周期的状态。 此部分将在每次运行增量循环时更新。 将显示以下详细信息:
- 当前正在运行或最后完成的预配周期 (初始或增量) 的类型。
- 显示已完成的预配周期百分比的**进度栏**。 百分比反映预配的页的计数。 请注意, 每个页面可能包含多个用户或组, 因此该百分比并不与预配的用户、组或角色的数目直接相关。
- 可以使用 "**刷新**" 按钮来更新视图。
- 预配的**用户**和**组**的数量, 以及创建的角色数。 在初始周期期间, 用户在创建或更新用户时 **, 将按**1 计数, 并且在删除用户时, 将按1计数。 在增量循环期间, 用户更新不会影响**用户**计数;只有在创建或删除用户时, 才会更改此数字。
- "**查看审核日志**" 链接, 可打开 Azure AD 审核日志, 以获取有关用户预配服务运行的所有操作的详细信息, 包括每个用户的预配状态 (请参阅下面的 "[使用审核日志](#use-audit-logs-to-check-a-users-provisioning-status)" 部分)。

设置周期完成后, "**统计信息截止日期**" 部分显示已设置为 "日期" 的用户和组的累计数量, 以及最后一个周期的完成日期和持续时间。 **活动 ID**唯一标识最新的设置周期。 **作业 ID**是预配作业的唯一标识符, 特定于租户中的应用。

可以在 Azure 门户的 " **Azure Active Directory &gt;企业&gt; \[\] 应用程序""预配&gt;**  " 选项卡中查看设置进度。

![设置页进度栏](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-audit-logs-to-check-a-users-provisioning-status"></a>使用审核日志来检查用户的预配状态

若要查看选定用户的预配状态，请查阅 Azure AD 中的审核日志。 用户预配服务运行的所有操作都记录在 Azure AD 审核日志中。 这包括对源系统和目标系统执行的所有读取和写入操作, 以及每个操作期间读取或写入的用户数据。

可以在 Azure 门户的 " **Azure Active Directory &gt;企业&gt; \[\] 应用程序""审核日志"选项&gt;** 卡中访问预配审核日志。在“帐户预配”类别上筛选日志，以仅查看该应用的预配事件。 可根据在属性映射中为用户配置的“匹配 ID”搜索用户。 

例如，如果在 Azure AD 端将“用户主体名称”或“电子邮件地址”配置为匹配属性，并且尚未预配的用户的值为“audrey@contoso.com”，然后在审核日志中搜索“audrey@contoso.com”，并查看返回的条目。

预配审核日志记录了预配服务执行的全部操作，包括：

* 查询 Azure AD 以找到预配作用域中分配的用户
* 查询目标应用以验证是否存在这些用户
* 比较系统之间的用户对象
* 根据比较结果在目标系统中添加、更新或禁用用户帐户

有关如何读取 Azure 门户中的审核日志的详细信息, 请参阅[预配报告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>预配用户需要多长时间？
在应用程序中使用自动用户预配时, Azure AD 会按定期计划的时间间隔 (通常每10分钟) 在应用中自动预配和更新用户帐户 (如[用户和组分配](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal))。

预配给定用户所用的时间主要取决于你的预配作业是正在运行初始同步还是增量同步。

- 对于**初始同步**, 作业时间取决于许多因素, 包括用于预配的作用域中的用户和组的数量, 以及源系统中的用户和组的总数。 Azure AD 和应用之间的首次同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 本部分中的下文中汇总了影响初始同步性能的因素的完整列表。

- 对于在初始同步后的**增量同步**, 作业时间往往更快 (例如, 在10分钟内), 因为预配服务在初始同步后存储表示这两个系统状态的水印, 从而提高后续同步的性能。 作业时间取决于在该预配周期中检测到的更改的数量。 如果用户或组成员身份更改少于 5000, 则作业可以在单个增量预配周期内完成。 

下表总结了常见的预配方案的同步时间。 在这些方案中，源系统是 Azure AD，目标系统是 SaaS 应用程序。 同步时间是从 SaaS 应用程序 ServiceNow、工作区、Salesforce 和 G Suite 的同步作业的统计分析派生而来的。


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


对于“仅同步已分配的用户和组”配置，可以使用以下公式来确定大概的最小和最大预计**初始同步**时间：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影响**初始同步**的完成时间的因素汇总：

- 预配范围内用户和组的总数。

- 源系统 (Azure AD) 中存在的用户、组和组成员的总数。

- 用于预配的范围内的用户是否匹配目标应用程序中的现有用户, 或是否需要首次创建。 首次为其创建所有用户的同步作业需要大约*两次*, 只要同步作业的所有用户与现有用户匹配。

- [审核日志](check-status-user-account-provisioning.md)中的错误数。 如果有许多错误，并且预配服务已进入“隔离”状态，则性能较低。    

- 目标系统实现的请求速率限制。 某些目标系统实现请求速率限制和限制, 这可能会影响大型同步操作期间的性能。 在这些情况下，太快地接收太多请求的应用可能会拖慢其响应速率或关闭连接。 为提高性能，连接器需要进行调整，以不高于应用可以应对的处理速率的速率来向应用发送请求。 Microsoft 构建的预配连接器进行此调整。 

- 已分配的组的数量和大小。 同步已分配的组比同步用户花费的时间要长。 已分配的组的数量和大小都会影响性能。 如果应用程序[为组对象同步启用了映射](customize-application-attributes.md#editing-group-attribute-mappings)，则除了用户之外，还会同步组名称和成员身份等组属性。 这些额外的同步比仅同步用户对象需要花费更长的时间。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 自动化 SaaS 应用程序用户预配和取消预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
