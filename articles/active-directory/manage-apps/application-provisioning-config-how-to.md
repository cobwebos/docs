---
title: 如何配置 Azure AD 库应用的用户预配
description: Azure AD 애플리케이션 갤러리에 이미 나열된 애플리케이션에 대해 다양한 사용자 계정 프로비전 및 프로비전 해제를 빠르게 구성하는 방법
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 87818066a656aa4bcd6f45f1835ca1512a674a9e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712307"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전을 구성하는 방법

*사용자 계정 프로비전*은 애플리케이션의 로컬 사용자 프로필 저장소에 사용자 계정 레코드를 생성, 업데이트 및/또는 비활성화하는 작업입니다. 大多数云和 SaaS 应用程序将用户角色和权限存储在用户自己的本地用户配置文件存储区中，并且用户本地存储中存在此类用户记录是单一登录和访问工作*所必需*的。

Azure Portal에서 엔터프라이즈 앱에 대한 왼쪽 탐색 창의 **프로비전** 탭에는 해당 앱에 대해 지원되는 프로비전 모드가 표시됩니다. 다음 두 값 중 하나일 수 있습니다.

## <a name="configuring-an-application-for-manual-provisioning"></a>수동 프로비전에 대한 애플리케이션 구성

*手动*设置是指必须使用应用提供的方法手动创建用户帐户。 해당 앱의 관리 포털에 로그인하고 웹 기반 사용자 인터페이스를 사용하여 사용자를 추가한다는 의미일 수 있습니다. 또는 해당 애플리케이션에서 제공하는 메커니즘을 사용하여 사용자 계정 세부 정보를 스프레드시트에 업로드한다는 의미일 수 있습니다. 请查阅应用提供的文档，或联系应用开发人员来确定可用的机制。

如果 "*手动*" 是针对给定应用程序显示的唯一模式，则表示该应用尚未自动 Azure AD 预配连接器。 或者，这意味着该应用程序不支持 Microsoft 的用户管理 API 的先决条件，该 API 用于构建自动预配连接器。

해당 앱에 대해 자동 프로비전 지원을 요청하려면 [Azure Active Directory 애플리케이션 요청](https://aka.ms/aadapprequest)에서 요청 양식을 작성하세요.

## <a name="configuring-an-application-for-automatic-provisioning"></a>자동 프로비전에 대한 애플리케이션 구성

*자동*이란 Azure AD 프로비전 커넥터가 이 애플리케이션에 대해 개발되었음을 의미합니다. Azure AD 프로비전 서비스 및 작동 방식에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비전 및 프로비전 해제](user-provisioning.md)를 참조하세요.

특정 사용자 및 그룹을 애플리케이션에 프로비저닝하는 방법에 대한 자세한 내용은 [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](configure-automatic-user-provisioning-portal.md)를 참조하세요.

启用和配置自动预配所需的实际步骤因应用程序而异。

> [!NOTE]
> 애플리케이션의 프로비전 설정에 관한 설정 자습서를 찾아 해당 단계에 따라 앱 및 Azure AD를 구성하여 프로비전 연결을 생성해야 합니다.

앱 자습서는 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다.

프로비전을 설정할 때 고려해야 할 중요한 사항은 Azure AD에서 애플리케이션으로 이동하는 사용자(또는 그룹)를 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것입니다. 这包括设置用于唯一标识和匹配两个系统之间的用户/组的 "匹配属性"。 有关属性映射的详细信息，请参阅*后续步骤*中的链接。

## <a name="next-steps"></a>다음 단계
[Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비전 특성 매핑 사용자 지정](customize-application-attributes.md)

