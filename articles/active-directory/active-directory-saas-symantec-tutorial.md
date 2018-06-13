---
title: 教程：Azure Active Directory 与 Symantec Web Security Service (WSS) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Symantec Web Security Service (WSS) 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 0dc75a1091c56ddbbffcd54047b2465ece231254
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346170"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>教程：Azure Active Directory 与 Symantec Web Security Service (WSS) 集成

本教程介绍如何将 Symantec Web Security Service (WSS) 帐户与 Azure Active Directory (Azure AD) 帐户集成，以便 WSS 可使用 SAML 身份验证对 Azure AD 中预配的最终用户进行身份验证，并强制执行用户级或组级策略规则。

将 Symantec Web Security Service (WSS) 与 Azure AD 集成可提供以下优势：

- 从 Azure AD 门户管理 WSS 帐户使用的所有最终用户和组。 

- 允许最终用户使用其 Azure AD 凭据在 WSS 中验证自己的身份。

- 对于在 WSS 帐户中定义的用户和组级策略规则启用强制执行。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Symantec Web Security Service (WSS) 的集成，需要以下项：

- Azure AD 订阅
- Symantec Web Security Service (WSS) 帐户

> [!NOTE]
> 若要测试本教程中的步骤，我们不建议使用当前正用于生产用途的 WSS 帐户。

测试本教程中的步骤应遵循以下建议：

- 除非有必要，否则不要使用目前用于生产用途的 WSS 帐户进行此测试。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将配置 Azure AD，以使用 Azure AD 帐户中定义的最终用户凭据启用对 WSS 的单点登录。
本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Symantec Web Security Service (WSS) 应用
2. 配置和测试 Azure AD 单一登录

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>从库添加 Symantec Web Security Service (WSS)
若要配置 Symantec Web Security Service (WSS) 与 Azure AD 的集成，需将库中的 Symantec Web Security Service (WSS) 添加到托管 SaaS 应用列表。

若要从库添加 Symantec Web Security Service (WSS)，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Symantec Web Security Service (WSS)”，从结果面板选择“Symantec Web Security Service (WSS)”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Symantec Web Security Service (WSS)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 Symantec Web Security Service (WSS) 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Symantec Web Security Service (WSS) 用户。 换句话说，需在 Azure AD 用户与 Symantec Web Security Service (WSS) 中相关用户间建立链接关系。

将 Azure AD 中“用户名”的值指定为 Symantec Web Security Service (WSS) 中“用户名”的值来建立此链接关系。

若要配置和测试 Symantec Web Security Service (WSS) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Symantec Web Security Service (WSS) 测试用户](#create-a-symantec-web-security-service-wss-test-user)** - 在 Symantec Web Security Service (WSS) 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Symantec Web Security Service (WSS) 应用程序中配置单一登录。

若要配置 Symantec Web Security Service (WSS) 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Symantec Web Security Service (WSS) 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. 在“Symantec Web Security Service (WSS) 域和 URL”部分中，执行以下步骤：

    ![Symantec Web Security Service (WSS) 域和 URL 单一登录信息](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. 在“标识符”文本框中，键入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. 在“回复 URL”文本框中，键入 URL：`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > 如果“标识符”和“回复 URL”的值由于某种原因不起作用，请联系[ Symantec Web Security Service (WSS) 客户支持团队](https://www.symantec.com/contact-us)。

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. 若要在 Symantec Web Security Service (WSS) 端配置单一登录，请参阅 WSS 联机文档。 需要将下载的“元数据 XML”文件导入到 WSS 门户。 如果配置 WSS 门户时需要帮助，请联系 [Symantec Web Security Service (WSS) 支持团队](https://www.symantec.com/contact-us)。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>创建 Symantec Web Security Service (WSS) 测试用户

在本部分中，将在 Symantec Web Security Service (WSS) 中创建一个名为 Britta Simon 的用户。 可在 WSS 门户中手动创建相应的最终用户名，或可等待 Azure AD 中预配的用户/组在几分钟（约 15 分钟）后同步到 WSS 门户。 使用单一登录前，必须先创建并激活用户。 将用于浏览网站的最终用户计算机的公共 IP 地址也需要在 Symantec Web Security Service (WSS) 门户中进行预配。

> [!NOTE]
> 请[单击此处](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)，获取计算机的公共 IP 地址。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Symantec Web Security Service (WSS) 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 分配到 Symantec Web Security Service (WSS)，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Symantec Web Security Service (WSS)”。

    ![应用程序列表中的 Symantec Web Security Service (WSS) 链接](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

由于已配置了 WSS 帐户以使用 Azure AD 进行 SAML 身份验证，在本部分中，将测试单一登录功能。

将 Web 浏览器配置为代理流向 WSS 的流量后，打开 Web 浏览器并尝试浏览到某个站点时，系统会将你重定向到 Azure 登录页面。 输入在 Azure AD 中预配的测试最终用户（即 BrittaSimon）的凭据和相关密码。 身份验证后，你将能浏览所选网站。 如果在 WSS 端创建了一条策略规则，阻止 BrittaSimon 浏览特定站点，那么尝试以 BrittaSimon 用户身份浏览该站点时，会看到 WSS 阻止页。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

