---
title: "教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 SAP Cloud Platform Identity Authentication 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: e4e7c7273acc216ae82a52b3e6dcd530a6ad1bd7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成

本教程介绍如何将 SAP Cloud Platform Identity Authentication 与 Azure Active Directory (Azure AD) 集成。 SAP Cloud Platform Identity Authentication 可用作代理 IdP 来访问 SAP 应用程序，后者使用 Azure AD 作为主 IdP。

将 SAP Cloud Platform Identity Authentication 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 SAP 应用程序。
- 可以让用户使用其 Azure AD 帐户自动登录到 SAP 应用程序单一登录 (SSO)。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP Cloud Platform Identity Authentication 的集成，需备齐以下各项：

- Azure AD 订阅
- 已启用 SAP Cloud Platform Identity Authentication 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 SAP Cloud Platform Identity Authentication
2. 配置并测试 Azure AD 单一登录

在深入探讨技术细节之前，有必要先了解那些会遇到的概念。 通过 SAP Cloud Platform Identity Authentication 和 Azure Active Directory 进行联合身份验证，用户可以跨那些受 AAD（充当 IdP）保护的应用程序或服务实现 SSO，而 SAP 应用程序和服务则受 SAP Cloud Platform Identity Authentication 保护。

目前，SAP Cloud Platform Identity Authentication 充当 SAP 应用程序的代理标识提供者， 而 Azure Active Directory 则在此设置中充当主要标识提供者。 

下图对此进行了说明：

![创建 Azure AD 测试用户](./media/active-directory-saas-sapcloudauth-tutorial/architecture-01.png)

在此设置中，SAP Cloud Platform Identity Authentication 租户将配置为 Azure Active Directory 中的可信应用程序。 

随后会在 SAP Cloud Platform Identity Authentication 管理控制台中对用户希望通过这种方式进行保护的所有 SAP 应用程序和服务进行配置！

这意味着，如果采用此类设置，则在授予对 SAP 应用程序和服务的访问权限时，需在 SAP Cloud Platform Identity Authentication 中完成授权（不同于在 Azure Active Directory 中配置授权）。

通过 Azure Active Directory Marketplace 将 SAP Cloud Platform Identity Authentication 配置为应用程序以后，将无需配置为 SAP 应用程序生成有效身份验证令牌所必需的各个声明/SAML 断言和转换。

>[!NOTE] 
>目前 Web SSO 仅经双方验证。 进行应用到 API 或 API 到 API 通信所需的流应可正常运行，但尚未经过测试。 将在后续活动中对这些流进行测试。
>

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>从库添加 SAP Cloud Platform Identity Authentication
要配置 SAP Cloud Platform Identity Authentication 到 Azure AD 中的集成，需将 SAP Cloud Platform Identity Authentication 从库添加到托管型 SaaS 应用的列表。

**若要从库添加 SAP Cloud Platform Identity Authentication，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中键入 **SAP Cloud Platform Identity Authentication**，从结果面板中选择“SAP Cloud Platform Identity Authentication”，单击“添加”按钮添加该应用程序。

    ![结果列表中的 SAP Cloud Platform Identity Authentication](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SAP Cloud Platform Identity Authentication 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SAP Cloud Platform Identity Authentication 用户。 换句话说，需要在 Azure AD 用户与 SAP Cloud Platform Identity Authentication 中相关用户之间建立关联关系。

可通过将 Azure AD 中“用户名”的值指定为 SAP Cloud Platform Identity Authentication 中“用户名”的值来建立此关联关系。

若要配置和测试 SAP Cloud Platform Identity Authentication 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SAP Cloud Platform Identity Authentication 测试用户](#create-an-sap-cloud-platform-identity-authentication-test-user)** - 在 SAP Cloud Platform Identity Authentication 中创建 Britta Simon 的对应用户，将其关联到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，需在 Azure 门户中启用 Azure AD 单一登录，并在 SAP Cloud Platform Identity Authentication 应用程序中配置单一登录。

**若要通过 SAP Cloud Platform Identity Authentication 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的“SAP Cloud Platform Identity Authentication”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_samlbase.png)

