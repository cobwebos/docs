---
title: SAML 单一登录-非库应用程序-Microsoft 标识平台 |Microsoft Docs
description: 配置 Microsoft 标识平台中的非库应用程序的单一登录（SSO）（Azure AD）
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 07/19/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c99c38c9a1972bc434c7fa61d6745dac0d79d7b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711939"
---
# <a name="configure-saml-based-single-sign-on-to-non-gallery-applications"></a>为非库应用程序配置基于 SAML 的单一登录

[将库应用](add-gallery-app.md)或[非库 web 应用](add-non-gallery-app.md)添加到 Azure AD 企业应用程序时，可以使用的单一登录选项之一是[基于 SAML 的单一登录](what-is-single-sign-on.md#saml-sso)。 尽可能为使用 SAML 协议进行身份验证的应用程序选择 SAML。 SAML Single Sign-On을 사용하는 Azure AD는 사용자의 Azure AD 계정을 사용하여 애플리케이션에 인증합니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. 可以根据在 SAML 声明中定义的规则将用户映射到特定应用程序角色。 이 문서에서는 비갤러리 앱에 대한 SAML 기반 Single Sign-On을 구성하는 방법에 대해 설명합니다. 

> [!NOTE]
> 갤러리 앱을 추가하나요? 이에 대한 단계별 설치 지침은 [SaaS 앱 자습서 목록](../saas-apps/tutorial-list.md)에서 확인하세요.

若要在不编写代码的情况下为非库应用程序配置 SAML 单一登录，需要具有一个订阅或 Azure AD Premium，并且该应用程序必须支持 SAML 2.0。 Azure AD 버전에 대한 자세한 내용은 [Azure AD 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

如果尚未将应用程序添加到 Azure AD 租户，请参阅[添加非库应用](add-non-gallery-app.md)。

## <a name="step-1-edit-the-basic-saml-configuration"></a>1단계. 기본 SAML 구성 편집

1. [Azure Portal](https://portal.azure.com)에 Azure AD 테넌트의 클라우드 애플리케이션 관리자 또는 애플리케이션 관리자로 로그인합니다.

2. **Azure Active Directory** > **엔터프라이즈 애플리케이션**으로 차례로 이동하고, 목록에서 애플리케이션을 선택합니다. 
   
   - 애플리케이션을 검색하려면 **애플리케이션 유형** 메뉴에서 **모든 애플리케이션**, **적용**을 차례로 선택합니다. 검색 상자에서 애플리케이션 이름을 입력한 다음, 결과에서 애플리케이션을 선택합니다.

3. **관리** 섹션 아래에서 **Single Sign-On**을 선택합니다. 

4. **SAML**을 선택합니다. **SAML로 Single Sign-On 설정 - 미리 보기** 페이지가 표시됩니다.

   ![步骤1编辑基本 SAML 配置](media/configure-single-sign-on-non-gallery-applications/step-one-basic-saml-config.png)

5. 기본 SAML 구성 옵션을 편집하려면 **기본 SAML 구성** 섹션의 오른쪽 위 모서리에 있는 **편집** 아이콘(연필 모양)을 선택합니다.

1. 다음 설정을 입력합니다. 你应从应用程序供应商处获取值。 필드 값을 추출하려면 수동으로 값을 입력하거나 메타데이터 파일을 업로드할 수 있습니다.

    | 기본 SAML 구성 설정 | SP 시작 | idP 시작 | Description |
    |:--|:--|:--|:--|
    | **标识符（实体 ID）** | 일부 앱의 경우 필수 | 일부 앱의 경우 필수 | 唯一标识该应用程序。 Azure AD는 SAML 토큰의 대상 매개 변수로 애플리케이션에 식별자를 보냅니다. 애플리케이션이 식별자의 유효성을 검사해야 합니다. 또한 이 값은 애플리케이션에서 제공하는 모든 SAML 메타데이터 내에서 엔터티 ID로 표시됩니다. 输入使用以下模式的 URL： "https://<subdomain>. contoso.com"。*可以在应用程序发送的**AuthnRequest** （SAML 请求）中找到此值作为**颁发者**元素。* |
    | **회신 URL** | 필수 | 필수 | 애플리케이션이 SAML 토큰을 수신해야 하는 위치를 지정합니다. 회신 URL은 ACS(Assertion Consumer Service) URL이라고도 합니다. 您可以使用 "其他回复 URL" 字段指定多个答复 Url。 例如，你可能需要多个子域的其他回复 Url。 或者，出于测试目的，可以一次指定多个回复 Url （本地主机和公共 Url）。 |
    | **로그온 URL** | 필수 | 지정하지 않음 | 사용자가 이 URL을 열면 서비스 공급자가 Azure AD를 리디렉션하여 사용자를 인증하고 로그온하도록 합니다. Azure AD는 URL을 사용하여 Office 365 또는 Azure AD 액세스 패널에서 애플리케이션을 시작합니다. 如果为空，当用户从 Office 365、Azure AD 访问面板或 Azure AD SSO URL 启动应用程序时，Azure AD 会执行 IdP 启动的登录。|
    | **中继状态** | 선택 사항 | 선택 사항 | 인증이 완료되면 사용자를 리디렉션할 위치를 애플리케이션에 지정합니다. 일반적으로 이 값은 애플리케이션에 대한 올바른 URL입니다. 그러나 일부 애플리케이션에서는 이 필드를 다르게 사용합니다. 자세한 내용은 애플리케이션 공급 업체에 요청하세요.
    | **로그아웃 URL** | 선택 사항 | 선택 사항 | SAML 로그아웃 응답을 애플리케이션에 다시 보내는 데 사용됩니다.

有关详细信息，请参阅[单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md)。

## <a name="step-2-configure-user-attributes-and-claims"></a>2단계. 사용자 특성 및 클레임 구성 

当用户对应用程序进行身份验证时，Azure AD 向应用程序颁发 SAML 令牌，其中包含有关唯一标识它们的用户的信息（或声明）。 默认情况下，此信息包括用户的用户名、电子邮件地址、名字和姓氏。 例如，如果应用程序需要特定声明值或用户名之外的**名称**格式，则可能需要自定义这些声明。 갤러리 앱에 대한 요구 사항은 [애플리케이션 관련 자습서](../saas-apps/tutorial-list.md)에 설명되어 있거나 애플리케이션 공급업체에 문의할 수 있습니다. 사용자 특성 및 클레임을 구성하는 일반적인 단계는 다음과 같습니다.

1. **사용자 특성 및 클레임** 섹션의 오른쪽 위 모서리에서 **편집** 아이콘(연필)을 선택합니다.

   ![步骤2配置用户属性和声明](media/configure-single-sign-on-non-gallery-applications/step-two-user-attributes-claims.png)

2. **이름 식별자 값**을 확인합니다. 기본값은 *user.principalname*입니다. 사용자 ID는 애플리케이션 내에서 각 사용자를 고유하게 식별합니다. 예를 들어 이메일 주소가 사용자 이름 및 고유 식별자 모두인 경우 값을 *user.mail*로 설정합니다.

3. **이름 식별자 값**을 수정하려면 **이름 식별자 값** 필드에 대한 **편집** 아이콘(연필 모양)을 선택합니다. 필요에 따라 식별자 형식과 원본을 적절하게 변경합니다. 자세한 내용은 [NameId 편집](../develop/active-directory-saml-claims-customization.md#editing-nameid)을 참조하세요. 완료되면 변경 내용을 저장합니다. 
 
4. 그룹 클레임을 구성하려면 **클레임에서 반환되는 그룹** 필드에 대한 **편집** 아이콘을 선택합니다. 자세한 내용은 [그룹 클레임 구성](../hybrid/how-to-connect-fed-group-claims.md)을 참조하세요.

5. 클레임을 추가하려면 페이지의 위쪽에서 **새 클레임 추가**를 선택합니다. **이름**을 입력하고 적절한 원본을 선택합니다. **특성** 원본을 선택하는 경우 사용하려는 **원본 특성**을 선택해야 합니다. **번역** 원본을 선택하는 경우 사용하려는 **변환** 및 **매개 변수 1**을 선택해야 합니다. 자세한 내용은 [애플리케이션 관련 클레임 추가](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)를 참조하세요. 완료되면 변경 내용을 저장합니다. 

6. **저장**을 선택합니다. 테이블에 새 클레임이 표시됩니다.

   > [!NOTE]
   > Azure AD에서 애플리케이션에 대한 SAML 토큰을 사용자 지정하는 다른 방법은 다음 리소스를 참조하세요.
   >- Azure Portal를 통해 사용자 지정 역할을 만들려면 [역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)을 참조하세요.
   >- PowerShell을 통해 클레임을 사용자 지정하려면 [클레임 사용자 지정 - PowerShell](../develop/active-directory-claims-mapping.md)을 참조하세요.
   >- 애플리케이션에 대한 선택적 클레임을 구성하도록 애플리케이션 매니페스트를 수정하려면 [선택적 클레임 구성](../develop/active-directory-optional-claims.md)을 참조하세요.
   >- 새로 고침 토큰, 액세스 토큰, 세션 토큰 및 ID 토큰에 대 한 토큰 수명 정책을 설정하려면 [토큰 수명 구성](../develop/active-directory-configurable-token-lifetimes.md)을 참조하세요. 또는 Azure AD 조건부 액세스를 통해 인증 세션을 제한하려면 [인증 세션 관리 기능](https://go.microsoft.com/fwlink/?linkid=2083106)을 참조하세요.

## <a name="step-3-manage-the-saml-signing-certificate"></a>3단계. SAML 서명 인증서 관리

Azure AD는 인증서를 사용하여 애플리케이션에 보내는 SAML 토큰에 서명합니다. Azure AD 및 애플리케이션 간 신뢰를 설정하려면 이 인증서가 필요합니다. 인증서 형식에 대한 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 有关详细信息，请参阅[在 SAML 令牌中](certificate-signing-options.md)[管理用于联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)和高级证书签名选项。

从 Azure AD 中，可以直接从主**设置 SAML 的单一登录**页下载 Base64 或 Raw 格式的活动证书。 或者，你可以通过下载应用程序元数据 XML 文件或使用应用联合元数据 URL 来获取活动证书。 若要查看、创建或下载证书（"活动" 或 "非活动"），请执行以下步骤。

1. **SAML 서명 인증서** 섹션으로 이동합니다. 

   ![步骤3管理 SAML 签名证书](./media/configure-single-sign-on-non-gallery-applications/step-three-certificate.png)

2. 인증서에 다음이 있는지 확인합니다.

   - *所需的到期日期。* 你可以将到期日期配置为在未来最多三年。
   - *所需证书的活动状态。* 如果状态为 "**非**活动"，则将状态更改为 "**活动**"。 若要更改状态，请右键单击所需证书的行，然后选择 "**使证书处于活动**状态"。
   - *正确的签名选项和算法。*
   - *正确的通知电子邮件地址。* 当活动证书接近到期日期时，Azure AD 向此字段中配置的电子邮件地址发送通知。

2. 若要下载证书，请选择 Base64 格式、原始格式或联合元数据 XML 的选项之一。 또한 Azure AD는 `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 형식의 애플리케이션과 관련된 메타데이터에 액세스할 수 있는 **앱 페더레이션 메타데이터 URL**을 제공합니다.

3. 若要管理、创建或导入证书，请选择 " **SAML 签名证书**" 部分右上角的 "**编辑**" 图标（铅笔）。

   ![SAML 签名证书](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)


   执行以下任一操作：

   - 새 인증서를 만들려면 **새 인증서**, **만료 날짜**, **저장**을 차례로 선택합니다. 인증서를 활성화하려면 상황에 맞는 메뉴( **...** ), **인증서를 활성 상태로 만들기**를 차례로 선택합니다.
   - 프라이빗 키 및 pfx 자격 증명을 사용하여 인증서를 업로드하려면 **인증서 가져오기**를 선택하여 해당 인증서로 이동합니다. **PFX 암호**를 입력한 다음, **추가**를 선택합니다.  
   - 고급 인증서 서명 옵션을 구성하려면 다음 옵션을 사용합니다. 이러한 옵션에 대한 설명은 [고급 인증서 서명 옵션](certificate-signing-options.md) 문서를 참조하세요.
      - **서명 옵션** 드롭다운 목록에서 **SAML 응답 서명**, **SAML 어설션 서명** 또는 **SAML 응답 및 어설션 서명**을 선택합니다.
      - **서명 알고리즘** 드롭다운 목록에서 **SHA-1** 또는 **SHA-256**을 선택합니다.
   - 활성 인증서의 만료 날짜가 가까워질 때 추가 사용자에게 알리려면 **알림 이메일 주소** 필드에서 이메일 주소를 입력합니다.

4. 如果进行了更改，请选择 " **SAML 签名证书**" 部分顶部的 "**保存**"。 

## <a name="step-4-set-up-the-application-to-use-azure-ad"></a>4단계. Azure AD를 사용하도록 애플리케이션 설정

**\<applicationName> 설정** 섹션에는 애플리케이션에서 구성해야 하는 값이 나열되어 있으므로 Azure AD를 SAML ID 공급자로 사용합니다. 필요한 값은 애플리케이션에 따라 달라집니다. 자세한 내용은 애플리케이션의 SAML 설명서를 참조합니다. 若要查找文档，请参阅**设置 \<应用程序名称 >** 标题，并选择 "**查看分步说明**"。 文档将出现在 "**配置登录**" 页中。 该页指导您填写 "**设置 \<应用程序名称 >** " 标题中的**登录 Url**、 **Azure AD 标识符**和**注销 URL**值。

1. **\<applicationName> 설정** 섹션까지 아래로 스크롤합니다. 
   
   ![步骤4设置应用程序](media/configure-single-sign-on-non-gallery-applications/step-four-app-config.png)

1. 필요에 따라 이 섹션의 각 행에서 값을 복사하고, 해당 값을 애플리케이션에 추가하기 위한 애플리케이션 관련 지침을 따릅니다. 갤러리 앱의 경우 **단계별 지침 보기**를 선택하여 설명서를 볼 수 있습니다. 
   - **로그인 URL** 및 **로그아웃 URL** 값은 모두 동일한 엔드포인트로 확인됩니다. 이 엔드포인트는 Azure AD 인스턴스에 대한 SAML 요청 처리 엔드포인트입니다. 
   - **Azure AD 식별자**는 애플리케이션에 발급된 SAML 토큰의 **Issuer**(발급자) 값입니다.
2. 모든 값을 해당 필드에 붙여넣은 다음, **저장**을 선택합니다.

## <a name="step-5-validate-single-sign-on"></a>5단계. Single Sign-On 유효성 검사

将应用程序配置为使用 Azure AD 作为基于 SAML 的标识提供程序后，你可以测试设置以查看单一登录是否适用于你的帐户。 

2. **<applicationName>을(를) 사용하여 Single Sign-On 유효성 검사** 섹션으로 스크롤합니다.

   ![步骤5验证单一登录](media/configure-single-sign-on-non-gallery-applications/step-five-validate.png)

3. **유효성 검사**를 선택합니다. 테스트 옵션이 표시됩니다.

4. **현재 사용자로 로그인**을 선택합니다. 

로그온에 성공하면 사용자 및 그룹을 SAML 애플리케이션에 할당할 준비가 된 것입니다.
오류 메시지가 표시되면 다음 단계를 수행합니다.

1. 세부 정보를 **어떤 오류인 것 같습니까?** 상자에 복사하고 붙여넣습니다.

    ![해결 지침 가져오기](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. **해결 지침 가져오기**를 선택합니다. 근본 원인 및 해결 지침이 표시됩니다.  이 예제에서 사용자는 애플리케이션에 할당되지 않았습니다.

3. 해결 지침을 읽은 다음, 가능한 경우 문제를 해결합니다.

4. 성공적으로 완료될 때까지 테스트를 다시 실행합니다.

有关详细信息，请参阅[在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录](../develop/howto-v1-debug-saml-sso-issues.md)。

## <a name="next-steps"></a>다음 단계

- [애플리케이션에 사용자 또는 그룹 할당](methods-for-assigning-users-and-groups.md)
- [자동 사용자 계정 프로비저닝 구성](configure-automatic-user-provisioning-portal.md)
