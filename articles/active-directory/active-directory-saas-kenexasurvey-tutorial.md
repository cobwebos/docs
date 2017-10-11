---
title: "教程：Azure Active Directory 与 IBM Kenexa Survey Enterprise 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 IBM Kenexa Survey Enterprise 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 5c276c23288292a1c54dd9d57177d5072b90c9e3
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>教程：Azure Active Directory 与 IBM Kenexa Survey Enterprise 集成

在本教程中，了解如何将 IBM Kenexa Survey Enterprise 与 Azure Active Directory (Azure AD) 集成。

将 IBM Kenexa Survey Enterprise 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 IBM Kenexa Survey Enterprise。
- 可以让用户通过其 Azure AD 帐户使用单一登录 (SSO) 自动登录到 IBM Kenexa Survey Enterprise。
- 可在一个中心位置（即 Azure 门户）管理帐户。

如果想要深入了解软件型服务 (SaaS) 应用与 Azure AD 集成，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 IBM Kenexa Survey Enterprise 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 IBM Kenexa Survey Enterprise SSO 的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD SSO。 本教程中概述的方案包括两个主要构建基块：

* 从库中添加 IBM Kenexa Survey Enterprise
* 配置和测试 Azure AD SSO

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>从库中添加 IBM Kenexa Survey Enterprise
要配置 IBM Kenexa Survey Enterprise 与 Azure AD 的集成，请从库中将 IBM Kenexa Survey Enterprise 添加到托管 SaaS 应用列表。

若要从库中添加 IBM Kenexa Survey Enterprise，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，单击“Azure Active Directory”按钮。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序”，然后选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加一个应用程序，请点击“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“IBM Kenexa Survey Enterprise”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. 在结果列表中，选择“IBM Kenexa Survey Enterprise”，并单击“添加”按钮以添加该应用程序。

    ![结果列表中的 IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 IBM Kenexa Survey Enterprise 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要识别 Azure AD 中与 IBM Kenexa Survey Enterprise 用户对应的用户。 换句话说，Azure AD 需要在 Azure AD 用户与 IBM Kenexa Survey Enterprise 中相关用户之间建立链接关系。

若要建立此关联关系，请将 IBM Kenexa Survey Enterprise 中“用户名”的值指定为 Azure AD 中“用户名”的值。

若要配置和测试 IBM Kenexa Survey Enterprise 的 Azure AD SSO，请在下面两节中完成构建基块。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分，我们将执行以下操作，在 Azure 门户中启用 Azure AD SSO，并在 IBM Kenexa Survey Enterprise 应用程序中配置 SSO：

1. 在 Azure 门户中的 IBM Kenexa Survey Enterprise 应用程序集成页上，单击“单一登录”。

    ![“IBM Kenexa Survey Enterprise 配置”单一登录链接][4]

2. 在“单一登录”对话框中的“模式”对话框内，选择“基于 SAML 的登录”启用 SSO。
 
    ![“单一登录”对话框](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. 在“IBM Kenexa Survey Enterprise 域和 URL”部分，执行以下步骤：

    ![“IBM Kenexa Survey Enterprise 域和 URL”单一登录信息](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://surveys.kenexa.com/<companycode>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE] 
    > 上面的值不是实际值。 使用实际标识符和回复 URL 更新它们。 若要获得实际值，请联系 [IBM Kenexa Survey Enterprise 支持团队](https://www.ibm.com/support/home/?lnk=fcw)。

4. 在“SAML 签名证书”下面，单击“证书(Base64)”，然后将证书文件保存到计算机。

    ![证书 (Base64) 下载链接](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa Survey Enterprise 应用程序需要收到特殊格式的安全断言标记语言 (SAML) 断言，这将要求你将自定义属性映射添加到 SAML 令牌属性配置中。 响应中的用户标识符声明的值必须匹配在 Kenexa 中配置的 SSO ID。 若要在组织中映射适当的用户标识符作为 SSO Internet Datagram Protocol (IDP)，请联系 [IBM Kenexa Survey Enterprise 支持团队](https://www.ibm.com/support/home/?lnk=fcw)。 

    默认情况下，Azure AD 会设置用户标识符作为用户主体名称 (UPN) 值。 可以在“属性”选项卡上更改此值，如下面的屏幕截图中所示。 仅在正确完成映射后集成才能运行。
    
    ![“用户属性”对话框](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. 单击“保存” 。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. 若要打开“配置登录”窗口，请在“IBM Kenexa Survey Enterprise 配置”下点击“配置 IBM Kenexa Survey Enterprise”。 
 
    ![“配置IBM Kenexa Survey Enterprise”链接](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”的值。

8. “配置登录”窗口中，在“快速参考”下方，复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”的值。

9. 若要在“IBM Kenexa Survey Enterprise”侧配置 SSO，请将已下载的“证书 (Base64)”、“登出 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”的值发送给 [IBM Kenexa Survey Enterprise 支持团队](https://www.ibm.com/support/home/?lnk=fcw)。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中参考这些说明的简明版本了。 从“Active Directory” > “企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档](https://go.microsoft.com/fwlink/?linkid=845985)。
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分，我们将执行以下操作，在 Azure 门户中创建一个名为 Britta Simon 的测试用户：

![创建 Azure AD 测试用户][100]

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。
 
    ![“添加”按钮](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框中，执行以下步骤：
 
    ![“用户”对话框](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>创建 IBM Kenexa Survey Enterprise 测试用户

在本部分中，会在 IBM Kenexa Survey Enterprise 中创建一个名为“Britta Simon”的用户。 

若要在 IBM Kenexa Survey Enterprise 系统中创建用户并为他们映射 SSO ID，可联系 [IBM Kenexa Survey Enterprise 支持团队](https://www.ibm.com/support/home/?lnk=fcw)获取帮助。 应将此 SSO ID 值映射到 Azure AD 中的用户标识符的值。 可以在“属性”选项上中更改此默认设置。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予用户 Britta Simon 访问 IBM Kenexa Survey Enterprise 的权限，允许其使用 Azure SSO。

![分配用户角色][200] 

要将用户 Britta Simon 分配到 IBM Kenexa Survey Enterprise，请执行以下步骤：

1. 在 Azure 门户中，打开“应用程序”视图，转到“目录”视图，选择“企业应用程序”，并单击“所有应用程序”。

    ![“企业应用程序”和“所有应用程序”链接][201] 

2. 在应用程序列表中，选择“IBM Kenexa Survey Enterprise”。

    ![应用程序列表中的“IBM Kenexa Survey Enterprise”链接](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. 在左窗格中，单击“用户和组”。

    ![“用户和组”链接][202] 

4. 单击“添加”按钮，并在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中的“用户”列表内，选择“Britta Simon”。

6. 在“用户和组”对话框中，单击“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分，我们将使用访问面板测试 Azure AD SSO 配置。

在访问面板中单击 IBM Kenexa Survey Enterprise 磁贴就会自动登录到 IBM Kenexa Survey Enterprise 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 
