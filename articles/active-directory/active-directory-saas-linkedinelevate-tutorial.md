---
title: "教程：Azure Active Directory 与 LinkedIn Elevate 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 LinkedIn Elevate 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 5336543e06d60be555722a615568b12048c2aa2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>教程：Azure Active Directory 与 LinkedIn Elevate 的集成

本教程介绍了如何将 LinkedIn Elevate 与 Azure Active Directory (Azure AD) 进行集成。

将 LinkedIn Elevate 与 Azure AD 集成可具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 LinkedIn Elevate
- 可以让用户使用其 Azure AD 帐户自动登录到 LinkedIn Elevate（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LinkedIn Elevate 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 LinkedIn Elevate 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 LinkedIn Elevate
2. 配置并测试 Azure AD 单一登录

## <a name="adding-linkedin-elevate-from-the-gallery"></a>从库中添加 LinkedIn Elevate
要配置 LinkedIn Elevate 与 Azure AD 的集成，需要从库中将 LinkedIn Elevate 添加到托管 SaaS 应用列表。

**若要从库中添加 LinkedIn Elevate，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **LinkedIn Elevate**。 从结果面板单击“LinkedIn Elevate”以添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 LinkedIn Elevate 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 LinkedIn Elevate 用户。 换句话说，需要在 Azure AD 用户与 LinkedIn Elevate 中的相关用户之间建立关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 LinkedIn Elevate 中“用户名”的值来建立此链接关系。

若要配置并测试 LinkedIn Elevate 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 LinkedIn Elevate 测试用户](#creating-a-linkedin-elevate-test-user)** - 通过 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中的步骤在 Azure 管理门户中启用 Azure AD 单一登录并在 LinkedIn Elevate 应用程序中配置单一登录。

**若要配置 LinkedIn Elevate 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“LinkedIn Elevate”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. 在另一个 Web 浏览器窗口中，以管理员身份登录到 LinkedIn Elevate 租户。

4. 在“帐户中心”，单击“设置”下的“全局设置”。 此外，请从下拉列表中选择“提升 - 提升 AAD 测试”。

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. 单击“打开”或单击此处以加载，从窗体复制单个字段，并复制“实体 ID”和“断言使用者访问(ACS) URL”

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. 在 Azure 门户的“LinkedIn Elevate 域和 URL”下，如果想要以“已启动 IdP”模式配置 SSO，请执行以下步骤

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. 在“标识符”文本框中，输入从 LinkedIn 门户复制的“实体 ID” 

    b. 在“回复 URL”文本框中，输入从 LinkedIn 门户复制的“断言使用者访问(ACS) URL”

7. 如果想要以“已启动 SP”模式配置 SSO，请单击配置部分的“显示高级 URL”设置选项，并使用以下模式配置登录 URL：

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 
    
    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 
    
8. LinkedIn Elevate 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示一个示例。 “用户标识符”的默认值是“user.userprincipalname”，但 LinkedIn Elevate 要求通过用户的电子邮件地址映射此项。 为此，可以使用列表中的 **user.mail** 属性，或使用基于组织配置的相应属性值。 

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. 在“用户属性”部分，单击“查看和编辑所有其他用户属性”并设置属性。 需要添加另一个名为 **department** 的声明，且值必须映射到 **user.department**中。

    | 属性名称 | 属性值 |
    | --- | --- |    
    | department| user.department |

      ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. 单击“添加属性”，打开属性详细信息页，添加如下所示的 department 属性

      ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. 单击“确定”保存属性。

      c. 将属性 emailaddress 的名称更改为 email。


10. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. 单击“保存” 。

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. 转到“LinkedIn 管理设置”分区。 单击“上传 XML”文件选项，上传刚从 Azure 门户下载的 XML 文件。

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. 单击“开启”启用 SSO。 SSO 状态将从“未连接”更改为“已连接”

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 

### <a name="creating-a-linkedin-elevate-test-user"></a>创建 LinkedIn Elevate 测试用户

LinkedIn Elevate 应用程序支持及时用户设置，进行身份验证后，会在应用程序中自动创建用户。 在 LinkedIn Elevate 门户的管理设置页上，将“自动分配许可证”开关打开为活动状态以启用及时预配，这也会将许可证分配给用户。

   ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予对 LinkedIn Elevate 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 LinkedIn Elevate，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“LinkedIn Elevate”。

    ![配置单一登录](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击“访问面板”中的 LinkedIn Elevate 磁贴时，应到达 Azure 登录页，成功登录后，应到达 LinkedIn Elevate 应用程序。

## <a name="additional-resources"></a>其他资源

* [教程：使用 Azure Active Directory 为LinkedIn Elevate 配置自动用户预配](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png
