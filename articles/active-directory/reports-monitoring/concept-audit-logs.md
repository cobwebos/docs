---
title: Azure Active Directory 포털의 감사 작업 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 감사 작업 보고서 소개
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b49949c1765c3cb1598d728e21479c65037930
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714488"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 감사 작업 보고서 

Azure AD(Azure Active Directory) 보고서를 통해 사용자 환경의 작동 방법을 결정하는 데 필요한 모든 정보를 얻을 수 있습니다.

보고 아키텍처는 다음과 같은 구성 요소로 구성됩니다.

- **활동** 
    - **로그인** – [로그인 보고서](concept-sign-ins.md)는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.
    - **감사 로그** - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
- **보안** 
    - **위험한 로그인** - [위험한 로그인](concept-risky-sign-ins.md)은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 
    - **위험 플래그가 지정된 사용자** - [위험한 사용자](concept-user-at-risk.md)는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이 문서에서는 감사 보고서에 대한 개요를 제공합니다.
 
## <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?

* **安全管理员**、**安全读者**、**报表读者**或**全局管理员**角色中的用户

## <a name="audit-logs"></a>감사 로그

Azure AD 감사 로그는 규정 준수를 위한 시스템 활동의 기록을 제공합니다. 감사 보고서에 액세스하려면 **Azure Active Directory**의 **활동** 섹션에서 **감사 로그**를 선택합니다. 请注意，审核日志的滞后时间可能最长为一小时，因此，在完成任务后，在门户中显示审核活动数据可能需要较长时间。



감사 로그에는 다음 항목을 보여 주는 기본 목록 보기가 있습니다.

- 발생 날짜와 시간
- 记录发生事件的服务
- 活动的类别和名称（*内容*） 
- 活动的状态（成功或失败）
- 대상
- 작업의 초기자/행위자(누가)

![감사 로그](./media/concept-audit-logs/listview.png "감사 로그")

도구 모음에서 **열**을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![감사 로그](./media/concept-audit-logs/columns.png "감사 로그")

그러면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![감사 로그](./media/concept-audit-logs/columnselect.png "감사 로그")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![감사 로그](./media/concept-audit-logs/details.png "감사 로그")


## <a name="filtering-audit-logs"></a>감사 로그 필터링

다음 필드에서 감사 데이터를 필터링할 수 있습니다.

- 서비스
- 범주
- 작업
- 상태
- 대상 파악
- 초기자(작업자)
- 날짜 범위

![감사 로그](./media/concept-audit-logs/filter.png "감사 로그")

使用**服务**筛选器可以从以下服务的下拉列表中进行选择：

- 전체
- 액세스 검토
- 계정 프로비전 
- 应用程序 SSO
- 身份验证方法
- B2C
- 조건부 액세스
- 핵심 디렉터리
- 권한 관리
- ID 보호
- 사용자 초대
- PIM
- 셀프 서비스 그룹 관리
- 셀프 서비스 암호 관리
- 사용 조건

**类别**筛选器使您可以选择以下筛选器之一：

- 전체
- AdministrativeUnit
- ApplicationManagement
- 인증
- 권한 부여
- 지원 문의
- 디바이스
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- 기타
- 정책
- ResourceManagement
- RoleManagement
- UserManagement

**活动**筛选器基于所选的类别和活动资源类型。 보려는 특정 활동을 선택하거나 모두 선택할 수 있습니다. 

你可以使用图形 API 获取所有审核活动的列表： `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

利用**状态**筛选器，您可以基于审核操作的状态进行筛选。 状态可以为下列其中一项：

- 전체
- Success
- 실패

**目标**筛选器允许你按名称或用户主体名称（UPN）搜索特定目标。 目标名称和 UPN 区分大小写。 

使用 "**启动者**" 筛选器可以定义参与者的名称或通用主体名称（UPN）。 名称和 UPN 区分大小写。

使用 "**日期范围**" 筛选器可以定义返回的数据的时间范围。  
가능한 값은 다음과 같습니다.

- 1개월
- 7일
- 24시간
- 맞춤형 서비스

사용자 지정 시간 범위를 선택하면 시작 시간과 종료 시간을 구성할 수 있습니다.

通过选择 "**下载**" 按钮，还可以选择下载筛选的数据，最多250000条记录。 可以采用 CSV 或 JSON 格式下载日志。 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다.

![감사 로그](./media/concept-audit-logs/download.png "감사 로그")

## <a name="audit-logs-shortcuts"></a>감사 로그 바로 가기

**Azure Active Directory** 외에도 Azure Portal에서는 감사 데이터에 대한 다음 두 개의 추가 진입점을 제공합니다.

- 개요
- Enterprise 애플리케이션

### <a name="users-and-groups-audit-logs"></a>사용자 및 그룹 감사 로그

사용자 및 그룹 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

- 어떤 종류의 업데이트가 사용자에게 적용되나요?

- 얼마나 많은 사용자가 변경되었나요?

- 얼마나 많은 암호가 변경되었나요?

- 관리자가 디렉터리에서 무엇을 수행했나요?

- 추가된 그룹은 무엇인가요?

- 멤버 자격이 변경된 그룹이 있나요?

- 그룹의 소유자가 변경되었나요?

- 어떤 라이선스가 그룹 또는 사용자에 할당되었나요?

如果只想查看与用户相关的审核数据，则可以在 "**用户**" 选项卡的 "**活动**" 部分中的 "**审核日志**" 下找到筛选视图。此入口点已将**UserManagement**作为预先选择的类别。

![감사 로그](./media/concept-audit-logs/users.png "감사 로그")

如果只想查看与组相关的审核数据，则可以在 "**组**" 选项卡的 "**活动**" 部分中的 "**审核日志**" 下找到筛选视图。此入口点已将**GroupManagement**作为预先选择的类别。

![감사 로그](./media/concept-audit-logs/groups.png "감사 로그")

### <a name="enterprise-applications-audit-logs"></a>Enterprise 애플리케이션 감사 로그

애플리케이션 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

* 추가되거나 업데이트된 애플리케이션은 무엇인가요?
* 제거된 애플리케이션은 무엇인가요?
* 애플리케이션에 대한 서비스 주체가 변경되었나요?
* 애플리케이션의 이름이 변경되었나요?
* 누가 애플리케이션에 동의했나요?

애플리케이션과 관련된 감사 데이터를 검토하려면 **Enterprise 애플리케이션** 블레이드의 **활동** 섹션에 있는 **감사 로그**에서 필터링된 보기를 찾을 수 있습니다. 此入口点具有预先选择为**应用程序类型**的**企业应用程序**。

![감사 로그](./media/concept-audit-logs/enterpriseapplications.png "감사 로그")

## <a name="office-365-activity-logs"></a>Office 365 활동 로그

可以从[Microsoft 365 管理中心](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center)查看 Office 365 活动日志。 尽管 Office 365 活动和 Azure AD 活动日志共享大量的目录资源，但只有 Microsoft 365 管理中心提供 Office 365 活动日志的完整视图。 

你还可以使用[office 365 管理 api](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview)以编程方式访问 office 365 活动日志。

## <a name="next-steps"></a>다음 단계

- [Azure AD 감사 활동 참조](reference-audit-activities.md)
- [Azure AD 보고서 보존 참조](reference-reports-data-retention.md)
- [Azure AD 로그 대기 시간 참조](reference-reports-latencies.md)