3. 在“SAP Cloud Platform Identity Authentication 域和 URL”部分中，如果要在 **IDP** 发起的模式下配置应用程序，请执行以下步骤：

    ![SAP Cloud Platform Identity Authentication 域和 URL 单一登录信息](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url.png)

    在“标识符”文本框中，使用以下模式键入 URL：`https://<entity-id>.accounts.ondemand.com`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际标识符更新此值。 请联系 [SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) 获取此值。 如果不知道此值，请按有关 [Tenant SAML 2.0 Configuration](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)（租户 SAML 2.0 配置）的 SAP Cloud Platform Identity Authentication 文档的说明操作。

4. 选中“显示高级 URL 设置”。 如果要在“SP”发起的模式下配置应用程序：

    ![SAP Cloud Platform Identity Authentication 域和 URL 单一登录信息](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<entity-id>.accounts.ondemand.com/admin`

    > [!NOTE] 
    > 此值不是真实值。 使用实际登录 URL 更新此值。 请联系 [SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) 获取此值。

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_certificate.png)

6. SAP Cloud Platform Identity Authentication 应用程序要求 SAML 断言采用特定格式。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。 

    ![配置单一登录](./media/active-directory-saas-sapcloudauth-tutorial/attribute.png)

7. 在“单一登录”对话框的“用户属性”部分，如果 SAP 应用程序需要一个属性（例如“firstName”）， 则可在 SAML 令牌属性对话框中，添加“firstName”属性。

    a. 单击“添加属性”，打开“添加属性”对话框。
    
    ![配置单一登录](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入属性名称“firstName”。
    
    c. 在“值”列表中，选择属性值“user.givenname”。
    
    d.单击“下一步”。 单击“确定” 。

8. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_400.png)

9. 在“SAP Cloud Platform Identity Authentication 配置”部分，单击“配置 SAP Cloud Platform Identity Authentication”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![SAP Cloud Platform Identity Authentication 配置](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_configure.png) 

10. 若要为应用程序配置 SSO，请转到“SAP Cloud Platform Identity Authentication 管理控制台”。 URL 采用以下模式：`https://<tenant-id>.accounts.ondemand.com/admin`。 然后，按照有关 SAP Cloud Platform Identity Authentication 的文档的说明操作，以便[在 SAP Cloud Platform Identity Authentication 上将 Microsoft Azure AD 配置为企业标识提供者](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)。 

11. 在 Azure 门户中单击“保存”按钮。

12. 仅当要为另一个 SAP 应用程序添加和启用 SSO 时，才继续执行以下步骤。 重复“从库中添加 SAP Cloud Platform Identity Authentication”部分下的步骤，添加 SAP Cloud Platform Identity Authentication 的另一个实例。

13. 在 Azure 门户的“SAP Cloud Platform Identity Authentication”应用程序集成页上，单击“链接的登录”。

    ![配置链接登录](./media/active-directory-saas-sapcloudauth-tutorial/linked_sign_on.png)

14. 保存配置。

>[!NOTE] 
>新应用程序将利用以前 SAP 应用程序的 SSO 配置。 请确保在 SAP Cloud Platform Identity Authentication 管理控制台中使用相同的企业标识提供程序。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-sapcloudauth-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>创建 SAP Cloud Platform Identity Authentication 测试用户

不需在 SAP Cloud Platform Identity Authentication 上创建用户。 Azure AD 用户存储中的用户可以使用 SSO 功能。

SAP Cloud Platform Identity Authentication 支持“联合身份验证”选项。 此选项允许应用程序检查 SAP Cloud Platform Identity Authentication 的用户存储中是否存在经过公司标识提供者身份验证的用户。 

在默认设置中，“联合身份验证”选项已禁用。 如果启用了联合身份验证，则仅已导入 SAP Cloud Platform Identity Authentication 中的用户能够访问该应用程序。 

如需详细了解如何启用或禁用 SAP Cloud Platform Identity Authentication 的联合身份验证，请参阅[使用 SAP Cloud Platform Identity Authentication 的用户存储配置联合身份验证](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)中的“启用 SAP Cloud Platform Identity Authentication 的联合身份验证”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP Cloud Platform Identity Authentication 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 SAP Cloud Platform Identity Authentication，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“SAP Cloud Platform Identity Authentication”。

    ![应用程序列表中的“SAP Cloud Platform Identity Authentication”链接](./media/active-directory-saas-sapcloudauth-tutorial/tutorial_sapcpia_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“SAP Cloud Platform Identity Authentication”磁贴就会自动登录到 SAP Cloud Platform Identity Authentication 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapcloudauth-tutorial/tutorial_general_203.png


