---
title: 教程：Azure Active Directory 与 Adobe Sign 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Adobe Sign 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d5cdc2ec0c6cfcf52f84629485d0dd879fbf6fa2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053992"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>教程：Azure Active Directory 与 Adobe Sign 集成

在本教程中，了解如何将 Adobe Sign 与 Azure Active Directory (Azure AD) 集成。

将 Adobe Sign 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Adobe Sign。
- 可以让用户使用其 Azure AD 帐户自动登录到 Adobe Sign（单一登录）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Adobe Sign 的集成，需要：

- Azure AD 订阅
- 已启用 Adobe Sign 单一登录的订阅

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Adobe Sign。
2. 配置和测试 Azure AD 单一登录。

## <a name="add-adobe-sign-from-the-gallery"></a>从库中添加 Adobe Sign
若要配置 Adobe Sign 与 Azure AD 的集成，需要从库中将 Adobe Sign 添加到托管 SaaS 应用列表。

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![Azure Active Directory 图标的屏幕截图][1]

2. 浏览到“企业应用程序” > “所有应用程序”。

    ![Azure Active Directory 菜单的屏幕截图，其中已突出显示“企业应用程序”和“所有应用程序”][2]
    
3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![对话框顶部的“新建应用程序”选项的屏幕截图][3]

