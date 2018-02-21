---
title: "教程：Azure Active Directory 与 BambooHR 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 BambooHR 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 081144a645683d4d00ed0d464e23558378dc1b38
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>教程：Azure Active Directory 与 BambooHR 集成

在本教程中，了解如何将 BambooHR 与 Azure Active Directory (Azure AD) 进行集成。

将 BambooHR 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权限访问 BambooHR。
- 可以让用户使用其 Azure AD 帐户以单一登录 (SSO) 方式自动登录到 BambooHR。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 BambooHR 的集成，需要以下项：

- Azure AD 订阅
- 启用了 SSO 的 BambooHR 订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取免费的一个月试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 

本教程概述的方案包括两个主要构建基块：

1. 从库中添加 BambooHR
2. 配置和测试 Azure AD 单一登录

## <a name="add-bamboohr-from-the-gallery"></a>从库中添加 BambooHR
若要配置 BambooHR 与 Azure AD 的集成，请执行以下操作，从库中将 BambooHR 添加到托管 SaaS 应用列表：

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”窗格][2]
    
3. 若要添加应用程序，请选择“新建应用程序”。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入 BambooHR。 在结果列表中，选择“BambooHR”，然后选择“添加”。

    ![结果列表中的 BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将使用名为“Britta Simon”的测试用户配置和测试 BambooHR 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道 BambooHR 中的对应用户。 换句话说，必须在 Azure AD 用户与 BambooHR 中的相关用户之间建立链接关系。

若要在 BambooHR 中建立链接关系，请将 Azure AD“用户名”值指定为 BambooHR“用户名”值。

若要配置和测试 BambooHR 的 Azure AD 单一登录，请完成下面 5 个部分中的构建基块。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，我们将执行以下操作，在 Azure 门户中启用 Azure AD SSO，并在 BambooHR 应用程序中配置 SSO：

1. 在 Azure 门户中，在 **BambooHR** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”窗口中，在“模式”下拉列表中，选择“基于 SAML 的登录”。
 
    ![“单一登录”窗口](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. 在“BambooHR 域和 URL”下，执行以下操作：

    ![“BambooHR 域和 URL”部分](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    a. 在“登录 URL”框中，键入采用以下格式的 URL：`https://<company>.bamboohr.com`。

    b. 在“标识符”框中，键入一个值：`BambooHR-SAML`。

    > [!NOTE] 
    > “登录 URL”值不是实际值。 请将其更新为实际的登录 URL。 若要获取此值，请与 [BambooHR 客户端支持团队](https://www.bamboohr.com/contact.php)联系。 
 
4. 在“SAML 签名证书”下，选择“证书(Base64)”，然后将证书文件保存到计算机。

    ![证书下载链接](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. 选择“保存”。

    ![点击“保存”按钮](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. 在“BambooHR 配置”下，选择“配置 BambooHR”以打开“配置登录”窗口。 在“快速参考”部分中，复制“SAML 单一登录服务 URL”供稍后使用。

    ![BambooHR 配置](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

7. 在一个新窗口中，以管理员身份登录到 BambooHR 公司站点。

8. 在主页上执行以下操作：
   
    ![BambooHR 单一登录页面](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "单一登录")   

    a. 选择“应用”。
   
    b. 在“应用”窗格中，选择“单一登录”。
   
    c. 选择“SAML 单一登录”。

9. 在“SAML 单一登录”窗格中，执行以下操作：
   
    ![“SAML 单一登录”窗格](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML 单一登录")
   
    a. 在“SSO 登录 URL”框中，粘贴在步骤 6 中从 Azure 门户复制的“SAML 单一登录服务 URL”。
      
    b. 在记事本中，打开从 Azure 门户下载的 base-64 编码证书，复制其内容，然后将其粘贴到“X.509 证书”框中。
   
    c. 选择“保存”。

> [!TIP]
> 设置应用时，可在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本。 从“Active Directory” > “企业应用程序”部分中添加该应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 有关信息，请参阅 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户 Britta Simon][100]

若要在 Azure AD 中创建测试用户，请执行以下步骤：

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png)

3. 在“所有用户”窗格的顶部，选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png)

4. 在“用户”窗口中执行以下操作：

    ![“用户”窗口](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 选择“创建”。
 
### <a name="create-a-bamboohr-test-user"></a>创建 BambooHR 测试用户

若要使 Azure AD 用户能够登录到 BambooHR，请执行以下操作，在 BambooHR 中手动设置它们：

1. 以管理员身份登录到 **BambooHR** 站点。

2. 在顶部的工具栏中，选择“设置”。
   
    ![“设置”按钮](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "设置")

3. 选择“概述”。

4. 在左窗格中，选择“安全” > “用户”。

5. 键入要设置的有效 Azure AD 帐户的用户名、密码和电子邮件地址。

6. 选择“保存”。
        
>[!NOTE]
>若要设置 Azure AD 用户帐户，还可以使用 BambooHR 用户帐户创建工具或 API。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

通过向用户 Britta Simon 授予对 BambooHR 的访问权限，使其能够使用 Azure SSO。

![分配用户角色][200] 

若要将用户 Britta Simon 分配到 BambooHR，请执行以下操作：

1. 在 Azure 门户中，打开“应用程序”视图，转到“目录”视图，然后选择“企业应用程序” > “所有应用程序”。

    ![分配用户][201] 

2. 在“企业应用程序”列表中，选择“BambooHR”。

    ![“企业应用程序”列表中的 BambooHR 链接](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png)  

3. 在左窗格中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”按钮，并在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”窗口中的“用户”列表内，选择“Britta Simon”。

6. 选择“选择”按钮。

7. 在“添加分配”窗口中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

使用访问面板测试 Azure AD SSO 配置。

在访问面板中单击 **BambooHR** 磁贴时，应该会自动登录到 BambooHR 应用程序。

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

