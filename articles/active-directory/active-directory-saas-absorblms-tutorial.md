---
title: "教程：Azure Active Directory 与 Absorb LMS 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 Absorb LMS 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: df39b957e70be38331b3711a36ce847348049591
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>教程：Azure Active Directory 与 Absorb LMS 的集成

本教程介绍如何将 Absorb LMS 与 Azure Active Directory (Azure AD) 集成。

将 Absorb LMS 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Absorb LMS。
- 可以让用户使用其 Azure AD 帐户自动登录到 Absorb LMS（通过单一登录）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

如果想要深入了解软件型服务 (SaaS) 应用与 Azure AD 的集成，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Absorb LMS 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Absorb LMS 单一登录的订阅

> [!NOTE]
> 建议不要在本教程中使用生产环境。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

* 从库中添加 Absorb LMS
* 配置并测试 Azure AD 单一登录

## <a name="add-absorb-lms-from-the-gallery"></a>从库中添加 Absorb LMS
要配置 Absorb LMS 与 Azure AD 的集成，请将库中的 Absorb LMS 添加到托管的 SaaS 应用列表。

要从库中添加 Absorb LMS，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 转到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”窗格][2]
    
3. 要添加应用程序，请选择“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Absorb LMS”，在结果面板中选择“Absorb LMS”，然后选择“添加”按钮。

    ![结果列表中的 Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分基于名为 Britta Simon 的测试用户配置和测试 Absorb LMS 的 Azure AD 单一登录。

要运行单一登录，Azure AD 需要知道 Absorb LMS 用户对应的 Azure AD 用户。 换句话说，必须在 Azure AD 用户与 Absorb LMS 中的对应用户之间建立链接关系。

可以通过将 Azure AD 中的“用户名”值分配为 Absorb LMS 中的“用户名”值来建立此链接关系。

要配置和测试 Absorb LMS 的 Azure AD 单一登录，请完成下面 5 个部分中的构建基块。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 Absorb LMS 应用程序中配置单一登录。

要配置 Absorb LMS 的 Azure AD 单一登录，请执行以下操作：

1. 在 Azure 门户中的 Absorb LMS 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中的“模式”框内，选择“基于 SAML 的登录”，启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. 在“Absorb LMS 域和 URL”部分中，执行以下操作：

    ![Absorb LMS 域和 URL 单一登录信息](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. 在“标识符”框中，键入使用以下语法的 URL：`https://<subdomain>.myabsorb.com/Account/SAML`。

    b. 在“回复 URL”框中，键入使用以下语法的 URL：`https://<subdomain>.myabsorb.com/Account/SAML`。
     
    > [!NOTE] 
    > 这些 URL 不是实际值。 使用实际标识符和回复 URL 更新它们。 要获取这些值，请联系 [Absorb LMS 客户端支持团队](https://www.absorblms.com/support)。 

4. 在“SAML 签名证书”部分中的“下载”列中，选择“元数据 XML”，并将元数据文件保存到计算机上。

    ![签名证书下载链接](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. 在“Absorb LMS 配置”部分中，选择“配置 Absorb LMS”打开“配置登录”窗口，然后在“快速参考部分”中复制“注销 URL”。

    ![“Absorb LMS 配置”窗格](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. 在新的 Web 浏览器窗口中，以管理员身份登录 Absorb LMS 公司站点。

8. 选择右上角的“帐户”按钮。 

    ![“帐户”按钮](./media/active-directory-saas-absorblms-tutorial/1.png)

9. 在“帐户”窗格中，选择“门户设置”。

    ![“门户设置”链接](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. 选择“用户”选项卡。

    ![“用户”选项卡](./media/active-directory-saas-absorblms-tutorial/3.png)

11. 在“单一登录”配置页上，执行以下操作：

    ![“单一登录”配置页](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. 在“模式”框中，选择“发起的标识提供者”。

    b. 在记事本中，打开从 Azure 门户下载的证书。 删除 ---BEGIN CERTIFICATE--- 和 ---END CERTIFICATE--- 标记。 然后，在“密钥”框中，粘贴其余内容。
    
    c. 在“ID 属性”框中，选择在 Azure AD 中配置为用户标识符的属性。 例如，如果在已 Azure AD 中选择“userPrincipalName”，请选择“用户名”。

    d.单击“下一步”。 在“登录 URL”框中，粘贴 Azure 门户应用程序的“属性”页中的“用户访问 URL”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“注销 URL”中，粘贴从 Azure 门户的“配置登录”窗口中复制的“注销 URL”值。

12. 将“仅允许 SSO 登录”切换为“启用”。

    ![“仅允许 SSO 登录”切换](./media/active-directory-saas-absorblms-tutorial/5.png)

13. 选择“保存”。

> [!TIP]
> 设置应用时，可在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本。 从“Active Directory” > “企业应用程序”部分添加应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 有关详细信息，请参阅 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户][100]

若要在 Azure AD 中创建测试用户，请执行以下步骤：

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请选择“用户和组” > “所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. 在对话框顶部，选择“添加”。
 
    ![“添加”按钮](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框中执行以下操作：
 
    ![“用户”对话框](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”文本框中，键入 Britta Simon 的电子邮件地址。

    c. 选择“显示密码”复选框，然后记下“密码”框中的值。

    d.单击“下一步”。 选择“创建” 。

### <a name="create-an-absorb-lms-test-user"></a>创建 Absorb LMS 测试用户

要使 Azure AD 用户能够登录 Absorb LMS，必须在 Absorb LMS 中设置他们。  

对于 Absorb LMS，需要手动设置。

要设置用户帐户，请执行以下操作：

1. 以管理员身份登录 Absorb LMS 公司站点。

2. 在左窗格中，选择“用户”。

    ![“Absorb LMS 用户”链接](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. 在“用户”窗格中，选择“用户”。

    ![“用户”链接](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. 在“新增”下拉列表中，选择“用户”。

    ![“新增”下拉列表](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. 在“添加用户”页上执行以下操作：

    ![“添加用户”页](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. 在“名字”框中键入名字，例如 Britta。

    b. 在“姓氏”框中键入姓氏，例如 Simon。
    
    c. 在“用户名”框中键入全名，例如 Britta Simon。

    d.单击“下一步”。 在“密码”框中，键入 Britta Simon 的密码。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“确认密码”框中，重新键入该密码。
    
    f. 将“是否活动”设置切换为“活动”。  

6. 选择“保存”。
 
### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予用户 Britta Simon 访问 Absorb LMS 的权限，允许该用户使用 Azure 单一登录。

![分配用户角色][200]

要将用户 Britta Simon 分配到 Absorb LMS，请执行以下操作：

1. 在 Azure 门户中，打开“应用程序”视图，转到“目录”视图，然后选择“企业应用程序” > “所有应用程序”。

    ![“所有应用程序”链接][201] 

2. 在“应用程序”列表中，选择“Absorb LMS”。

    ![应用程序列表中的 Absorb LMS 链接](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. 在左窗格中，选择“用户和组”。

    ![“用户和组”链接][202] 

4. 选择“添加”，然后在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中的“用户”列表内，选择“Britta Simon”。

6. 在“用户和组”对话框中，选择“选择”按钮。

7. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在“访问面板”中，选择“Absorb LMS”磁贴可自动登录到 Absorb LMS 应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://msdn.microsoft.com/library/dn308586)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

