---
title: "教程：Azure Active Directory 与 Cezanne HR Software 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Cezanne HR Software 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8abe4b089401c8f086a77be2aa6812b4fa7910ce
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>教程：Azure Active Directory 与 Cezanne HR Software 的集成

在本教程中，了解如何将 Cezanne HR Software 与 Azure Active Directory (Azure AD) 集成。

将 Cezanne HR Software 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Cezanne HR Software
- 可以让用户使用其 Azure AD 帐户自动登录到 Cezanne HR Software 单一登录 (SSO)
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Cezanne HR Software 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Cezanne HR Software SSO 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。
>
>

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Cezanne HR Software
2. 配置和测试 Azure AD SSO


## <a name="add-cezanne-hr-software-from-the-gallery"></a>从库中添加 Cezanne HR Software
若要配置 Cezanne HR Software 与 Azure AD 的集成，需要从库中将 Cezanne HR Software 添加到托管 SaaS 应用列表。

**若要从库中添加 Cezanne HR Software，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Cezanne HR Software”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_00001.png)

5. 在结果面板中，选择“Cezanne HR Software”，然后单击“添加”按钮以添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Cezanne HR Software 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Cezanne HR Software 用户。 换句话说，需要在 Azure AD 用户与 Cezanne HR Software 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Cezanne HR Software 中“用户名”的值来建立此链接关系。

若要配置和测试 Cezanne HR Software 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Cezanne HR Software 测试用户](#creating-a-cezanne-hr-software-test-user)** - 在 Cezanne HR Software 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 管理门户中启用 Azure AD SSO 并在 Cezanne HR Software 应用程序中配置单一登录。

**若要配置 Cezanne HR Software的 Azure AD SSO，请执行以下步骤：**

1. 在 Azure 管理门户的“Cezanne HR Software”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框页中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

3. 在“Cezanne HR Software 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_02.png)
  1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`
  2. 在“标识符”文本框中，键入：`https://w3.cezanneondemand.com/CezanneOnDemand/`

     >[!NOTE] 
     > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 此处我们建议在“标识符”中使用 URL 的唯一值。 若要获取这些值，请联系 [Cezanne HR Software 支持团队](mailto:info@cezannehr.com)。

4. 在“SAML 签名证书”部分中，单击“创建新证书”。

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)     

5. 在“创建新证书”对话框中，单击日历图标，然后选择“到期日期”。 然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_300.png)

6. 在“SAML 签名证书”部分中，选择“激活新证书”，然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

7. 在弹出的“滚动更新证书”窗口中，单击“确定”。

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

8. 在“SAML 签名证书”部分中，单击“证书(base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png) 

9. 在“Cezanne HR Software 配置”部分中，单击“配置 Cezanne HR Software”打开“配置登录”窗口。

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_06.png) 

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_07.png)

10. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Cezanne HR Software 租户。

11. 在左侧导航窗格中，单击“系统设置”。 转到“安全设置”。 然后导航到“单一登录配置”。

    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

12. 在“允许用户使用以下单一登录 (SSO) 服务进行登录”面板上，选中“SAML 2.0”框并选择“高级配置”选项。

    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

13. 单击“新增”按钮。

    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

14. 在“SAML 2.0 标识提供者”部分中执行以下步骤。

    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
 1. 对于“显示名称”，输入标识提供者的名称。
 2. 在“实体标识符”文本框中，放置 Azure AD 应用程序配置窗口中的“SAML 实体 ID”值。
 3. 将“SAML 绑定”更改为“POST”。
 4. 在“安全令牌服务终结点”文本框中，放置 Azure AD 应用程序配置窗口中的“SAML 单一登录服务 URL”值。
 5. 在“用户 ID 属性名称”文本框中输入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”。
 6. 单击“上载”图标，上载从 Azure AD 下载的证书。
 7. 单击“确定”按钮。 

15. 单击“保存”按钮。

    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 
 1. 在“名称”文本框中，键入 **BrittaSimon**。
 2. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。
 3. 选择“显示密码”并记下“密码”的值。
 4. 单击“创建” 。 

### <a name="create-a-cezanne-hr-software-test-user"></a>创建 Cezanne HR Software 测试用户

为了使 Azure AD 用户能够登录到 Cezanne HR Software，必须将其预配到 Cezanne HR Software 中。 对于 Cezanne HR Software，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1.  以管理员身份登录到你的 Cezanne HR Software 公司站点。

2.  在左侧导航窗格中，单击“系统设置”。 转到“管理用户”。 然后导航到“添加新用户”。

    ![新建用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "New User")

3.  在“人员详细信息”部分中，执行以下步骤︰

    ![新建用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "New User")
 1. 将“内部用户”设置为“关闭”。
 2. 在“名字”文本框中，键入“Britta”。  
 3. 在“姓氏”文本框中，键入“Simon”。
 4. 在“电子邮件”文本框中，键入 Britta Simon 帐户的电子邮件地址。

4.  在“帐户信息”部分中，执行以下步骤：

    ![新建用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "New User")
 1. 在“用户名”文本框中，键入 Britta Simon 的电子邮件地址。
 2. 在“密码”文本框中，键入 Britta Simon 帐户的密码。
 3. 对于“安全角色”，选择“HR 专业人员”。
 4. 单击“确定”。

5. 导航到“单一登录”选项卡，并在“SAML 2.0 标识符”区域中选择“新增”。

    ![用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "用户")

6. 对于“标识提供者”，选择你的标识提供者，在“用户标识符”文本框中，输入 Britta Simon 帐户的电子邮件地址。

    ![用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "用户")
    
7. 单击“保存”按钮。

    ![用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "用户")

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Cezanne HR Software 的权限，允许她使用 Azure SSO。

![分配用户][200] 

**若要将 Britta Simon 分配到 Cezanne HR Software，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Cezanne HR Software”。

    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD SSO 配置。

当在访问面板中单击 Cezanne HR Software 磁贴时，应当会自动登录到 Cezanne HR Software 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
