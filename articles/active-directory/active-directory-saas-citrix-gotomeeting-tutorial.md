---
title: "教程：Azure Active Directory 与 Citrix GoToMeeting 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Citrix GoToMeeting 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7d7897f6-b88e-4dd5-8f3a-e612337b1413
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: c1ac144c4fa43312ec26fce03cd0ee1bfcf73d4b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>教程：Azure Active Directory 与 Citrix GoToMeeting 的集成

本教程介绍如何将 Citrix GoToMeeting 与 Azure Active Directory (Azure AD) 集成。

将 Citrix GoToMeeting 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Citrix GoToMeeting
- 可让用户使用其 Azure AD 帐户自动登录到 Citrix GoToMeeting（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Citrix GoToMeeting 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Citrix GoToMeeting 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Citrix GoToMeeting
2. 配置和测试 Azure AD 单一登录

## <a name="adding-citrix-gotomeeting-from-the-gallery"></a>从库添加 Citrix GoToMeeting
若要配置 Citrix GoToMeeting 与 Azure AD 的集成，需从库中将 Citrix GoToMeeting 添加到托管 SaaS 应用列表。

**若要从库中添加 Citrix GoToMeeting，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中键入“Citrix GoToMeeting”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_citrix-gotomeeting_search.png)

5. 在结果面板中选择“Citrix GoToMeeting”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_citrix-gotomeeting_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Citrix GoToMeeting 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 Citrix GoToMeeting 中与 Azure AD 用户对应的用户。 换句话说，需要建立 Azure AD 用户与 Citrix GoToMeeting 中相关用户之间的关联。

可以通过将 Azure AD 中“用户名”的值指定为 Citrix GoToMeeting 中“用户名”的值来建立此关联。

若要配置并测试 Citrix GoToMeeting 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Citrix GoToMeeting 测试用户](#creating-a-citrix-gotomeeting-test-user)** - 在 Citrix GoToMeeting 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Citrix GoToMeeting 应用程序中配置单一登录。

**若要配置 Citrix GoToMeeting 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Citrix GoToMeeting”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_citrix-gotomeeting_samlbase.png)

3. 在“Citrix GoToMeeting 域和 URL”部分，无需执行任何步骤。

    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_citrix-gotomeeting_url.png)


3. 在“SAML 签名证书”部分中，单击“证书(Base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_citrix-gotomeeting_certificate.png) 

4. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_400.png)
    
5. 在“Citrix GoToMeeting SAML 配置”部分中，单击“配置 Citrix GoToMeeting SAML”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

6. 在其他浏览器窗口中，登录到 [Citrix 组织中心](https://account.citrixonline.com/organization/administration/)。

7. 单击“标识提供者”选项卡，然后执行以下步骤：  
   
    ![SAML 设置](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“手动”

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在 Azure 门户中的“配置 Citrix GoToMeeting 的单一登录”对话框页上，复制“SAML 单一登录服务 URL”值，然后将其粘贴到“登录页 URL”文本框。 

    c. 在 Azure 门户中的“配置 Citrix GoToMeeting 的单一登录”对话框页上，复制“注销 URL”值，然后将其粘贴到“注销页 URL”文本框。

    d. 在 Azure 门户中的“配置 Citrix GoToMeeting 的单一登录”对话框页上，复制“SAML 实体 ID”值，然后将其粘贴到“标识提供者实体 ID”文本框。

    e. 若要上传所下载的证书，请单击“上传证书”。

    f. 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-citrix-gotomeeting-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-citrix-gotomeeting-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-citrix-gotomeeting-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-citrix-gotomeeting-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-citrix-gotomeeting-test-user"></a>创建 Citrix GoToMeeting 测试用户

在本部分中，将在 Citrix GoToMeeting 中创建名为 Britta Simon 的用户。 Citrix GoToMeeting 支持默认启用的实时预配。

此部分不存在任何操作项。 尝试访问 Citrix GoToMeeting 时，如果 Citrix GoToMeeting 中尚不存在用户，系统将创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Citrix GoToMeeting 支持团队](https://care.citrixonline.com/gotomeeting) 


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Citrix GoToMeeting 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配给 Citrix GoToMeeting，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Citrix GoToMeeting”。

    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_citrix-gotomeeting_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置用户预配](active-directory-saas-citrixgotomeeting-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-citrix-gotomeeting-tutorial/tutorial_general_203.png


