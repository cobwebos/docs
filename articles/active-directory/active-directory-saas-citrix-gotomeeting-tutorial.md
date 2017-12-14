---
title: "教程：Azure Active Directory 与 GoToMeeting 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 GoToMeeting 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: 1c3ea5175b02e35e7c624ce936d59fd82163b0fc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>教程：Azure Active Directory 与 GoToMeeting 的集成

本教程介绍如何将 GoToMeeting 与 Azure Active Directory (Azure AD) 集成。

将 GoToMeeting 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 GoToMeeting
- 可让用户使用其 Azure AD 帐户自动登录到 GoToMeeting（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 GoToMeeting 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 GoToMeeting 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 GoToMeeting
2. 配置并测试 Azure AD 单一登录

## <a name="adding-gotomeeting-from-the-gallery"></a>从库添加 GoToMeeting
若要配置 GoToMeeting 与 Azure AD 的集成，需从库中将 GoToMeeting 添加到托管 SaaS 应用列表。

**若要从库中添加 GoToMeeting，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“GoToMeeting”，在结果面板中选择“GoToMeeting”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 GoToMeeting](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 GoToMeeting 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 GoToMeeting 中与 Azure AD 用户对应的用户。 换句话说，需要建立 Azure AD 用户与 GoToMeeting 中相关用户之间的关联。

通过将 Azure AD 中“用户名”的值指定为 GoToMeeting 中“用户名”的值来建立此链接关系。

若要配置并测试 GoToMeeting 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 GoToMeeting 测试用户](#create-a-gotomeeting-test-user)** - 在 GoToMeeting 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 GoToMeeting 应用程序中配置单一登录。

**若要配置 GoToMeeting 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“GoToMeeting”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. 在“GoToMeeting 域和 URL”部分中，单击“显示高级 URL 设置”并执行以下操作：

    ![GoToMeeting 域和 URL 单一登录信息](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_ssourl.png)

    a. 在“标识符”文本框中，键入 URL：`https://authentication.logmeininc.com/saml/sp`

    b. 在“回复 URL”文本框中，键入 URL：`https://authentication.logmeininc.com/saml/acs`

    c. 在“中继状态”文本框中，键入以下 URL 之一：

    **对于 GoToMeeting**：`https://global.gotomeeting.com`
    
    **对于 GoToTraining**：`https://global.gototraining.com`

    **对于 GoToWebinar**：`https://global.gotowebinar.com`

    **对于 GoToAssist**：`https://app.gotoassist.com`


4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. 若要生成**元数据** URL，请执行以下步骤：

    a. 单击“应用注册”。
    
    ![配置单一登录](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appregistrations.png)
   
    b. 单击“终结点”以打开“终结点”对话框。  
    
    ![配置单一登录](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpointicon.png)

    c. 单击复制按钮以复制**联合元数据文档** URL 并将其粘贴到记事本。
    
    ![配置单一登录](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpoint.png)
     
    d.单击“下一步”。 现在，转到 **GoToMeeting** 的属性页，使用“复制”按钮复制“应用程序 ID”并将其粘贴到记事本中。
 
    ![配置单一登录](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appid.png)

    e. 使用以下模式生成**元数据 URL**：`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`   

7. 在“GoToMeeting 配置”部分中，单击“配置 GoToMeeting”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![GoToMeeting 配置](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_configure.png) 

8. 在其他浏览器窗口中，登录到 [GoToMeeting 组织中心](https://account.citrixonline.com/organization/administration/)

9. 在“标识提供者”选项卡下，可以通过提供生成的**元数据 URL** 或下载的**元数据文件**或以**手动**方式配置 Azure 设置。

10. 若要使用**元数据 URL**，请执行以下步骤：

    ![GoToMeeting 配置](./media/active-directory-saas-gotomeeting-tutorial/config1.png)

    a. 在“想要如何配置 SAML IDP?”中，从下拉列表中选择“自动”。

    b. 在“元数据 URL”文本框中粘贴在前面步骤中生成的**元数据 URL**。

    c. 单击“保存” 。

11. 若要使用**元数据文件**，请执行以下步骤：

    ![GoToMeeting 配置](./media/active-directory-saas-gotomeeting-tutorial/config2.png)

    a. 在“想要如何配置 SAML IDP?”中，从下拉列表中选择“上传 SAML 元数据文件”。

    b. 若要上传已下载的元数据文件，请单击“上传元数据文件”。

    c. 单击“保存” 。

12. 若要**手动**配置，请执行以下步骤：

    ![GoToMeeting 配置](./media/active-directory-saas-gotomeeting-tutorial/config3.png)

    a.  在“登录页 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    b.  在“注销页 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    c.  在“标识提供者实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    d.单击“下一步”。 从下载的元数据文件中提取 X509 证书，并单击“上传证书”上传此证书。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-gotomeeting-test-user"></a>创建 GoToMeeting 测试用户

在本部分，我们会在 GoToMeeting 中创建一个名为 Britta Simon 的用户。 GoToMeeting 支持默认启用的实时预配。

此部分不存在任何操作项。 尝试访问 GoToMeeting 时，如果 GoToMeeting 中尚不存在用户，系统将创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，请联系 [GoToMeeting 支持团队](https://support.logmeininc.com/gotomeeting)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 GoToMeeting 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配给 GoToMeeting，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“GoToMeeting”。

    ![应用程序列表中的 GoToMeeting 链接](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 GoToMeeting 磁贴时，应会自动登录到 GoToMeeting 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置用户预配](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

