---
title: 비갤러리 애플리케이션에 대해 비갤러리 Single Sign-On 구성 문제 | Microsoft Docs
description: Azure AD 애플리케이션 갤러리에 나열되지 않은 사용자 지정 SAML 애플리케이션에 페더레이션된 Single Sign-On을 구성할 때 발생하는 일반적인 문제 몇 가지를 해결
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
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711890"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>비갤러리 애플리케이션에 대해 비갤러리 Single Sign-On 구성 문제

애플리케이션을 구성할 때 문제가 발생 할 경우. [Azure Active Directory 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](configure-federated-single-sign-on-non-gallery-applications.md) 문서에 있는 단계를 모두 수행했는지 확인합니다.

## <a name="cant-add-another-instance-of-the-application"></a>애플리케이션의 다른 인스턴스를 추가할 수 없음

애플리케이션의 두 번째 인스턴스를 추가하려면 다음을 수행할 수 있어야 합니다.

-   두 번째 인스턴스에 대한 고유 식별자를 구성합니다. 첫 번째 인스턴스에 대해 사용한 것과 동일한 식별자를 구성할 수 없습니다.

-   첫 번째 인스턴스에 대해 사용한 것과 다른 인증서를 구성합니다.

애플리케이션이 이전 항목을 지원하지 않으면 두 번째 인스턴스를 구성할 수 없습니다.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityID(사용자 식별자) 형식을 설정하는 위치

사용자 인증 후에 Azure AD에서 응답을 통해 애플리케이션으로 보내는 EntityID(사용자 ID) 형식은 선택할 수 없습니다.

Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 애플리케이션이 요청한 형식을 기반으로 NameID 특성(사용자 ID)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md#authnrequest) 문서에서 확인할 수 있습니다.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Azure AD에서 애플리케이션 메타데이터 또는 인증서를 가져오는 위치

Azure AD에서 애플리케이션 메타데이터 또는 인증서를 다운로드하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 클릭합니다.

5. **모든 애플리케이션**을 클릭하여 모든 애플리케이션의 목록을 봅니다.

   * 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션**으로 설정합니다.

6. Single Sign-On을 구성한 애플리케이션을 선택합니다.

7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8. **SAML 서명 인증서** 섹션으로 이동한 다음 **다운로드** 열 값을 클릭합니다. Single Sign-On을 구성해야 할 애플리케이션에 따라 메타데이터 XML이나 인증서를 다운로드하는 옵션이 표시됩니다.

Azure AD에서는 메타데이터를 가져오는 URL을 제공하지 않습니다. 메타데이터는 XML 파일로만 검색할 수 있습니다.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>애플리케이션에 전송된 SAML 클레임을 사용자 지정하는 방법을 알 수 없음

애플리케이션에 전송된 SAML 특성 클레임을 사용자 지정하는 방법을 알아보려면 [Azure Active Directory의 클레임 매핑](../develop/active-directory-claims-mapping.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 애플리케이션 관리](what-is-application-management.md)
