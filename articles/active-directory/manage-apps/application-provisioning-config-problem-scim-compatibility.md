---
title: SCIM 2.0 协议符合性的已知问题-Azure AD
description: SCIM 2.0을 지원하는 비갤러리 애플리케이션을 Azure AD에 추가할 때 발생하는 일반적인 프로토콜 호환성 문제를 해결하는 방법
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
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da171db6535100342342571a5c1f6468abd0fc
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712344"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 준수와 관련하여 알려진 문제 및 해결 방법

Azure AD(Active Directory)는 [SCIM(System for Cross-Domain Identity Management) 2.0 프로토콜 사양](https://tools.ietf.org/html/draft-ietf-scim-api-19)에 정의된 인터페이스를 가진 웹 서비스가 향하는 애플리케이션 또는 시스템에 사용자 및 그룹을 자동으로 프로비전할 수 있습니다. 

Azure AD의 SCIM 2.0 프로토콜 지원은 [Using System for Cross-Domain Identity Management (SCIM) to automatically provision users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md)(SCIM(System for Cross-Domain Identity Management)을 사용하여 Azure Active Directory에서 애플리케이션으로 사용자 및 그룹 자동 프로비전)에서 설명합니다. 여기서는 SCIM 2.0을 지원하는 애플리케이션으로 Azure AD에서 사용자 및 그룹을 자동 프로비전하기 위해 구현하는 프로토콜의 특정 부분을 나열합니다.

이 문서에서는 Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 준수와 관련한 현재와 과거의 문제 및 이러한 문제를 해결하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure AD 사용자 프로비저닝 서비스 SCIM 클라이언트의 최신 업데이트는 2018년 12월 18일에 작성되었습니다. 이 업데이트에서는 아래 표에 나열된 알려진 호환성 문제가 해결되었습니다. 이 업데이트에 대한 자세한 내용은 아래의 질문과 대답을 참조하세요.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 준수 문제 및 상태

| **SCIM 2.0 준수 문제** |  **수정?** | **수정 날짜**  |  
|---|---|---|
| Azure AD가 애플리케이션의 SCIM 엔드포인트 URL의 루트에 위치하려면 “/scim”이 필요함  | 예  |  2018년 12월 18일 | 
| 확장 특성은 특정 이름 앞에 콜론 “:” 대신 점 “.” 표기법을 사용함 |  예  | 2018년 12월 18일  | 
|  다중 값 특성의 패치 요청에 있는 경로 필터 구문이 잘못됨 | 예  |  2018년 12월 18일  | 
|  그룹 생성 요청에 있는 스키마 URI가 잘못됨 | 예  |  2018년 12월 18일  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>설명한 서비스 수정이 내 기존 SCIM 앱에 자동으로 적용되었나요?

아닙니다. 이전 동작에서 작동하도록 코딩된 SCIM 앱에 호환성이 손상되는 변경이 발생한 경우 변경 사항이 기존 앱에 자동으로 적용되지 않았습니다.

Azure Portal에서 구성된 모든 신규 [비갤러리 SCIM 앱](configure-single-sign-on-non-gallery-applications.md)에 변경 사항이 수정 날짜 이후에 적용됩니다.

최신 수정을 포함하도록 기존 사용자 프로비저닝 작업을 마이그레이션하는 방법에 대한 정보는 다음 섹션을 참조하세요.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>기존의 SCIM 기반 사용자 프로비저닝 작업을 마이그레이션하여 최신 서비스 수정을 포함할 수 있나요?

예. Single Sign-On에 이 애플리케이션 인스턴스를 이미 사용 중인 경우 기존 프로비저닝 작업을 마이그레이션하여 최신 수정을 포함하도록 해야 하면 아래 절차를 따릅니다. 이 절차에서는 Microsoft Graph API 및 Microsoft Graph API 탐색기를 사용하여 기존의 SCIM 앱에서 이전 프로비저닝 작업을 제거하고 새 동작을 나타내는 새 프로비저닝 작업을 작성하는 방법을 설명합니다.

> [!NOTE]
> 애플리케이션이 여전히 개발 중이고 Single Sign-On 또는 사용자 프로비저닝에 아직 배포되지 않은 경우, 가장 쉬운 해결 방법은 Azure Portal의 **Azure Active Directory > 엔터프라이즈 애플리케이션** 섹션에서 애플리케이션 항목을 삭제하고 **애플리케이션 만들기 > 비갤러리** 옵션을 사용하여 애플리케이션의 새 항목을 추가하면 됩니다. 이는 아래 절차 실행의 대안입니다.
 
1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
2. Azure Portal의 **Azure Active Directory > 엔터프라이즈 애플리케이션** 섹션에서 기존의 SCIM 애플리케이션을 찾아 선택합니다.
3. 기존의 SCIM 앱의 **속성** 섹션에서 **개체 ID**를 복사합니다.
4. 새 웹 브라우저 창에서 https://developer.microsoft.com/graph/graph-explorer 로 이동하여 앱이 추가된 Azure AD 테넌트의 관리자로 로그인합니다.
5. Graph 탐색기에서 아래 명령을 실행하여 프로비저닝 작업의 ID를 찾습니다. “[object-id]”를 세 번째 단계에서 복사한 서비스 주체 ID(개체 ID)로 바꿉니다.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![获取作业](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "获取作业") 


6. 결과에서 “customappsso” 또는 “scim”으로 시작하는 전체 “ID” 문자열을 복사합니다.
7. 백업을 수행할 수 있도록, 아래 명령을 실행하여 특성 매핑 구성을 검색합니다. 이전과 동일한 [object-id]를 사용하고, [job-id]를 마지막 단계에서 복사한 프로비저닝 작업 ID로 바꿉니다.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![获取架构](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "获取架构") 

8. 마지막 단계의 JSON 출력을 복사하여 텍스트 파일에 저장합니다. 여기에는 이전 앱에 추가한 사용자 지정 특성 매핑이 포함되어 있으며 수천 개 정도의 JSON 줄이 있습니다.
9. 아래 명령을 실행하여 프로비저닝 작업을 삭제합니다.
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 아래 명령을 실행하여 최신 서비스 수정 사항이 있는 새 프로비저닝 작업을 만듭니다.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 마지막 단계의 결과에서 “scim”으로 시작하는 전체 “ID” 문자열을 복사합니다. 선택적으로, 아래 명령을 실행하여 이전 특성 매핑을 다시 적용하고 [new-job-id]를 방금 복사한 새 작업 ID로 바꾸고 7단계의 JSON 출력을 요청 본문으로 입력합니다.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 첫 번째 웹 브라우저 창으로 돌아가서 애플리케이션에 해당하는 **프로비저닝** 탭을 선택합니다.
13. 구성을 확인한 후 프로비저닝 작업을 시작합니다. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>이전 사용자 프로비저닝 동작이 있는 새 비갤러리 앱을 추가할 수 있나요?

예. 수정 이전에 존재했던 이전 동작에 애플리케이션을 코딩한 경우 새 인스턴스를 배포해야 하면 아래 절차를 따릅니다. 이 절차에서는 Microsoft Graph API 및 Microsoft Graph API 탐색기를 사용하여 이전 동작을 표시하는 SCIM 프로비저닝 작업을 작성하는 방법에 대해 설명합니다.
 
1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
2. Azure Portal의 **Azure Active Directory > 엔터프라이즈 애플리케이션 > 애플리케이션 만들기** 섹션에서 새 **비갤러리** 애플리케이션을 만듭니다.
3. 새 사용자 지정 앱의 **속성** 섹션에서 **개체 ID**를 복사합니다.
4. 새 웹 브라우저 창에서 https://developer.microsoft.com/graph/graph-explorer 로 이동하여 앱이 추가된 Azure AD 테넌트의 관리자로 로그인합니다.
5. Graph 탐색기에서 아래 명령을 실행하여 앱의 프로비저닝 구성을 초기화합니다.
   “[object-id]”를 세 번째 단계에서 복사한 서비스 주체 ID(개체 ID)로 바꿉니다.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. 첫 번째 웹 브라우저 창으로 돌아가서 애플리케이션에 해당하는 **프로비저닝** 탭을 선택합니다.
7. 일반적으로 하는 방법대로 사용자 프로비저닝 구성을 완료합니다.


## <a name="next-steps"></a>다음 단계
[SaaS 애플리케이션에 대한 사용자 프로비전 및 프로비전 해제 구성에 대해 자세히 알아보기](user-provisioning.md)

