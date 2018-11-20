---
title: 教程：Azure Active Directory 与 SuccessFactors 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SuccessFactors 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 89224b32efaecdf7a2797b034b1beac7ad191ee5
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685219"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>教程：Azure Active Directory 与 SuccessFactors 集成

在本教程中，了解如何将 SuccessFactors 与 Azure Active Directory (Azure AD) 集成。

将 SuccessFactors 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 SuccessFactors。
- 可以让用户使用其 Azure AD 帐户自动登录到 SuccessFactors（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SuccessFactors 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 SuccessFactors 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 SuccessFactors
2. 配置和测试 Azure AD 单一登录

## <a name="adding-successfactors-from-the-gallery"></a>从库添加 SuccessFactors

要配置 SuccessFactors 与 Azure AD 的集成，需要从库中将 SuccessFactors 添加到托管 SaaS 应用列表。

**若要从库中添加 SuccessFactors，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“SuccessFactors”，在结果面板中选择“SuccessFactors”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 SuccessFactors](./media/successfactors-tutorial/tutorial_successfactors_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SuccessFactors 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SuccessFactors 用户。 换句话说，需要建立 Azure AD 用户与 SuccessFactors 中相关用户之间的链接关系。

若要配置和测试 SuccessFactors 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SuccessFactors 测试用户](#creating-a-successfactors-test-user)** - 在 SuccessFactors 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录，并在 SuccessFactors 应用程序中配置单一登录。

**若要配置 SuccessFactors 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 SuccessFactors 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![SuccessFactors 域和 URL 单一登录信息](./media/successfactors-tutorial/tutorial_successfactors_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. 在“标识符”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|
         
    > [!NOTE] 
    > 这些不是实际值。 使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [SuccessFactors 客户端支持团队](https://www.successfactors.com/support.html)获取这些值。 

5. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/successfactors-tutorial/tutorial_successfactors_certificate.png) 

6. 在“设置 SuccessFactors”部分中，根据要求复制相应的 URL。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![SuccessFactors 配置](common/configuresection.png)

7. 在另一个 Web 浏览器窗口中，以管理员身份登录 **SuccessFactors 管理门户**。
    
8. 请访问“应用程序安全性”并从本机转到“单一登录功能”。 

9. 将任何值放在“重置令牌”中，然后单击“保存令牌”以启用 SAML SSO。
   
    ![在应用端配置单一登录][11]

    > [!NOTE] 
    > 此值将用作打开/关闭开关。 如果保存了任何值，则 SAML SSO 为打开。 如果保存为空值，则 SAML SSO 为关闭。

10. 从本机转到以下屏幕快照并执行以下操作：
   
    ![在应用端配置单一登录][12]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“SAML v2 SSO”单选按钮
   
    b. 设置“SAML 声明方名称”（例如，SAML 颁发者+公司名称）。
   
    c. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。
   
    d. 对于“需要强制签名”，选择“断言”。
   
    e. 对于“启用 SAML 标志”选择“启用”。
   
    f. 对于“登录请求签名（SF 生成/SP/RP）”选择“否”。
   
    g. 对于“SAML 配置文件”，选择“浏览器/Post 配置文件”。
   
    h. 对于“强制证书有效期限”，选择“否”。
   
    i. 复制从 Azure 门户下载的证书文件的内容，然后将其粘贴到“SAML 验证证书”文本框。

    > [!NOTE] 
    > 证书内容必须具有开始证书和结束证书标记。

11. 导航到 SAML V2，并执行以下步骤：
   
    ![在应用端配置单一登录][13]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 对于“支持 SP 启动的全局注销”，选择“是”。
   
    b. 在“全局注销服务URL (LogoutRequest 目标)”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。
   
    c. 对于“要求 sp 必须加密所有 NameID 元素”选择“否”。
   
    d. 对于“NameID 格式”，选择“未指定”。
   
    e. 对于“启用 sp 启动的登录 (AuthnRequest)”选择“是”。
   
    f. 在“以公司范围颁发者身份发送请求”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

12. 如果希望登录用户名不区分大小写，请执行这些步骤。
   
    ![配置单一登录][29]
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 请访问“公司设置”（靠近底部）。
   
    b. 选中“启用不区分大小写的用户名”旁边的复选框。
   
    c.单击“保存”。
   
    > [!NOTE] 
    > 如果尝试启用此功能，系统会检查是否会创建重复的 SAML 登录名。 例如，如果客户具有用户名 User1 和 user1。 取消区分大小写会认定它们重复。 系统将提供一条错误消息，且不会启用该功能。 客户需要更改其中一个用户名，以使其拼写不同。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-a-successfactors-test-user"></a>创建 SuccessFactors 测试用户

要使 Azure AD 用户能够登录 SuccessFactors，必须将这些用户预配到 SuccessFactors 中。  
对于 SuccessFactors，预配任务需要手动完成。

若要在 SuccessFactors 中创建用户，需要联系 [SuccessFactors 支持团队](https://www.successfactors.com/support.html)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SuccessFactors 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“SuccessFactors”。

    ![配置单一登录](./media/successfactors-tutorial/tutorial_successfactors_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“SuccessFactors”磁贴时，应会自动登录 SuccessFactors 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