4. 在“搜索框”中，键入“Adobe Sign”。

    ![搜索框的屏幕截图](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. 在结果面板中选择“Adobe Sign”，然后选择“添加”。

    ![结果面板的屏幕截图](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分，我们将基于名为“Britta Simon”的测试用户配置和测试 Adobe Sign 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道 Azure AD 用户与 Adobe Sign 相关用户之间的链接关系。

若要建立此链接关系，请将 Azure AD 中“用户名”的值指定为 Adobe Sign 中“用户名”的值。

若要配置和测试 Adobe Sign 的 Azure AD 单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Adobe Sign 测试用户](#creating-an-adobe-sign-test-user)，在 Adobe Sign 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. [分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#testing-single-sign-on)，以验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Adobe Sign 应用程序中配置单一登录。

1. 在 Azure 门户中的 Adobe Sign 应用程序集成页上，选择“单一登录”。

    ![Adobe Sign 应用程序集成页的屏幕截图，其中已突出显示“单一登录”][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框的屏幕截图，其中已突出显示“模式”框](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. 在“Adobe Sign 域和 URL”部分，执行以下步骤：

    ![“Adobe Sign 域和 URL”部分的屏幕截图](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.echosign.com/`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<companyname>.echosign.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)获取这些值。

4. 在“SAML 签名证书”部分中，选择“证书(Base64)”，并在计算机上保存证书文件。

    ![“SAML 签名证书”部分的屏幕截图](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. 选择“保存”。

    ![“保存”按钮的屏幕截图](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. 在“Adobe Sign 配置”部分，选择“配置 Adobe Sign”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![“Adobe Sign 配置”部分的屏幕截图，其中已突出显示“配置 Adobe Sign”](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. 在配置之前，请联系 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)，以便将域添加到 Adobe Sign 的白名单中。 下面介绍如何添加域：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)会向你发送随机生成的令牌。 对于域，令牌将如下所示：**adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. 在 DNS 文本记录中发布该验证令牌并通知 [Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)。
    
    > [!NOTE]
    > 这可能需要几天或更长时间。 请注意，DNS 传播延迟意味着 DNS 中发布的值可能在一小时或更长时间内不可见。 IT 管理员应具备有关如何在 DNS 文本记录中发布此令牌的丰富知识。
    
    c. 发布令牌并通过支持票证通知[Adobe Sign 客户端支持团队](https://helpx.adobe.com/in/contact/support.html)后，他们会验证该域并将其添加到你的帐户。
    
    d. 一般而言，在 DNS 记录中发布令牌的方法如下：

    * 登录到域帐户
    * 查找用于更新 DNS 记录的页面。 此页面可能名为“DNS 管理”、“名称服务器管理”或“高级设置”。
    * 查找你的域的 TXT 记录。
    * 添加带有 Adobe 提供的完整令牌值的 TXT 记录。
    * 保存所做更改。

8. 在其他 Web 浏览器窗口中，以管理员身份登录到 Adobe Sign 公司站点。

9. 在 SAML 菜单中，选择“帐户设置” > “SAML 设置”。
   
    ![Adobe Sign SAML 设置页的屏幕截图](./media/adobe-echosign-tutorial/ic789520.png "帐户")

10. 在“SAML 设置”部分执行以下步骤：
  
    ![SAML 设置的屏幕截图](./media/adobe-echosign-tutorial/ic789521.png "SAML 设置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“SAML 模式”下，选择“SAML 强制”。
   
    b. 选择“允许 Echosign 帐户管理员使用 Echosign 凭据登录”。
   
    c. 在“用户创建”下，选择“自动添加通过 SAML 验证了身份的用户”。

    d. 将从 Azure 门户复制的“SAML 实体 ID”粘贴到“实体 ID/颁发者 URL”文本框中。
    
    e. 将从 Azure 门户复制的“SAML 单一登录服务 URL”粘贴到“登录 URL/SSO 终结点”文本框中。
   
    f. 将从 Azure 门户复制的“注销 URL”粘贴到“注销 URL/SLO 终结点”文本框中。

    g. 在记事本中打开已下载的 **Certificate(Base64)** 文件。 将其内容复制到剪贴板，然后将其粘贴到“IdP 证书”文本框。

    h. 选择“保存更改”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![Azure 门户中的测试用户名的屏幕截图][100]

1. 在 **Azure 门户**的左窗格中，选择“Azure Active Directory”图标。

    ![Azure AD 图标的屏幕截图](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。
    
    ![Azure AD 菜单的屏幕截图，其中已突出显示“用户和组”和“所有用户”](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请选择“添加”。
 
    ![“所有用户”对话框顶部的屏幕截图，其中已突出显示“添加”选项](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框中，执行以下步骤：
 
    ![“用户”对话框的屏幕截图](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”文本框中，键入“BrittaSimon”。

    b. 在“用户名”文本框中，键入 BrittaSimon 的电子邮件地址。

    c. 选择“显示密码”并记下“密码”的值。

    d. 选择**创建**。
 
### <a name="create-an-adobe-sign-test-user"></a>创建 Adobe Sign 测试用户

为了使 Azure AD 用户能够登录到 Adobe Sign，必须对其进行预配。 这是一个手动任务。

>[!NOTE]
>可以使用任何其他 Adobe Sign 用户帐户创建工具或 Adobe Sign 提供的 API 预配 Azure AD 用户帐户。 

1. 以管理员身份登录到 **Adobe Sign** 公司站点。

2. 在顶部菜单中，选择“帐户”。 然后，在左窗格中选择“用户和组” > “创建新用户”。
   
    ![Adobe Sign 公司站点的屏幕截图，其中已突出显示“帐户”、“用户和组”和“创建新用户”](./media/adobe-echosign-tutorial/ic789524.png "帐户")
   
3. 在“创建新用户”部分中，执行以下步骤：
   
    ![“创建新用户”部分的屏幕截图](./media/adobe-echosign-tutorial/ic789525.png "创建用户")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 键入希望在相关文本框中预配的有效 Azure AD 帐户的“电子邮件地址”、“名字”和“姓氏”。
   
    b. 选择“创建用户”。

>[!NOTE]
>Azure Active Directory 帐户持有者收到一封电子邮件，该邮件包含在激活帐户前确认帐户的链接。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们将通过向 Britta Simon 授予 Adobe Sign 的访问权限，使其能够使用 Azure 单一登录。

![Azure 门户单一登录的屏幕截图][200] 

1. 在 Azure 门户中，打开应用程序视图。 浏览到目录视图，转到“企业应用程序”，并选择“所有应用程序”。

    ![Azure 门户应用程序视图的屏幕截图，其中已突出显示“企业应用程序”和“所有应用程序”][201] 

2. 在应用程序列表中，选择“Adobe Sign”。

    ![应用程序列表的屏幕截图，其中已突出显示“Adobe Sign”](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. 在左侧菜单中，选择“用户和组”。

    ![菜单的屏幕截图，其中已突出显示“用户和组”][202] 

4. 选择 **添加** 。 在“添加分配”部分选择“用户和组”。

    ![“用户和组”页和“添加分配”部分的屏幕截图][203]

5. 在“用户和组”对话框中的用户列表内，选择“Britta Simon”。

6. 在“用户和组”对话框中，单击“选择”。

7. 在“添加分配”对话框中选择“分配”。
    
### <a name="test-single-sign-on"></a>测试单一登录

在访问面板中选择 Adobe Sign 磁贴时，应会自动登录到 Adobe Sign 应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
