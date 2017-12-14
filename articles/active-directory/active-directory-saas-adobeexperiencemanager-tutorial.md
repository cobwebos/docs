---
title: "教程：Azure Active Directory 与 Adobe Experience Manager 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 Adobe Experience Manager 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4a4fccc4210fd6cf0ddbe99089c84a1fd38d5b09
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>教程：Azure Active Directory 与 Adobe Experience Manager 的集成

本教程介绍如何将 Adobe Experience Manager 与 Azure Active Directory (Azure AD) 集成。

将 Adobe Experience Manager 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Adobe Experience Manager。
- 可让用户使用 Azure AD 帐户自动登录到 Adobe Experience Manager（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Adobe Experience Manager 的集成，需准备好以下各项：

- 一个 Azure AD 订阅
- 启用了 Adobe Experience Manager 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Adobe Experience Manager
2. 配置并测试 Azure AD 单一登录

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>从库中添加 Adobe Experience Manager
若要配置 Adobe Experience Manager 与 Azure AD 的集成，需要将库中的 Adobe Experience Manager 添加到托管 SaaS 应用列表。

**若要从库中添加 Adobe Experience Manager，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入 **Adobe Experience Manager**，在结果面板中选择“Adobe Experience Manager”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Adobe Experience Manager](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，基于名为“Britta Simon”的测试用户配置和测试 Adobe Experience Manager 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Adobe Experience Manager 用户。 也就是说，需要在 Azure AD 用户与 Adobe Experience Manager 中的相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”值的指定为 Adobe Experience Manager 中“用户名”的值来建立此链接关系。

若要配置和测试 Adobe Experience Manager 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Adobe Experience Manager 测试用户](#create-an-adobe-experience-manager-test-user)** - 在 Adobe Experience Manager 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中的步骤在 Azure 门户中启用 Azure AD 单一登录并在 Adobe Experience Manager 应用程序中配置单一登录。

**若要配置 Adobe Experience Manager 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Adobe Experience Manager”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. 若要在“IdP”模式下配置应用，请在“Adobe Experience Manager 域和 URL”部分中执行以下步骤：

    ![Adobe Experience Manager 域和 URL 单一登录信息](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. 在“标识符”文本框中，键入同样已在 AEM 服务器上定义的唯一值。 

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<AEM Server Url>/saml_login`

    > [!NOTE] 
    > 这些不是实际值。 使用实际标识符和回复 URL 更新这些值。 请联系 [Adobe Experience Manager 支持团队](https://helpx.adobe.com/support/experience-manager.html)来获取这些值。
 
4. 若要在“SP”发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Adobe Experience Manager 域和 URL 单一登录信息](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    在“登录 URL”文本框中，键入 Adobe Experience Manager 的服务器 URL。 

5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. 在“Adobe Experience Manager 配置”部分中，单击“配置 Adobe Experience Manager”打开“配置登录”窗口。 从“快速参考”部分复制“SAML 登录服务 URL”、“SAML 实体 ID”和“注销 ID”。

    ![配置部分的链接](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. 在另一个浏览器窗口中打开“Adobe Experience Manager”管理门户。

9. 选择“设置” -> “安全” -> “用户”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. 选择“管理员”或其他任何相关用户。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. 选择“帐户设置” -> “创建/管理信任存储”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. 在“从 CER 文件添加证书”中单击“选择证书文件”按钮。 浏览并选择已从 Azure 门户下载的证书文件。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. 证书随即已添加到信任存储。 请记下证书的别名。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. 在“用户”页上，选择“身份验证服务”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. 选择“帐户设置” -> “创建/管理密钥存储”。 通过提供密码创建密钥存储。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. 返回管理屏幕，选择“设置” -> “操作” -> “Web 控制台”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

17. 此时会打开“配置”页。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

18. 找到“Adobe Granite SAML 2.0 身份验证处理程序”并单击“添加”图标。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. 在此页上执行以下操作。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. 在“路径”文本框中，输入 **/**。

    b. 在“IDP URL”文本框中，输入已从 Azure 门户复制的“SAML 登录服务 URL”值。

    c. 在“IDP 证书别名”文本框中，输入已添加到信任存储中的“证书别名”值。

    d.单击“下一步”。 在“安全提供的实体 ID”文本框中，输入已在 Azure 门户中配置的“SAML 实体 ID”唯一值。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“断言使用者服务 URL”文本框中，输入已在 Azure 门户中配置的“回复 URL”值。

    f. 在“密钥存储的密码”文本框中，输入已在密钥存储中设置的“密码”。

    g. 在“用户属性 ID”文本框中，输入“名称 ID”或与具体情况相关的其他用户 ID。

    h. 选择“自动创建 CRX 用户”。

    i. 在“注销 URL”文本框中，输入从 Azure 门户复制的“注销 URL”唯一值。

    j. 单击“保存”

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
  
### <a name="create-an-adobe-experience-manager-test-user"></a>创建 Adobe Experience Manager 测试用户

本部分的步骤在 Adobe Experience Manager 中创建名为 Britta Simon 的用户。 如果已选择“自动创建 CRX 用户”选项，则在成功完成身份验证后，会自动创建这些用户。 

若要手动创建用户，请与 [Adobe Experience Manager 支持团队](https://helpx.adobe.com/support/experience-manager.html)配合，在 Adobe Experience Manager 平台中添加用户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分的步骤通过向 Britta Simon 授予对 Adobe Experience Manager 的访问权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Adobe Experience Manager，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Adobe Experience Manager”。

    ![应用程序列表中的 Adobe Experience Manager 链接](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Adobe Experience Manager 磁贴时，应会自动登录到 Adobe Experience Manager 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

