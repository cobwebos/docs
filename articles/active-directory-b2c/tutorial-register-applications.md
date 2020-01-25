---
title: 教程：注册应用程序
titleSuffix: Azure AD B2C
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 웹 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 214797f187c1812932c00bdf2a39c5ed79e15d4b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712663"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中注册应用程序

必须先在你管理的租户中注册[应用程序](active-directory-b2c-apps.md)，然后才能与 Azure Active Directory B2C （Azure AD B2C）交互。 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션을 등록하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

Azure AD B2C 테넌트에 애플리케이션을 등록하기 위해 현재 **애플리케이션** 환경 또는 새로운 통합 **앱 등록(미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 在门户工具栏中选择 "**目录 + 订阅**" 图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择 " **Azure AD B2C**"。
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 例如，你可以将其设置为在 `https://localhost:44316`上进行本地侦听。 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.

    对于像本教程这样的测试目的，您可以将其设置为 `https://jwt.ms` 显示令牌的内容进行检查。 对于本教程，请将**回复 URL**设置为 `https://jwt.ms`。

    以下限制适用于回复 Url：

    * 回复 URL 必须以方案 `https`开头。
    * 回复 URL 区分大小写。 它的大小写必须与正在运行的应用程序的 URL 路径匹配。 例如，如果你的应用程序将作为其路径 `.../abc/response-oidc`的一部分包含，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 web 浏览器将路径视为区分大小写，因此在重定向到不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

1. 选择 "**创建**" 以完成应用程序注册。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 在门户工具栏中选择 "**目录 + 订阅**" 图标，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择 " **Azure AD B2C**"。
1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. 选择**任何组织目录或任何标识提供者中的帐户**。
1. 在 "**重定向 URI**" 下，选择 " **Web**"，然后在 "URL" 文本框中输入 `https://jwt.ms`。

    "重定向 URI" 是授权服务器向其发送用户的终结点（在此示例中为 Azure AD B2C，在这种情况下，在完成其与用户的交互之后，以及成功授权后发送访问令牌或授权代码的终结点。 在生产应用程序中，它通常是在其中运行应用程序的可公开访问的终结点，如 `https://contoso.com/auth-response`。 对于像本教程这样的测试目的，你可以将其设置为 `https://jwt.ms`，这是 Microsoft 拥有的 web 应用程序，用于显示令牌的已解码内容（令牌的内容绝不会离开你的浏览器）。 在应用程序开发过程中，可以添加应用程序在本地侦听的终结点，如 `https://localhost:5000`。 随时可以添加和修改已注册应用程序中的重定向 Uri。

    以下限制适用于重定向 Uri：

    * 回复 URL 必须以方案 `https`开头。
    * 回复 URL 区分大小写。 它的大小写必须与正在运行的应用程序的 URL 路径匹配。 例如，如果你的应用程序将作为其路径 `.../abc/response-oidc`的一部分包含，请不要在回复 URL 中指定 `.../ABC/response-oidc`。 由于 web 浏览器将路径视为区分大小写，因此在重定向到不匹配的 `.../ABC/response-oidc` URL 时，可能会排除与 `.../abc/response-oidc` 关联的 cookie。

1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.

完成应用程序注册后，启用隐式授权流：

1. **관리**에서 **인증**을 선택합니다.
1. **새 환경을 체험해 보세요.** (표시되는 경우)를 선택합니다.
1. **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장**을 선택합니다.

* * *

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

如果你的应用程序交换访问令牌的授权代码，则需要创建应用程序机密。

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. 在 " **Azure AD B2C 应用**程序" 页中，选择你创建的应用程序，例如*webapp1*。
1. 选择 "**密钥**"，然后选择 "**生成密钥**"。
1. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 在应用程序代码中使用此值作为应用程序机密。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. 在 " **Azure AD B2C 应用注册（预览）** " 页中，选择你创建的应用程序，例如*webapp1*。
1. **관리**에서 **인증서 및 암호**를 선택합니다.
1. **새 클라이언트 비밀**을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대한 설명을 입력합니다. 예: *clientsecret1*.
1. **만료**에서 암호가 유효한 기간을 선택한 다음 **추가**를 선택합니다.
1. 비밀의 **값**을 기록합니다. 在应用程序代码中使用此值作为应用程序机密。

* * *

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

接下来，了解如何创建用户流，使用户能够注册、登录和管理其配置文件。

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C > 中创建用户流](tutorial-create-user-flows.md)
