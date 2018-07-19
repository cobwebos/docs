---
title: 教程：Azure Active Directory 与 Questetra BPM Suite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Questetra BPM Suite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 57c418f686aa956294efeabd831042441c055e56
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041103"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>教程：Azure Active Directory 与 Questetra BPM Suite 集成

在本教程中，了解如何将 Questetra BPM Suite 与 Azure Active Directory (Azure AD) 集成。

将 Questetra BPM Suite 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Questetra BPM Suite
- 可以让用户使用其 Azure AD 帐户自动登录 Questetra BPM Suite（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Questetra BPM Suite 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Questetra BPM Suite 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Questetra BPM Suite
2. 配置和测试 Azure AD 单一登录

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>从库中添加 Questetra BPM Suite
要配置 Questetra BPM Suite 与 Azure AD 的集成，需要从库中将 Questetra BPM Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 Questetra BPM Suite，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Questetra BPM Suite”，在结果面板中选择“Questetra BPM Suite”，然后单击“添加”按钮添加该应用程序。

    ![从库添加](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Questetra BPM Suite 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Questetra BPM Suite 用户。 换句话说，需要在 Azure AD 用户与 Questetra BPM Suite 中相关用户之间建立链接关系。

在 Questetra BPM Suite 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 Questetra BPM Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Questetra BPM Suite 测试用户](#create-a-questetra-bpm-suite-test-user)** - 在 Questetra BPM Suite 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Questetra BPM Suite 应用程序中配置单一登录。

**若要配置 Questetra BPM Suite 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Questetra BPM Suite”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![基于 SAML 的登录](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. 在“Questetra BPM Suite 域和 URL”部分中，执行以下步骤：

    ![Questetra BPM Suite 域和 URL 部分](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 可以从 **Questetra BPM Suite** 公司站点的“SP 信息”部分获取这些值（在本教程中的后面部分进行说明），或者联系 [Questetra BPM Suite 客户端支持团队](https://www.questetra.com/contact/)来获取。 
 
4. 在“SAML 签名证书”部分中，单击“证书 (Base64)”，然后在计算机上保存证书文件。

    ![“SAML 签名证书”部分](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. 单击“保存”按钮。

    ![保存按钮](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. 在“Questetra BPM Suite 配置”部分中，单击“配置 Questetra BPM Suite”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![“Questetra BPM Suite 配置”部分](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. 在其他 Web 浏览器窗口中，以管理员身份登录 **Questetra BPM Suite** 公司站点。

8. 在顶部菜单中，单击“系统设置”。 
   
    ![Azure AD 单一登录][10]

9. 若要打开“SingleSignOnSAML”页，请单击“SSO (SAML)”。 
   
    ![Azure AD 单一登录][11]

10. 在 **Questetra BPM Suite** 公司站点的“SP 信息”部分中，执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 复制 **ACS URL**，然后将其粘贴到 Azure 门户的“Questetra BPM Suite 域和 URL”部分的“登录 URL”文本框中。
    
    b. 复制“实体 ID”，然后将其粘贴到 Azure 门户的“Questetra BPM Suite 域和 URL”部分的“标识符”文本框中。

11. 在 **Questetra BPM Suite** 公司站点上，执行以下步骤： 
   
    ![配置单一登录][15]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“启用单一登录”。
   
    b. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。
    
    c. 在“登录页 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
    
    d. 在“注销页 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。
    
    e. 在“NameID 格式”文本框中，键入 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。

    f. 在记事本中打开从 Azure 门户下载的 Base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“验证证书”文本框中。 

    g. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>创建 Questetra BPM Suite 测试用户

本部分的目的是在 Questetra BPM Suite 中创建名为“Britta Simon”的用户。

**若要在 Questetra BPM Suite 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录 Questetra BPM Suite 公司站点。
2. 转到“系统设置”>“用户列表”>“新建用户”。 
3. 在“新建用户”对话框中，执行以下步骤： 
   
    ![创建测试用户][300] 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”文本框中，键入用户的**名称** **britta.simon@contoso.com**。
   
    b. 在“电子邮件”文本框中，键入用户的电子邮件地址**britta.simon@contoso.com**。
   
    c. 在“密码”文本框中，键入用户的密码。
    
    d. 单击“添加新用户”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Questetra BPM Suite 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Questetra BPM Suite，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Questetra BPM Suite”。

    ![应用列表中的 Questetra BPM Suite](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Questetra BPM Suite”磁贴，即可自动登录 Questetra BPM Suite 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

