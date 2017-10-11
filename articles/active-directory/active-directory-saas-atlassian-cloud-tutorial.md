---
title: "教程：Azure Active Directory 与 Atlassian Cloud 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Atlassian Cloud 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 2891838b56dd15cb5f97dcae391770143a80c781
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>教程：Azure Active Directory 与 Atlassian Cloud 的集成

本教程介绍如何将 Atlassian Cloud 与 Azure Active Directory (Azure AD) 集成。

将 Atlassian Cloud 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Atlassian Cloud
- 可以让用户使用其 Azure AD 帐户自动登录到 Atlassian Cloud（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Atlassian Cloud 的集成，需要具有以下项：

- 一个 Azure AD 订阅
- 已启用 Atlassian Cloud 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Atlassian Cloud
2. 配置和测试 Azure AD 单一登录

## <a name="adding-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud
要配置 Atlassian Cloud 与 Azure AD 的集成，需要从库中将 Atlassian Cloud 添加到托管 SaaS 应用列表。

**若要从库中添加 Atlassian Cloud，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Atlassian Cloud**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_search.png)

5. 在结果面板中选择“Atlassian Cloud”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置并测试 Atlassian Cloud 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Atlassian Cloud 用户。 换句话说，需要在 Azure AD 用户与 Atlassian Cloud 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Atlassian Cloud 中“用户名”的值来建立此链接关系。

若要配置并测试 Atlassian Cloud 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建一个 Atlassian 云的测试用户](#creating-an-atlassian-cloud-test-user)** -若要具有 Atlassian 云中的链接到用户的 Azure AD 表示 Britta 人 Simon 对应项。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Atlassian Cloud 应用程序中配置单一登录。

**若要配置 Atlassian Cloud 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Atlassian Cloud”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. 在“Atlassian Cloud 域和 URL”部分中，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<instancename>.atlassian.net/admin/saml/edit`

    b. 在“回复 URL”文本框中键入 URL：`https://id.atlassian.com/login/saml/acs`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<instancename>.atlassian.net`

    > [!NOTE] 
    > 这些不是实际值。 使用实际标识符和登录 URL 更新这些值。 可从 Atlassian Cloud SAML 配置屏幕中获取确切值。
 
5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. 在“Atlassian Cloud 配置”部分中，单击“配置 Atlassian Cloud”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

7. 若要为应用程序配置 SSO，请使用管理员权限登录到 Atlassian 门户。

8. 在左侧导航窗格的“身份验证”部分中单击“域”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

    a. 在文本框中键入域名，并单击“添加域”。
        
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)

    b. 若要验证该域，请单击“验证”。 

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png)

    c. 下载域验证 html 文件，将其上传到域网站的根文件夹，然后单击“验证域”。
    
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)

    d. 验证域后，“状态”字段的值变为“已验证”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

9. 在左侧导航栏中，单击“SAML”。
 
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

10. 创建 SAML 配置并添加标识提供者配置。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. 在“标识提供者实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    b. 在“标识提供者 SSO URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 在 Azure 门户中打开下载的证书并复制其中包含的值（不要复制 Begin 和 End 行），然后将其粘贴到“公共 X509 证书”框。
    
    d. 单击“保存配置”以保存设置。
     
11. 更新 Azure AD 设置，确保设置正确的标识符 URL。
  
    a. 从 SAML 屏幕复制“SP 标识 ID”并将其作为“标识符”值粘贴到 Azure AD。

    b. “登录 URL”是 Atlassian Cloud 的租户 URL。   

     ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
12. 在 Azure 门户中单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> 现在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-atlassian-cloud-test-user"></a>创建 Atlassian Cloud 测试用户

为使 Azure AD 用户能够登录到 Atlassian Cloud，必须将其预配到 Atlassian Cloud。  
就 Atlassian Cloud 而言，预配需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 在“站点管理”部分单击“用户”按钮

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. 单击“创建用户”按钮在 Atlassian Cloud 中创建用户

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. 输入该用户的电子邮件地址、用户名和全名，并分配应用程序访问权限。 

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. 单击“创建用户”按钮，随即会向该用户发送电子邮件邀请，接受邀请后，该用户会在系统中激活。 

>[!NOTE] 
>还可通过单击“用户”部分中的“批量创建”按钮创建批量用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Atlassian Cloud 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Atlassian Cloud，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Atlassian Cloud”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD SSO 配置。

当在访问面板中单击 Atlassian Cloud 磁贴时，应当会自动登录到 Atlassian Cloud 应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

