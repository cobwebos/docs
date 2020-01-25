---
title: 查明特定的用户何时可以访问应用
description: 매우 중요한 사용자가 Azure AD를 사용하여 사용자를 프로비저닝하도록 구성한 애플리케이션에 액세스할 수 있는 시기를 찾는 방법
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
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7296c63a467b2f53550b3e609cf1146244cf933
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712125"
---
# <a name="check-the-status-of-user-provisioning"></a>检查用户设置的状态

Azure AD 预配服务对源系统和目标系统运行初始设置周期，后跟定期增量循环。 配置应用的预配时，可以检查预配服务的当前状态，并查看用户何时可以访问应用。

## <a name="view-the-provisioning-progress-bar"></a>查看设置进度栏

 在应用的 "**设置**" 页上，你可以查看 Azure AD 预配服务的状态。 页面底部的 "**当前状态**" 部分显示预配周期是否已开始预配用户帐户。 您可以查看周期的进度，查看已设置的用户和组的数量，并查看已创建的角色数。

首次配置自动预配时，页面底部的 "**当前状态**" 部分会显示初始预配周期的状态。 此部分将在每次运行增量循环时更新。 将显示以下详细信息：
- 当前正在运行或最后完成的预配周期（初始或增量）的类型。
- 显示已完成的预配周期百分比的**进度栏**。 百分比反映预配的页的计数。 请注意，每个页面可能包含多个用户或组，因此该百分比并不与预配的用户、组或角色的数目直接相关。
- 可以使用 "**刷新**" 按钮来更新视图。
- 连接器数据存储中的**用户**和**组**的数目。 当将对象添加到预配的作用域时，计数将增加。 如果用户已软删除或硬删除，则计数不会关闭，因为这样不会从连接器数据存储中删除对象。 在 CD[重置](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)后，将 recaculated 第一次同步的计数 
- "**查看审核日志**" 链接，可打开 Azure AD 预配日志，以获取有关用户预配服务运行的所有操作的详细信息，包括每个用户的预配状态（请参阅下面的[使用预配日志](#use-provisioning-logs-to-check-a-users-provisioning-status)部分）。

设置周期完成后，"**统计信息截止日期**" 部分显示已设置为 "日期" 的用户和组的累计数量，以及最后一个周期的完成日期和持续时间。 **活动 ID**唯一标识最新的设置周期。 **作业 ID**是预配作业的唯一标识符，特定于租户中的应用。

可以在 Azure 门户的**Azure Active Directory &gt; 企业应用 &gt; \[** \] 的 "预配" 选项卡中查看设置进度。

![设置页进度栏](media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>使用设置日志检查用户的预配状态

若要查看所选用户的预配状态，请参阅 Azure AD 中的[预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 。 用户预配服务运行的所有操作都记录在 Azure AD 预配日志中。 这包括对源系统和目标系统执行的所有读取和写入操作，以及每个操作期间读取或写入的用户数据。

可以通过在 "**活动**" 部分中选择 " **Azure Active Directory** &gt;**企业应用**&gt;**预配日志（预览版）** " 来访问 Azure 门户中的设置日志。 你可以根据用户的名称或源系统或目标系统中的标识符来搜索设置数据。 有关详细信息，请参阅[预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 

预配日志记录预配服务执行的所有操作，包括：

* 프로비저닝 범위에 있는 할당된 사용자에 대해 Azure AD 쿼리
* 해당 사용자의 존재에 대해 대상 앱 쿼리
* 시스템 간의 사용자 개체 비교
* 비교를 기반으로 대상 시스템에서 사용자 계정 추가, 업데이트 또는 비활성화

有关如何读取 Azure 门户中的设置日志的详细信息，请参阅[预配报告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>사용자를 프로비전하는 데 걸리는 시간은 어느 정도인가요?
在应用程序中使用自动用户预配时，Azure AD 会按定期计划的时间间隔（通常每隔40分钟）在应用中自动预配和更新用户帐户（如[用户和组分配](assign-user-or-group-access-portal.md)）。

预配给定用户所用的时间主要取决于你的预配作业是正在运行初始循环还是增量循环。

- 对于**初始周期**，作业时间取决于许多因素，包括用于预配的作用域中的用户和组的数量，以及源系统中的用户和组的总数。 Azure AD와 앱 간의 첫 번째 동기화는 Azure AD 디렉터리의 크기와 프로비저닝 범위에 있는 사용자 수에 따라 20분에서 몇 시간이 걸릴 수 있습니다. 本部分稍后将概述影响初始周期性能的因素的综合列表。

- 对于初始周期后的**增量循环**，作业时间往往更快（例如，在10分钟内），因为预配服务在初始周期后存储表示这两个系统状态的水印，从而提高后续同步的性能。 作业时间取决于在该预配周期中检测到的更改的数量。 如果用户或组成员身份更改少于5000，则作业可以在单个增量预配周期内完成。 

다음 표에는 일반적인 프로비저닝 시나리오의 동기화 시간이 요약되어 있습니다. 이러한 시나리오에서 원본 시스템은 Azure AD이고, 대상 시스템은 SaaS 애플리케이션입니다. 同步时间是从 SaaS 应用程序 ServiceNow、工作区、Salesforce 和 G Suite 的同步作业的统计分析派生而来的。


| 범위 구성 | 범위 내 사용자, 그룹 및 멤버 | 初始周期时间 | 增量循环时间 |
| -------- | -------- | -------- | -------- |
| 할당된 사용자 및 그룹만 동기화 |  1,000 미만 |  30분 미만 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |  1,000 - 10,000 | 142 - 708분 | 30분 미만 |
| 할당된 사용자 및 그룹만 동기화 |   10,000 - 100,000 | 1,170 - 2,340분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 미만 | 30분 미만  | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  1,000 - 10,000 | 30 - 120분 | 30분 미만 |
| Azure AD의 모든 사용자 및 그룹 동기화 |  10,000 - 100,000  | 713 - 1,425분 | 30분 미만 |
| Azure AD의 모든 사용자 동기화|  1,000 미만  | 30분 미만 | 30분 미만 |
| Azure AD의 모든 사용자 동기화 | 1,000 - 10,000  | 43 - 86분 | 30분 미만 |


对于**仅限配置同步分配的用户和组**，可以使用以下公式来确定预计的最小和最大预计**初始周期**时间：

    Minimum minutes =  0.01 x [Number of assigned users, groups, and group members]
    Maximum minutes = 0.08 x [Number of assigned users, groups, and group members] 
    
影响完成**初始周期**所花费时间的因素的摘要：

- 프로비저닝 범위에 속하는 사용자 및 그룹의 총 수

- 원본 시스템(Azure AD)에 있는 사용자, 그룹 및 그룹 멤버의 총 수

- 用于预配的范围内的用户是否匹配目标应用程序中的现有用户，或是否需要首次创建。 首次为其创建所有用户的同步作业需要大约*两次*，只要同步作业的所有用户与现有用户匹配。

- [预配日志](check-status-user-account-provisioning.md)中的错误数。 많은 오류가 있고 프로비저닝 서비스가 격리 상태로 전환된 경우 성능이 저하됩니다. 

- 대상 시스템에서 구현된 요청 비율 한도 및 제한. 某些目标系统实现请求速率限制和限制，这可能会影响大型同步操作期间的性能。 이러한 조건에서 너무 많은 요청을 너무 빠르게 받는 앱은 응답 속도가 느려지거나 연결을 닫을 수도 있습니다. 성능을 개선하려면 커넥터가 앱이 처리할 수 있는 속도보다 빠르게 앱 요청이 전송되지 않도록 조정해야 합니다. Microsoft에서 빌드한 프로비저닝 커넥터는 이러한 조정 작업을 수행합니다. 

- 할당된 그룹 수 및 크기. 할당된 그룹 동기화가 사용자 동기화보다 시간이 오래 걸립니다. 할당된 그룹 수와 크기는 둘 다 성능에 영향을 줍니다. 애플리케이션에 [그룹 개체 동기화가 사용되는 매핑](customize-application-attributes.md#editing-group-attribute-mappings)이 있는 경우 그룹 이름 및 멤버 자격과 같은 그룹 속성이 사용자와 함께 동기화됩니다. 이러한 추가 동기화 때문에 사용자 객체만 동기화하는 것보다 시간이 오래 걸립니다.

- 如果性能成为问题，并且你正在尝试预配租户中的大部分用户和组，请使用范围筛选器。 범위 지정 필터를 사용하면 특정 특성 값에 따라 사용자를 필터링하여 프로비저닝 서비스가 Azure AD에서 추출하는 데이터를 미세 조정할 수 있습니다. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비전](define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비전 및 프로비전 해제](user-provisioning.md)
