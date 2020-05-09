---
title: 查明特定的用户何时可以访问应用
description: 如何找出非常重要的用户何时可以访问已使用 Azure AD 配置用户预配的应用程序
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 9d976785340e1a032aa4484dd2e8fd6cb2915129
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593907"
---
# <a name="check-the-status-of-user-provisioning"></a>检查用户设置的状态

Azure AD 预配服务对源系统和目标系统运行初始设置周期，后跟定期增量循环。 配置应用的预配时，可以检查预配服务的当前状态，并查看用户何时可以访问应用。

## <a name="view-the-provisioning-progress-bar"></a>查看设置进度栏

 在应用的 "**设置**" 页上，你可以查看 Azure AD 预配服务的状态。 页面底部的 "**当前状态**" 部分显示预配周期是否已开始预配用户帐户。 您可以查看周期的进度，查看已设置的用户和组的数量，并查看已创建的角色数。

首次配置自动预配时，页面底部的 "**当前状态**" 部分会显示初始预配周期的状态。 此部分将在每次运行增量循环时更新。 将显示以下详细信息：
- 当前正在运行或最后完成的预配周期（初始或增量）的类型。
- 显示已完成的预配周期百分比的**进度栏**。 百分比反映预配的页的计数。 请注意，每个页面可能包含多个用户或组，因此该百分比并不与预配的用户、组或角色的数目直接相关。
- 可以使用 "**刷新**" 按钮来更新视图。
- 连接器数据存储中的**用户**和**组**的数目。 当将对象添加到预配的作用域时，计数将增加。 如果用户已软删除或硬删除，则计数不会关闭，因为这样不会从连接器数据存储中删除对象。 在 CD[重置](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)后，将重新计算第一次同步 
- "**查看审核日志**" 链接，可打开 Azure AD 预配日志，以获取有关用户预配服务运行的所有操作的详细信息，包括每个用户的预配状态（请参阅下面的[使用预配日志](#use-provisioning-logs-to-check-a-users-provisioning-status)部分）。

设置周期完成后，"**统计信息截止日期**" 部分显示已设置为 "日期" 的用户和组的累计数量，以及最后一个周期的完成日期和持续时间。 **活动 ID**唯一标识最新的设置周期。 **作业 ID**是预配作业的唯一标识符，特定于租户中的应用。

可以在 Azure 门户的 " **Azure Active Directory &gt;企业&gt; \[应用程序\] &gt; " "预配**" 选项卡中查看设置进度。

![设置页进度栏](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>使用设置日志检查用户的预配状态

若要查看所选用户的预配状态，请参阅 Azure AD 中的[预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 。 用户预配服务运行的所有操作都记录在 Azure AD 预配日志中。 这包括对源系统和目标系统执行的所有读取和写入操作，以及每个操作期间读取或写入的用户数据。

可以通过在 "**活动**" 部分中选择 " **Azure Active Directory** &gt; **企业应用** &gt; **预配日志（预览版）** " 来访问 Azure 门户中的设置日志。 你可以根据用户的名称或源系统或目标系统中的标识符来搜索设置数据。 有关详细信息，请参阅[预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 

预配日志记录预配服务执行的所有操作，包括：

* 查询 Azure AD 以找到预配作用域中分配的用户
* 查询目标应用以验证是否存在这些用户
* 比较系统之间的用户对象
* 根据比较结果在目标系统中添加、更新或禁用用户帐户

有关如何读取 Azure 门户中的设置日志的详细信息，请参阅[预配报告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>预配用户需要多长时间？
在应用程序中使用自动用户预配时，Azure AD 会按定期计划的时间间隔（通常每隔40分钟）在应用中自动预配和更新用户帐户（如[用户和组分配](../manage-apps/assign-user-or-group-access-portal.md)）。

预配给定用户所用的时间主要取决于你的预配作业是正在运行初始循环还是增量循环。

- 对于**初始周期**，作业时间取决于许多因素，包括用于预配的作用域中的用户和组的数量，以及源系统中的用户和组的总数。 Azure AD 和应用之间的首次同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 本部分稍后将概述影响初始周期性能的因素的综合列表。

- 对于初始周期后的**增量循环**，作业时间往往更快（例如，在10分钟内），因为预配服务在初始周期后存储表示这两个系统状态的水印，从而提高后续同步的性能。 作业时间取决于在该预配周期中检测到的更改的数量。 如果用户或组成员身份更改少于5000，则作业可以在单个增量预配周期内完成。 

下表总结了常见的预配方案的同步时间。 在这些方案中，源系统是 Azure AD，目标系统是 SaaS 应用程序。 同步时间是从 SaaS 应用程序 ServiceNow、工作区、Salesforce 和 G Suite 的同步作业的统计分析派生而来的。


| 作用域配置 | 作用域中的用户、组和成员 | 初始周期时间 | 增量循环时间 |
| -------- | -------- | -------- | -------- |
| 仅同步已分配的用户和组 |  < 1,000 |  < 30 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |  1,000 - 10,000 | 142 - 708 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |   10,000 - 100,000 | 1,170 - 2,340 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  < 1,000 | < 30 分钟  | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  1,000 - 10,000 | < 30 - 120 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  10,000 - 100,000  | 713 - 1,425 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户|  < 1,000  | < 30 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户 | 1,000 - 10,000  | 43 - 86 分钟 | < 30 分钟 |


对于**仅限配置同步分配的用户和组**，可以使用以下公式来确定预计的最小和最大预计**初始周期**时间：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影响完成**初始周期**所花费时间的因素的摘要：

- 预配范围内用户和组的总数。

- 源系统 (Azure AD) 中存在的用户、组和组成员的总数。

- 用于预配的范围内的用户是否匹配目标应用程序中的现有用户，或是否需要首次创建。 首次为其创建所有用户的同步作业需要大约*两次*，只要同步作业的所有用户与现有用户匹配。

- [预配日志](check-status-user-account-provisioning.md)中的错误数。 如果有许多错误，并且预配服务已进入“隔离”状态，则性能较低。 

- 目标系统实现的请求速率限制。 某些目标系统实现请求速率限制和限制，这可能会影响大型同步操作期间的性能。 在这些情况下，太快地接收太多请求的应用可能会拖慢其响应速率或关闭连接。 为提高性能，连接器需要进行调整，以不高于应用可以应对的处理速率的速率来向应用发送请求。 Microsoft 构建的预配连接器进行此调整。 

- 已分配的组的数量和大小。 同步已分配的组比同步用户花费的时间要长。 已分配的组的数量和大小都会影响性能。 如果应用程序[为组对象同步启用了映射](customize-application-attributes.md#editing-group-attribute-mappings)，则除了用户之外，还会同步组名称和成员身份等组属性。 这些额外的同步比仅同步用户对象需要花费更长的时间。

- 如果性能成为问题，并且你正在尝试预配租户中的大部分用户和组，请使用范围筛选器。 使用范围筛选器，可优化预配服务通过筛选用户（基于特定属性值）从 Azure AD 提取的数据。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>后续步骤
[通过 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](user-provisioning.md)
