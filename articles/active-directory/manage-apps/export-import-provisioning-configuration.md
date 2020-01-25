---
title: 使用图形 API 导出或导入预配配置 |Microsoft Docs
description: 了解如何使用图形 API 导出和导入预配配置。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609031bfad23a14a954a09a447e363e89a9d29d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711746"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>使用图形 API 导出或导入设置配置

你可以使用 Microsoft Graph API 和图形资源管理器将用户预配属性映射和架构导出到 JSON 文件，然后将其重新导入 Azure AD。 你还可以使用此处捕获的步骤来创建预配配置的备份。 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>步骤1：检索预配应用服务主体 ID （对象 ID）

1. 启动[Azure 门户](https://portal.azure.com)，并导航到预配应用程序的 "属性" 部分。 例如，如果要将*Workday 导出到 AD 用户预配应用程序*映射，请导航到该应用的 "属性" 部分。 
1. 프로비전 앱의 속성 섹션에서 ‘개체 ID’ 필드와 연결된 GUID 값을 복사합니다. 이 값은 앱의 **ServicePrincipalId**라고도 하고 Graph Explorer 작업에서 사용됩니다.

   ![Workday 앱 서비스 주체 ID](media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>步骤2：登录 Microsoft Graph 资源管理器

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 시작
1. “Microsoft에 로그인” 단추를 클릭하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.

    ![Graph 로그인](media/export-import-provisioning-mappings/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>步骤3：检索预配应用的设置作业 ID

Microsoft Graph Explorer에서 [servicePrincipalId]를 [1단계](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)에서 추출된 **ServicePrincipalId**로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

다음과 같은 응답이 제공됩니다. 응답에 있는 “id 특성”을 복사합니다. 이 값은 **ProvisioningJobId**이고 기본 스키마 메타데이터를 검색하는 데 사용됩니다.

   [![设置作业 ID](media/export-import-provisioning-mappings/wd_export_03.png)](media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>步骤4：下载预配架构

Microsoft Graph Explorer에서 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

응답에서 JSON 개체를 복사하고 파일에 저장하여 스키마 백업을 만듭니다.

## <a name="step-5-import-the-provisioning-schema"></a>步骤5：导入预配架构

> [!CAUTION]
> Azure Portal을 사용하여 변경할 수 없는 구성의 스키마를 수정해야 하거나 이전에 백업된 파일의 구성을 작동하는 유효한 스키마로 복원해야 하는 경우에만 이 단계를 수행합니다.

Microsoft Graph Explorer에서 다음 PUT 쿼리를 구성하고 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꿉니다.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

“요청 본문” 탭에서 JSON 스키마 파일의 콘텐츠를 복사합니다.

   [![요청 본문](media/export-import-provisioning-mappings/wd_export_04.png)](media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

“요청 헤더” 탭에서 “application/json” 값과 함께 Content-Type 헤더 특성을 추가합니다.

   [![요청 헤더](media/export-import-provisioning-mappings/wd_export_05.png)](media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

“쿼리 실행” 단추를 클릭하여 새 스키마를 가져옵니다.
