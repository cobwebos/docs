---
title: "教程：Azure Active Directory 与 Sugar CRM 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Sugar CRM 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: c27aef24e859522b8001ecb747906abdca14d87a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>教程：Azure Active Directory 与 Sugar CRM 的集成

本教程介绍了如何将 Sugar CRM 与 Azure Active Directory (Azure AD) 进行集成。

将 Sugar CRM 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Sugar CRM
- 可以让用户使用其 Azure AD 帐户自动登录到 Sugar CRM（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sugar CRM 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了单一登录的 Sugar CRM 订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Sugar CRM
2. 配置和测试 Azure AD 单一登录

## <a name="adding-sugar-crm-from-the-gallery"></a>从库中添加 Sugar CRM
若要配置 Sugar CRM 与 Azure AD 的集成，需要从库中将 Sugar CRM 添加到托管 SaaS 应用列表。

**若要从库中添加 Sugar CRM，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Sugar CRM”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_search.png)

5. 在结果窗格中，选择“Sugar CRM”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Sugar CRM 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Sugar CRM 用户。 换句话说，需要在 Azure AD 用户与 Sugar CRM 中的相关用户之间建立链接关系。

在 Sugar CRM 中，通过将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 Sugar CRM 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Sugar CRM 测试用户](#creating-a-sugar-crm-test-user)** -若要链接到用户的 Azure AD 表示的 Sugar CRM 中具有 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Sugar CRM 应用程序中配置单一登录。

**若要配置 Sugar CRM 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Sugar CRM** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

3. 在“Sugar CRM 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Sugar CRM 客户端支持团队](https://support.sugarcrm.com/)来获取此值。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_400.png)

6. 在“Sugar CRM 配置”部分中，单击“配置 Sugar CRM”以打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Sugar CRM 公司站点。

8. 转到“管理员”。
   
    ![管理员](./media/active-directory-saas-sugarcrm-tutorial/ic795888.png "管理员")

9. 在“管理”部分中，单击“密码管理”。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/ic795889.png "Administration")

10. 选择“启用 SAML 身份验证”。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/ic795890.png "Administration")

11. 在“SAML 身份验证”部分中，执行以下步骤：
   
    ![SAML 身份验证](./media/active-directory-saas-sugarcrm-tutorial/ic795891.png "SAML 身份验证")  
 
    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
  
    b. 在“SLO URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。
  
    c. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，并将整个证书粘贴到“X.509 证书”文本框中。
  
    d. 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sugarcrm-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-sugar-crm-test-user"></a>创建 Sugar CRM 测试用户

要使 Azure AD 用户能够登录到 Sugar CRM，必须将其预配到 Sugar CRM 中。

对于 Sugar CRM，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **Sugar CRM** 公司站点。

2. 转到“管理员”。
   
    ![管理员](./media/active-directory-saas-sugarcrm-tutorial/ic795888.png "管理员")

3. 在“管理”部分中，单击“用户管理”。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/ic795893.png "Administration")

4. 转到“用户”\>“创建新用户”。
   
    ![创建新用户](./media/active-directory-saas-sugarcrm-tutorial/ic795894.png "Create New User")

5. 在“用户配置文件”选项卡上，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-sugarcrm-tutorial/ic795895.png "New User")

    a. 在相关文本框中键入有效 Azure Active Directory 用户的用户名、姓氏和电子邮件地址。
  
6. 对于“状态”，选择“活动”。

7. 在“密码”选项卡中，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-sugarcrm-tutorial/ic795896.png "New User")

    a. 在相关文本框中键入密码。

    b. 单击“保存” 。

>[!NOTE]
>可以使用 Sugar CRM 提供的任何其他 Sugar CRM 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Sugar CRM 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Sugar CRM，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Sugar CRM”。

    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

当在访问面板中单击 Sugar CRM 磁贴时，应当会自动登录到 Sugar CRM 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sugarcrm-tutorial/tutorial_general_203.png

