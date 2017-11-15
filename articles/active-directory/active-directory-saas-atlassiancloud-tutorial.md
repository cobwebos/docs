---
title: "教程：Azure Active Directory 与 Atlassian Cloud 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Atlassian Cloud 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: 25a7694cefde344b34d3a46535bf09209b306593
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>教程：Azure Active Directory 与 Atlassian Cloud 的集成

本教程介绍如何将 Atlassian Cloud 与 Azure Active Directory (Azure AD) 集成。

将 Atlassian Cloud 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权限访问 Atlassian Cloud。
- 可以让用户使用其 Azure AD 帐户自动登录到 Atlassian Cloud（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Atlassian Cloud 的集成，需要具有以下项：

- 一个 Azure AD 订阅
- 为了对 Atlassian Cloud 产品启用 SAML 单一登录，将需要设置 Identity Manager。 详细了解 [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Atlassian Cloud
2. 配置和测试 Azure AD 单一登录

## <a name="adding-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud
要配置 Atlassian Cloud 与 Azure AD 的集成，需要从库中将 Atlassian Cloud 添加到托管 SaaS 应用列表。

**若要从库中添加 Atlassian Cloud，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Atlassian Cloud”，在结果面板中选择“Atlassian Cloud”，然后单击“添加”按钮来添加应用程序。

    ![结果列表中的 Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Atlassian Cloud 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Atlassian Cloud 用户。 换句话说，需要在 Azure AD 用户与 Atlassian Cloud 中的相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Atlassian Cloud 中“用户名”的值来建立此链接关系。

若要配置并测试 Atlassian Cloud 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Atlassian Cloud 测试用户](#create-an-atlassian-cloud-test-user)** - 在 Atlassian Cloud 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Atlassian Cloud 应用程序中配置单一登录。

**若要配置 Atlassian Cloud 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Atlassian Cloud”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. 在“Atlassian Cloud 域和 URL”部分中，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![Atlassian Cloud 域和 URL 单一登录信息](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. 在“标识符”文本框中，键入 URL：`https://id.atlassian.com/login`
    
    b. 在“回复 URL”文本框中，键入 URL：`https://id.atlassian.com/login/saml/acs`

    c. 在“中继状态”文本框中，使用以下格式键入 URL：`https://<instancename>.atlassian.net`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Atlassian Cloud 域和 URL 单一登录信息](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<instancename>.atlassian.net`

    > [!NOTE] 
    > 这些不是实际值。 可以从 Atlassian Cloud SAML SAML Configuration 屏幕获取这些值，具体将在本教程后面部分对此进行介绍。

5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. 在“Atlassian Cloud 配置”部分中，单击“配置 Atlassian Cloud”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![Atlassian Cloud 配置](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. 若要为应用程序配置 SSO，请使用管理员权限登录到 Atlassian 门户。

9. 导航到“Atlassian 站点管理” > “组织和安全”。 如果尚未创建和命名组织，请执行此操作。 然后在左侧导航栏中，单击“域”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. 选择要验证域的方式 - DNS 或 HTTPS。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. 对于 DNS 验证，选择“域”页上的“DNS”选项卡，然后执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. 单击“复制”以复制 TXT 记录的值。

    b. 从你的 DNS 找到“设置”页，添加一条新记录

    c. 选择添加新记录的选项，并将从“域”页复制的值粘贴到“值”字段。 此外，DNS 还可能将此称为“应答”或“说明”。

    d.单击“下一步”。 你的 DNS 记录还可能包括以下字段：
    
    * 记录类型：输入 TXT
    * **名称/主机/别名**：保留默认值（@ 或空白）
    * 生存时间 (TTL)：输入 86400
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。  保存该记录。

12. 返回到组织管理中的“域”页，然后单击“验证域”按钮。 在弹出窗口中输入你的域名并单击“验证域”按钮。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > TXT 记录更改可能需要 72 小时才会生效，你不会立即知道域验证是否已成功。 请在执行这些步骤之后立即查看“域”页来确认验证状态。 你将会看到如下屏幕，其中更新状态为“已验证”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. 对于 HTTPS 验证，请选择“域”页上的“HTTPS”选项卡，然后执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  单击“下载文件”来下载 HTML 文件。

    b.  将 HTML 文件上传到域的根目录下。

14. 返回到组织管理中的“域”页，然后单击“验证域”按钮。 在弹出窗口中输入你的域名，并单击“验证域”按钮。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. 如果验证过程可以在根目录下找到上传的文件，域状态将更新为“已验证”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > 有关详细信息，请参阅 [Atlassian 的域验证文档](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

16. 在左侧导航栏中，单击“SAML 单一登录”。 如果尚未订阅 Atlassian Identity Manager，请订阅。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. 在“添加 SAML 配置”对话框添加 Identity Provider 设置，如下所示：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. 在“标识提供者实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    b. 在“标识提供者 SSO URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 在记事本中打开下载的证书并复制其中包含的值（不要复制 Begin 和 End 行），然后将其粘贴到“公共 X509 证书”框中。
    
    d.单击“下一步”。 单击“保存配置”以保存设置。
     
18. 更新 Azure AD 设置，确保设置正确的 URL。
  
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. 在 Atlassian Cloud“域和 URL”部分下，从 SAML 屏幕复制“SP 标识 ID”，并将该值粘贴到 Azure 门户的“标识符”框中。
    
    b. 在 Atlassian Cloud“域和 URL”部分下，从 SAML 屏幕复制“SP 断言使用者服务 URL”，并将该值粘贴到 Azure 门户的“答复 URL”框中。
    
    c. “登录 URL”是 Atlassian Cloud 的租户 URL。 
    
19. 在 Azure 门户中单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
  
### <a name="create-an-atlassian-cloud-test-user"></a>创建 Atlassian Cloud 测试用户

为使 Azure AD 用户能够登录到 Atlassian Cloud，必须将其预配到 Atlassian Cloud。 就 Atlassian Cloud 而言，预配需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 在 Atlassian 门户的“站点管理”部分，单击“用户”按钮

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. 单击“邀请用户”按钮，在 Atlassian Cloud 中创建用户。

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. 输入该用户的电子邮件地址，并分配应用程序访问权限。 

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. 单击“邀请用户”按钮，随即会向该用户发送电子邮件邀请，接受邀请后，该用户会在系统中激活。 

>[!NOTE] 
>还可通过单击“用户”部分中的“批量创建”按钮创建批量用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Atlassian Cloud 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Atlassian Cloud，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Atlassian Cloud”。

    ![应用程序列表中的 Atlassian Cloud 链接](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Atlassian Cloud 磁贴时，应当会自动登录到 Atlassian Cloud 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

