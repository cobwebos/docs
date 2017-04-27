---
title: "教程：Azure Active Directory 与 EasyTerritory 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 EasyTerritory 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d29b362d-e986-4f67-8ff2-e158e49353aa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 64131f85cceb463bdd91cec40c2f272ece773691
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-easyterritory"></a>教程：Azure Active Directory 与 EasyTerritory 的集成

本教程介绍了如何将 EasyTerritory 与 Azure Active Directory (Azure AD) 集成。

将 EasyTerritory 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 EasyTerritory
- 可以让用户使用其 Azure AD 帐户自动登录到 EasyTerritory 单一登录 (SSO)
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 EasyTerritory 的集成，需要做好以下准备：

- Azure AD 订阅
- 启用了 EasyTerritory 单一登录 (SSO) 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。
>
>

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以获取[一个月](https://azure.microsoft.com/pricing/free-trial/)的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD SSO。 

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 EasyTerritory
2. 配置和测试 Azure AD SSO

## <a name="adding-easyterritory-from-the-gallery"></a>从库中添加 EasyTerritory
若要配置 EasyTerritory 与 Azure AD 的集成，需要从库中将 EasyTerritory 添加到托管 SaaS 应用列表。

**若要从库中添加 EasyTerritory，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **EasyTerritory**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_001.png)

5. 在结果窗格中，选择“EasyTerritory”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 EasyTerritory 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 EasyTerritory 用户。 换句话说，需要建立 Azure AD 用户与 EasyTerritory 中相关用户之间的链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 EasyTerritory 中“用户名”的值来建立此链接关系。

若要配置和测试 EasyTerritory 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 EasyTerritory 测试用户](#creating-an-easyterritory-test-user)** - 在 EasyTerritory 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 管理门户中启用 Azure AD 单一登录并在 EasyTerritory 应用程序中配置 SSO。

**若要配置 EasyTerritory 的 Azure AD SSO，请执行以下步骤：**

1. 在 Azure 管理门户的“EasyTerritory”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用 SSO。
 
    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_01.png)

3. 在“EasyTerritory 域和 URL”部分，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_02.png)
   1. 在“标识符”文本框中，使用以下模式键入 URL：`https://apps.easyterritory.com/<tenant id>/DEV/`
   2. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://apps.easyterritory.com/<tenant id>/DEV/AuthServices/Acs`
    
4. 在“EasyTerritory 域和 URL”部分，如果要在“SP 发起的模式”下配置应用程序，请执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_03.png)
  1. 单击“显示高级 URL 设置”选项。
  2. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.easyterritory.com/`

    >[!NOTE] 
    >这些不是实际值。 必须使用实际登录 URL、标识符和回复 URL 更新这些值。请联系 [EasyTerritory 支持团队](mailto:sales@easyterritory.com)获取这些值。

5. 在“SAML 签名证书”部分中，单击“创建新证书”。

    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_04.png)     

6. 在“创建新证书”对话框中，单击日历图标，然后选择“到期日期”。 然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_300.png)

7. 在“SAML 签名证书”部分中，选择“激活新证书”，然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_05.png)

8. 在弹出的“滚动更新证书”窗口中，单击“确定”。

    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_general_400.png)

9. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_06.png) 

10. 若要为应用程序配置 SSO，请联系 [EasyTerritory 支持团队](mailto:sales@easyterritory.com)，并向他们提供下载的**元数据**。 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-easyterritory-tutorial/create_aaduser_04.png) 
 1. 在“名称”文本框中，键入 **BrittaSimon**。
 2. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。
 3. 选择“显示密码”并记下“密码”的值。
 4. 单击“创建”。 

### <a name="create-an-easyterritory-test-user"></a>创建 EasyTerritory 测试用户

本部分中的步骤在 EasyTerritory 中创建一个名为 Britta Simon 的用户。 请配合 [EasyTerritory 支持团队协作](mailto:sales@easyterritory.com)在 EasyTerritory 平台中添加用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 EasyTerritory 的权限，允许她使用 Azure SSO。

![分配用户][200] 

**若要将 Britta Simon 分配到 EasyTerritory，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“EasyTerritory”。

    ![配置单一登录](./media/active-directory-saas-easyterritory-tutorial/tutorial_easyterritory_50.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD SSO 配置。

当在访问面板中单击 EasyTerritory 磁贴时，应当会自动登录到 EasyTerritory 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-easyterritory-tutorial/tutorial_general_203.png
