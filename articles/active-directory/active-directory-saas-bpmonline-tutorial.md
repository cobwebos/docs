---
title: 教程：Azure Active Directory 与 Bpm’online 集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 与 Bpm’online 之间的单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 052db91d-ccff-4098-8ae3-2f76eca90539
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: d3e3cf8b9931665305baa4dc0968cb24394ba7d7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-bpmonline"></a>教程：Azure Active Directory 与 Bpm’online 集成

本教程介绍了如何将 Bpm’online 与 Azure Active Directory (Azure AD) 集成。

将 Bpm’online 与 Azure AD 集成可以带来下列优势：

- 可以控制在 Azure AD 中谁有权访问 Bpm’online。
- 方便用户使用自己的 Azure AD 帐户自动登录 Bpm’online（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Bpm’online 的集成，必须有以下项：

- Azure AD 订阅
- 已启用 Bpm’online 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Bpm’online
2. 配置和测试 Azure AD 单一登录

## <a name="adding-bpmonline-from-the-gallery"></a>从库中添加 Bpm’online
若要配置 Bpm’online 与 Azure AD 的集成，需要将库中的 Bpm’online 添加到托管 SaaS 应用程序列表。

**若要从库中添加 Bpm’online，请按照以下步骤操作：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Bpm’online”，从结果面板中选择“Bpm’online”，再单击“添加”按钮，添加此应用程序。

    ![结果列表中的“Bpm’online”](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在此部分中，将根据名为“Britta Simon”的测试用户，配置和测试 Azure AD 与 Bpm’online 之间的单一登录。

为了让单一登录能够正常运行，Azure AD 需要知道 Azure AD 用户在 Bpm’online 中的对应用户是谁。 也就是说，需要关联 Azure AD 用户和 Bpm’online 中的相关用户。

将 Azure AD 中的“用户名”值指定为 Bpm’online 中的“用户名”值，从而建立关联。

若要配置和测试 Azure AD 与 Bpm’online 之间的单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Bpm’online 测试用户](#create-a-bpmonline-test-user)** - 这样 Bpm’online 中就有 Britta Simon 的对应用户，此用户与 Azure AD 表示的用户相关联。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在此部分中，将在 Azure 门户中启用 Azure AD 单一登录，并在 Bpm’online 应用程序中配置单一登录。

**若要配置 Azure AD 与 Bpm’online 之间的单一登录，请按照以下步骤操作：**

1. 在 Azure 门户的“Bpm’online 应用程序集成”页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_samlbase.png)

3. 若要在 IDP 启动模式下配置应用程序，请在“Bpm’online 域和 URL”部分中，按照以下步骤操作：

    ![“Bpm’online 域和 URL”单一登录信息](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<client site name>.bpmonline.com/`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<client site name>.bpmonline.com/ServiceModel/AuthService.svc/SsoLogin`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![“Bpm’online 域和 URL”单一登录信息](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<client site name>.bpmonline.com/`
     
    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [Bpm’online 客户端支持团队](mailto:support@bpmonline.com)。 

5. 在“SAML 签名证书”部分上，单击”复制”按钮来复制**应用联合元数据 URL**，并将其粘贴到记事本。
    
    ![配置单一登录](./media/active-directory-saas-bpmonline-tutorial/tutorial_metadataurl.png)
     
6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-bpmonline-tutorial/tutorial_general_400.png)
    
7. 若要在 Bpm’online 端配置单一登录，需要将应用联合元数据 URL 发送给 [Bpm’online 支持团队](mailto:support@bpmonline.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-bpmonline-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-bpmonline-test-user"></a>创建 Bpm’online 测试用户

在此部分中，将在 Bpm’online 中创建名为 Britta Simon 的用户。 与 [Bpm’online 支持团队](mailto:support@bpmonline.com)协作，共同在 Bpm’online 平台中添加用户。 使用单一登录前，必须先创建并激活用户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在此部分中，将授予对 Bpm’online 的访问权限，让 Britta Simon 可以使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Bpm’online，请按照以下步骤操作：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Bpm’online”。

    ![应用程序列表中的“Bpm’online”链接](./media/active-directory-saas-bpmonline-tutorial/tutorial_bpmonline_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击“访问面板”中的“Bpm’online”磁贴后，应该会自动登录 Bpm’online 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bpmonline-tutorial/tutorial_general_203.png

