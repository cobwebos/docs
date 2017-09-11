---
title: "教程：将 Azure Active Directory 与 SAP HANA Cloud Platform Identity Authentication 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 SAP HANA Cloud Platform Identity Authentication 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9b3f305e71502119e5dee6592347cf30bafef157
ms.contentlocale: zh-cn
ms.lasthandoff: 04/03/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform-identity-authentication"></a>教程：将 Azure Active Directory 与 SAP HANA Cloud Platform Identity Authentication 集成

本教程介绍如何将 SAP HANA Cloud Platform Identity Authentication 与 Azure Active Directory (Azure AD) 集成。 SAP HANA Cloud Platform Identity Authentication 可用作代理 IdP 来访问 SAP 应用程序，后者使用 Azure AD 作为主 IdP。

将 SAP HANA Cloud Platform Identity Authentication 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 SAP 应用程序
- 可以让用户使用其 Azure AD 帐户自动登录到 SAP 应用程序单一登录 (SSO)
- 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。


## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP HANA Cloud Platform Identity Authentication 的集成，需备齐以下各项：

- Azure AD 订阅
- 启用了 **SAP HANA Cloud Platform Identity Authentication** SSO 的订阅


>[!NOTE] 
>不建议使用生产环境测试本教程中的步骤。
>

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库添加 SAP HANA Cloud Platform Identity Authentication
2. 配置和测试 Azure AD SSO

在深入探讨技术细节之前，有必要先了解那些会遇到的概念。 通过 SAP HANA Cloud Platform Identity Authentication 和 Azure Active Directory 进行联合身份验证，用户可以跨那些受 AAD（充当 IdP）保护的应用程序或服务实现 SSO，而 SAP 应用程序和服务则受 SAP HANA Cloud Platform Identity Authentication 保护。

目前，SAP HANA Cloud Platform Identity Authentication 充当 SAP 应用程序的代理标识提供者， 而 Azure Active Directory 则在此设置中充当主要标识提供者。 

下图对此进行了说明：    

![创建 Azure AD 测试用户](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

在此设置中，SAP HANA Cloud Platform Identity Authentication 租户将配置为 Azure Active Directory 中的可信应用程序。 

随后会在 SAP HANA Cloud Platform Identity Authentication 管理控制台中对用户希望通过这种方式进行保护的所有 SAP 应用程序和服务进行配置！

这意味着，如果采用此类设置，则在授予对 SAP 应用程序和服务的访问权限时，需在 SAP HANA Cloud Platform Identity Authentication 中完成授权（不同于在 Azure Active Directory 中配置授权）。

通过 Azure Active Directory Marketplace 将 SAP HANA Cloud Platform Identity Authentication 配置为应用程序以后，将无需配置为 SAP 应用程序生成有效身份验证令牌所必需的各个声明/SAML 断言和转换。

>[!NOTE] 
>目前 Web SSO 仅经双方验证。 进行应用到 API 或 API 到 API 通信所需的流应可正常运行，但尚未经过测试。 将在后续活动中对这些流进行测试。
>

## <a name="add-sap-hana-cloud-platform-identity-authentication-from-the-gallery"></a>从库添加 SAP HANA Cloud Platform Identity Authentication
若要配置 SAP HANA Cloud Platform Identity Authentication 到 Azure AD 中的集成，需将 SAP HANA Cloud Platform Identity Authentication 从库添加到托管型 SaaS 应用的列表。

**若要从库添加 SAP HANA Cloud Platform Identity Authentication，请执行以下步骤：**

1. 在 [**Azure 管理门户**](https://portal.azure.com)的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“SAP HANA Cloud Platform Identity Authentication”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_01.png)

5. 在结果面板中选择“SAP HANA Cloud Platform Identity Authentication”，然后单击“添加”按钮该添加应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_02.png)


##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户，通过 SAP HANA Cloud Platform Identity Authentication 来配置和测试 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 SAP HANA Cloud Platform Identity Authentication 用户。 换句话说，需要在 Azure AD 用户与 SAP HANA Cloud Platform Identity Authentication 中相关用户之间建立关联关系。

通过将 Azure AD 中“用户名”的值分配为 SAP HANA Cloud Platform Identity Authentication 中“用户名”的值来建立此关联关系。

若要配置和测试 SAP HANA Cloud Platform Identity Authentication 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SAP HANA Cloud Platform Identity Authentication 测试用户](#creating-a-sap-hana-cloud-platform-identity-authentication-test-user)** - 在 SAP HANA Cloud Platform Identity Authentication 中创建 Britta Simon 的对应用户，将其关联到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-sso"></a>配置 Azure AD SSO

在本部分，需在 Azure 管理门户中启用 Azure AD SSO，然后在 SAP HANA Cloud Platform Identity Authentication 应用程序中配置单一登录。

SAP HANA Cloud Platform Identity Authentication 应用程序要求 SAML 断言采用特定格式。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。

![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_03.png)

**若要通过 SAP HANA Cloud Platform Identity Authentication 配置 Azure AD SSO，请执行以下步骤：**

1. 在 Azure 管理门户的“SAP HANA Cloud Platform Identity Authentication”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录][5]

