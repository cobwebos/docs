---
title: "教程：Azure Active Directory 与 Atlassian Cloud 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Atlassian Cloud 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: db9e9c7ae8380612bac9d0aeaaaf6df78cba523f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>教程：Azure Active Directory 与 Atlassian Cloud 的集成

本教程介绍如何将 Atlassian Cloud 与 Azure Active Directory (Azure AD) 集成。

将 Atlassian Cloud 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权限访问 Atlassian Cloud。
- 可以让用户使用其 Azure AD 帐户自动登录（单一登录）到 Atlassian Cloud。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Atlassian Cloud 的集成，需要具有以下项：

- 一个 Azure AD 订阅。
- 若要对 Atlassian Cloud 产品启用安全断言标记语言 (SAML) 单一登录，需要设置 Identity Manager。 详细了解 [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)。

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

* 从库中添加 Atlassian Cloud
* 配置并测试 Azure AD 单一登录

## <a name="add-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud
若要配置 Atlassian Cloud 与 Azure AD 的集成，请执行以下操作，从库中将 Atlassian Cloud 添加到托管 SaaS 应用列表：

1. 在“Azure 门户”的左窗格中，选择“Azure Active Directory”按钮[](https://portal.azure.com)。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”窗格][2]
    
3. 若要添加应用程序，请选择“新建应用程序”。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **Atlassian Cloud**，在结果列表中选择“Atlassian Cloud”，然后选择“添加”。

    ![结果列表中的 Atlassian Cloud](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分基于名为 *Britta Simon* 的测试用户配置并测试 Atlassian Cloud 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 中的用户相对应的 Atlassian Cloud 用户。 换句话说，必须在 Azure AD 用户与 Atlassian Cloud 中的相关用户之间建立链接关系。

若要建立此链接关系，可将 Azure AD 中“用户名”的值分配为 Atlassian Cloud 中“用户名”的值。

若要配置并测试 Atlassian Cloud 的 Azure AD 单一登录，需要完成后续部分中的构建基块：

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Atlassian Cloud 应用程序中配置单一登录。

若要配置 Atlassian Cloud 的 Azure AD 单一登录，请执行以下操作：

1. 在 Azure 门户中的“Atlassian Cloud”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”窗口中的“单一登录模式”框内，选择“基于 SAML 的登录”。
 
    ![“单一登录”窗口](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. 若要在 IDP 发起的模式下配置该应用程序，请在“Atlassian Cloud 域和 URL”下执行以下操作：

    ![“Atlassian Cloud 域和 URL”单一登录信息](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. 在“标识符”框中，键入 **`https://auth.atlassian.com/saml/<unique ID>`**。
    
    b. 在“回复 URL”框中，键入 **`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`**。

    c. 在“中继状态”框中，使用以下语法键入 URL：**`https://<instancename>.atlassian.net`**。

4. 若要在 SP 发起的模式下配置该应用程序，请选择“显示高级 URL 设置”，然后在“登录 URL”框中，使用以下语法键入 URL：**`https://<instancename>.atlassian.net`**。

    ![“Atlassian Cloud 域和 URL”单一登录信息](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE] 
    > 上面的值不是实际值。 请使用实际的标识符、回复 URL 和登录 URL 值更新这些值。 可从 Atlassian Cloud SAML 配置屏幕中获取实际值。 本教程稍后会解释这些值。

5. 在“SAML 签名证书”下，选择“证书(Base64)”，然后将证书文件保存到计算机。

    ![证书下载链接](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Atlassian Cloud 应用程序需要查找特定格式的 SAML 断言，这需要向“SAML 令牌属性”配置添加自定义属性映射。 

    默认情况下，“用户标识符”值映射到 user.userprincipalname。 请将此值更改为映射到 user.mail。 也可以根据组织的设置选择其他任何适当值，不过，在大多数情况下，使用电子邮件就能满足需要。

    ![证书下载链接](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png) 

7. 选择“保存”。

    ![配置单一登录 -“保存”按钮](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

8. 若要打开“配置登录”窗口，请在“Atlassian Cloud 配置”部分选择“配置 Atlassian Cloud”。 

9. 在“快速参考”部分复制“SAML 实体 ID”和“SAML 单一登录服务 URL”。 

    ![Atlassian Cloud 配置](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

10. 若要为应用程序配置 SSO，请使用管理员凭据登录到 Atlassian 门户。

11. 转到“Atlassian 站点管理” > “组织和安全”。 （如果尚未这样做）创建组织并为其命名，然后在左窗格中选择“域”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

12. 选择用于验证域的方式 -“DNS”或“HTTPS”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

13. 要执行 DNS 验证，请在“域”窗口中选择“DNS”选项卡，然后执行以下操作：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. 若要复制文本记录（TXT 记录）的值，请选择“复制”。

    b. 若要添加记录，请转到 DNS 中的设置页。

    c. 选择用于添加新记录的选项，然后将从“域”窗口复制的值粘贴到“值”字段。 此外，DNS 记录也可能将此值称为“应答”或“说明”。

    d.单击“下一步”。 你的 DNS 记录还可能包括以下字段：
    
    * 在“记录类型”框中，输入 **TXT**。
    * 在“名称/主机/别名”框中，保留默认值（@ 或空白）。
    * 在“生存时间(TTL)”框中，输入 **86400**。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。  保存该记录。

14. 返回到组织管理中的“域”窗口，然后选择“验证域”。 在“域”框中键入域名，然后选择“验证域”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)  

    > [!NOTE]
    > 由于 TXT 记录更改可能需要 72 小时才会生效，因此我们不会立即知道域验证是否已成功。 若要查看验证状态，请在完成此过程后，尽快查看“域”窗口。 更新状态将显示为“已验证”，如下图所示：
    > 
    > ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)
    > 
    > 

15. 要执行 HTTPS 验证，请在“域”窗口中选择“HTTPS”选项卡，然后执行以下操作：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a. 若要下载 HTML 文件，请选择“下载文件”。

    b. 将 HTML 文件上传到域的根目录下。

16. 返回到组织管理中的“域”页，然后选择“验证域”。 在“验证域”窗口的“域”框中键入**域名**，然后选择“验证域”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

17. 如果验证过程可以在根目录下找到上传的文件，域状态将更新为“已验证”，如下所示：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > 有关详细信息，请参阅 [Atlassian 域验证](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)。

18. 在左窗格中，选择“SAML 单一登录”。 如果尚未订阅 Atlassian Identity Manager，请订阅。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

19. 在“添加 SAML 配置”窗口中执行以下操作：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. 在“标识提供者实体 ID”框中，粘贴从 Azure 门户复制的 SAML 实体 ID。

    b. 在“标识提供者 SSO URL”框中，粘贴从 Azure 门户复制的 SAML 单一登录服务 URL。

    c. 在 Azure 门户中打开下载的证书 .txt 文件，复制其中包含的值（不要复制 *Begin Certificate* 和 *End Certificate* 行），然后将其粘贴到“公共 X509 证书”框中。
    
    d.单击“下一步”。 选择“保存配置”。
     
20. 为了确保设置正确的 URL，请执行以下操作来更新 Azure AD 设置：
  
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. 在“SAML”窗口中，复制“SP 标识 ID”，然后在 Azure 门户中 Atlassian Cloud 的“域和 URL”下，将此 ID 粘贴到“标识符”框中。
    
    b. 在“SAML”窗口中，复制“SP 断言使用者服务 URL”，然后在 Azure 门户中 Atlassian Cloud 的“域和 URL”下，将此 URL 粘贴到“回复 URL”框中。  
        “登录 URL”是 Atlassian Cloud 的租户 URL。 

    > [!NOTE]
    > 现有客户在 Azure 门户中更新“SP 标识 ID”和“SP 断言使用者服务 URL”值之后，应选择“是，更新配置”。 新客户可以跳过此步骤。 
    
21. 在 Azure 门户中，选择“保存”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> 设置应用时，可以在 [Azure 门户](https://portal.azure.com)中阅读前述教程的简洁版本。 从“Active Directory” > “企业应用程序”部分添加此应用后，请选择“单一登录”选项卡，然后在窗口底部的“配置”部分访问嵌入式文档。 有关详细信息，请参阅 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将执行以下操作，在 Azure 门户中创建一个名为 Britta Simon 的测试用户：

   ![创建 Azure AD 测试用户][100]

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请选择“用户和组” > “所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. 在“所有用户”窗口中，选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. 在“用户”窗口中执行以下操作：

    ![“用户”窗口](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 选择“创建” 。
  
### <a name="create-an-atlassian-cloud-test-user"></a>创建 Atlassian Cloud 测试用户

若要使 Azure AD 用户能够登录到 Atlassian Cloud，请执行以下操作，在 Atlassian Cloud 中手动预配用户帐户：

1. 在“管理”窗格中，选择“用户”。

    ![Atlassian Cloud“用户”链接](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. 若要在 Atlassian Cloud 中创建用户，请选择“邀请用户”。

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. 在“电子邮件地址”框中输入该用户的电子邮件地址，然后分配应用程序访问权限。 

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. 若要向该用户发送电子邮件邀请，请选择“邀请用户”。  
    随后会将一封电子邮件邀请发送给该用户，接受邀请后，该用户将在系统中激活。 

>[!NOTE] 
>还可以通过选择“用户”部分中的“批量创建”按钮来批量创建用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予用户 Britta Simon 访问 Atlassian Cloud 的权限，使其能够使用 Azure 单一登录。 为此，请执行以下操作：

![分配用户角色][200] 

1. 在 Azure 门户中，打开“应用程序”视图，转到“目录”视图，然后选择“企业应用程序” > “所有应用程序”。

    ![分配用户][201] 

2. 在“应用程序”列表中，选择“Atlassian Cloud”。

    ![应用程序列表中的 Atlassian Cloud 链接](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. 在左窗格中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”，然后在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”窗口中的“用户”列表内，选择“Britta Simon”。

6. 在“用户和组”窗口中，选择“选择”。

7. 在“添加分配”窗口中，选择“分配”。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Atlassian Cloud”磁贴时，应会自动登录到 Atlassian Cloud 应用程序。
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

