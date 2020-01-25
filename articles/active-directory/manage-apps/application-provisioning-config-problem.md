---
title: 配置 Azure AD 库应用的用户预配时出现问题
description: Azure AD 애플리케이션 갤러리에 이미 나열된 애플리케이션에 대한 사용자 프로비전을 구성할 때 발생하는 일반적인 문제를 해결하는 방법
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4da7c874cc5f883d63f8613242c7a7e8b1e83cbd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712270"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제

앱에 대해 [자동 사용자 프로비전](user-provisioning.md)을 구성하려면(지원되는 경우) 자동 프로비전에 대해 애플리케이션을 준비하는 특정 지침을 따라야 합니다. 그런 다음, Azure Portal에서 프로비전 서비스를 구성하여 사용자 계정을 애플리케이션에 동기화할 수 있습니다.

항상 애플리케이션의 프로비전 설정에 대한 설정 자습서를 찾는 것부터 시작해야 합니다. 그런 다음 해당 단계에 따라 앱과 Azure AD를 구성하여 프로비전 연결을 만듭니다. 앱 자습서 목록은 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다.

## <a name="how-to-see-if-provisioning-is-working"></a>프로비전이 작동하는지 확인하는 방법 

서비스가 구성되면 서비스 작업에 대한 대부분의 정보는 다음 두 곳에서 확인할 수 있습니다.

-   **预配日志（预览版）** –[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)记录预配服务执行的所有操作，包括查询在预配范围内分配的用户 Azure AD。 시스템 간 사용자 개체를 비교하여 해당 사용자의 존재에 대해 대상 앱을 쿼리합니다. 그런 다음 비교를 기반으로 대상 시스템에서 사용자 계정을 추가, 업데이트 또는 비활성화합니다. 可以通过在 "**活动**" 部分中选择 " **Azure Active Directory** &gt;**企业应用**&gt;**预配日志（预览版）** " 来访问 Azure 门户中的设置日志。

-   **当前状态–** 可以在 "服务设置" 下屏幕底部的 " **Azure Active Directory &gt; 企业应用程序 &gt; \[应用程序名称\] &gt;预配**" 部分中查看针对给定应用程序的最后一次设置运行的摘要。 "当前状态" 部分显示预配周期是否已开始预配用户帐户。 您可以查看周期的进度，查看已设置的用户和组的数量，并查看已创建的角色数。 如果有任何错误，则可在[预配日志（预览版）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中找到详细信息。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>고려해야 할 프로비전 관련 일반적인 문제 영역

다음은 시작 위치를 파악한 경우 검색할 수 있는 일반적인 문제 영역 목록입니다.

* [프로비전 서비스가 시작하지 않는 것 같음](#provisioning-service-does-not-appear-to-start)
* 앱 자격 증명이 작동하지 않아 구성을 저장할 수 없음
* [预配日志指出用户已被 "跳过" 且未预配，即使它们已被分配](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>프로비전 서비스가 시작하지 않는 것 같음

如果在 Azure Active Directory 中将设置**状态**设置为 **"打开**" **&gt; 企业应用 &gt; \[** "\] &gt;预配" 部分中的 "应用程序名称"。 그러나 이후 다시 로드 후 기타 상태 세부 정보가 표시되지 않습니다. 可能是服务正在运行，但尚未完成初始循环。 检查上述**预配日志**，确定服务正在执行哪些操作，以及是否有任何错误。

>[!NOTE]
>初始周期可能需要20分钟到几小时的时间，具体取决于 Azure AD 目录的大小和预配范围内的用户数量。 初始循环后的后续同步速度更快，因为预配服务在初始周期后存储表示这两个系统状态的水印，从而提高后续同步的性能。
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>앱 자격 증명이 작동하지 않아 구성을 저장할 수 없음

프로비전을 작동하도록 하려면 Azure AD에서 해당 앱에서 제공한 사용자 관리 API에 연결할 수 있도록 유효한 자격 증명이 필요합니다. 이러한 자격 증명이 작동하지 않거나 자격 증명이 무엇인지 모르는 경우 이전에 설명한 이 앱 설정에 대한 자습서를 검토하세요.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>预配日志指出用户被跳过且未预配，即使它们已被分配

当用户在预配日志中显示为 "已跳过" 时，请务必阅读日志消息中的扩展详细信息以确定原因。 다음은 일반적인 원인과 해결 방법입니다.

- 已**配置范围筛选器，该筛选器** **基于属性值筛选用户**。 有关详细信息，请参阅[通过范围筛选器进行基于属性的应用程序设置](define-conditional-rules-for-provisioning-user-accounts.md)。

- **사용자가 “실질적으로 권한을 부여받지 않았습니다.”** 이 특정 오류 메시지가 표시되는 경우 Azure AD에 저장된 사용자 할당 레코드에 문제가 있기 때문입니다. 이 문제를 해결하려면 앱에서 사용자(또는 그룹)의 할당을 취소하고 다시 할당하세요. 有关详细信息，请参阅向[企业应用分配用户或组](assign-user-or-group-access-portal.md)。

- **사용자에게 필요한 특성이 누락되었거나 입력되지 않았습니다.** 프로비전을 설정할 때 고려해야 할 중요한 사항은 Azure AD에서 애플리케이션으로 이동하는 사용자(또는 그룹)를 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것입니다. 여기에는 두 시스템 간 사용자/그룹을 고유하게 식별하고 일치하는 데 사용되는 “일치하는 속성” 설정이 포함됩니다. 有关此重要过程的详细信息，请参阅[自定义用户预配属性映射](customize-application-attributes.md)。

  * **그룹에 대한 특성 매핑:** 일부 애플리케이션에 대해 지원되는 경우, 구성원 외에 그룹 이름 및 그룹 세부 정보 프로비전. 可以启用或禁用 "**预配**" 选项卡中显示的组对象**映射**来启用或禁用此功能。如果启用了预配组，请务必查看属性映射以确保 "匹配 ID" 使用合适的字段。 이는 표시 이름이나 메일 별칭일 수 있습니다. Azure AD에 일치하는 속성이 비어 있거나 그룹에 대해 입력되어 있지 않은 경우 그룹 및 구성원이 프로비전되지 않습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](user-provisioning.md)