3. 在“单一登录”对话框的“用户属性”部分，如果 SAP 应用程序需要一个属性（例如“firstName”）， 则可在 SAML 令牌属性对话框中，添加“firstName”属性。
 1. 单击“添加属性”，打开“添加属性”对话框。
 
    ![配置单一登录][6]

    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_05.png)
 2. 在“属性名称”文本框中，键入属性名称“firstName”。
 3. 在“属性值”列表中，选择属性值“user.givenname”。
 4. 单击“确定” 。

4. 在“SAP HANA Cloud Platform Identity Authentication 域和 URL”部分，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_06.png)
 1. 在“登录 URL”文本框中，键入 SAP 应用程序的登录 URL。
 2. 在“标识符”文本框中，按 `<entity-id>.accounts.ondemand.com` 模式键入值 
    * 如果不知道此值，请按有关 [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)（租户 SAML 2.0 配置）的 SAP HANA Cloud Platform Identity Authentication 文档的说明操作。

5. 在“SAP HANA Cloud Platform Identity Authentication 配置”部分，单击“配置 SAP HANA Cloud Platform Identity Authentication”打开“配置登录”对话框。 然后单击“SAML XML 元数据”，在计算机上保存该文件。

    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_07.png) 

    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_08.png)

6. 若要为应用程序配置 SSO，请转到“SAP HANA Cloud Platform Identity Authentication 管理控制台”。 URL 采用以下模式：`https://<tenant-id>.accounts.ondemand.com/admin`
 * 然后，按照有关 SAP HANA Cloud Platform Identity Authentication 的文档的说明操作，以便[在 SAP HANA Cloud Platform Identity Authentication 上将 Microsoft Azure AD 配置为企业标识提供者](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)。 

7. 在 Azure 管理门户中，单击“保存”按钮。
8. 仅当要为另一个 SAP 应用程序添加和启用 SSO 时，才继续执行以下步骤。 重复“从库中添加 SAP HANA Cloud Platform Identity Authentication”部分下的步骤，添加 SAP HANA Cloud Platform Identity Authentication 的另一个实例。
9. 在 Azure 管理门户的 **SAP HANA Cloud Platform Identity Authentication** 应用程序集成页上，单击“链接登录”。

    ![配置链接登录](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)
10. 然后，保存配置。

>[!NOTE] 
>新应用程序将利用以前 SAP 应用程序的 SSO 配置。 请确保在 SAP HANA Cloud Platform Identity Authentication 管理控制台中使用相同的企业标识提供程序。
>

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在新门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png) 
  1. 在“名称”文本框中，键入 **BrittaSimon**。
  2. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。
  3. 选择“显示密码”并记下“密码”的值。
  4. 单击“创建”。 

### <a name="create-a-sap-hana-cloud-platform-identity-authentication-test-user"></a>创建 SAP HANA Cloud Platform Identity Authentication 测试用户

不需在 SAP HANA Cloud Platform Identity Authentication 上创建用户。 Azure AD 用户存储中的用户可以使用 SSO 功能。

SAP HANA Cloud Platform Identity Authentication 支持“联合身份验证”选项。 此选项允许应用程序检查 SAP HANA Cloud Platform Identity Authentication 的用户存储中是否存在经过公司标识提供者身份验证的用户。 

在默认设置中，“联合身份验证”选项已禁用。 如果启用了联合身份验证，则仅已导入 SAP HANA Cloud Platform Identity Authentication 中的用户能够访问该应用程序。 

如需详细了解如何启用或禁用 SAP HANA Cloud Platform Identity Authentication 的联合身份验证，请参阅[使用 SAP HANA Cloud Platform Identity Authentication 的用户存储配置联合身份验证](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)中的“启用 SAP HANA Cloud Platform Identity Authentication 的联合身份验证”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP HANA Cloud Platform Identity Authentication 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 SAP HANA Cloud Platform Identity Authentication，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“SAP HANA Cloud Platform Identity Authentication”。

    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sap_cloud_identity_09.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD SSO 配置。

单击访问面板中的“SAP HANA Cloud Platform Identity Authentication”磁贴就会自动登录到 SAP HANA Cloud Platform Identity Authentication 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_06.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_203.png
