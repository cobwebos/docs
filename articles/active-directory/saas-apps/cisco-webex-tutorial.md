---
title: 教程：Azure Active Directory 与 Cisco Webex 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Cisco Webex 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005507"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>教程：Azure Active Directory 与 Cisco Webex 的集成

本教程介绍如何将 Cisco Webex 与 Azure Active Directory (Azure AD) 集成。

将 Cisco Webex 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Cisco Webex。
- 可以让用户使用他们的 Azure AD 帐户自动登录到 Cisco Webex。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Cisco Webex 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Cisco Webex 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Cisco Webex
2. 配置和测试 Azure AD 单一登录

## <a name="add-cisco-webex-from-the-gallery"></a>从库中添加 Cisco Webex
若要配置 Cisco Webex 与 Azure AD 的集成，需要从库中将 Cisco Webex 添加到托管 SaaS 应用的列表。

**若要从库中添加 Cisco Webex，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 转到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Cisco Webex”。 

5. 从结果窗格中选择“Cisco Webex”。 然后选择“添加”按钮以添加该应用程序。

    ![结果列表中的 Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Cisco Webex 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要能够确定在 Azure AD 中与 Cisco Webex 用户相对应的用户。 换句话说，需要建立 Azure AD 用户与 Cisco Webex 中相关用户之间的关联。

在 Cisco Webex 中，将“用户名”的值指定为与 Azure AD 中的“用户名”相同的值。 现在，已关联这两个用户。 

若要配置和测试 Cisco Webex 的 Azure AD 单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Cisco Webex 测试用户](#create-a-cisco-webex-test-user)以在 Cisco Webex 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Cisco Webex 应用程序中配置单一登录。

**若要配置 Cisco Webex 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Cisco Webex”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 若要启用单一登录，请在“单一登录”对话框中的“模式”下拉列表中，选择“基于 SAML 的登录”。
 
    ![“单一登录”对话框](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Cisco Webex 公司网站。

4. 从菜单的左侧中单击“设置”。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. 在“设置”页面上，在“身份验证”部分下向下滚动，单击“修改”。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. 选择“集成第三方标识提供者。(高级)”，然后转到下一个屏幕。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. 在“导出目录元数据”页面上，单击“下载元数据文件”以下载元数据文件。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. 在 Azure 门户中，在“Cisco Webex 域和 URL”部分中，通过执行以下步骤上传已下载的**服务提供程序元数据文件**并配置应用程序：

    a. 单击“上传元数据文件”。

    ![Cisco Webex 域和 URL 单一登录信息](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. 单击“文件夹徽标”来选择元数据文件并单击“上传”。

    ![Cisco Webex 域和 URL 单一登录信息](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. 成功完成**服务提供程序元数据文件**的上传后，**标识符**和**回复 URL** 值将自动填充在“Cisco Webex 域和 URL”部分的文本框中，如下所示：

    ![Cisco Webex 域和 URL 单一登录信息](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. 在“登录 URL”框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.webex.com/`
     
    > [!NOTE] 
    > 这些不是实际值。 请将这些值更新为实际登录 URL。 请联系 [Cisco Webex 客户端支持团队](https://www.webex.co.in/support/support-overview.html)获取这些值。

9. Cisco Webex 应用程序预期 SAML 断言包含特定属性。 请为此应用程序配置以下属性。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    |  属性名称  | 属性值 |
    | --------------- | -------------------- |    
    |   名    | user.givenname |
    |   姓    | user.surname |
    |   uid    | user.mail |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定” 。

11. 在“SAML 签名证书”部分中，选择“元数据 XML”，并将元数据文件保存在计算机上。

    ![证书下载链接](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. 在 Cisco Webex 公司站点管理员页面上，使用文件浏览器选项找到并上传 Azure AD 元数据文件。 然后，选择“元数据中需要由证书颁发机构签名的证书(更安全)”并转到下一个屏幕。 

    ![配置单一登录](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. 选择“测试 SSO 连接”，当新的浏览器标签页打开时，通过登录使用 Azure AD 进行身份验证。

15. 返回到 **Cisco Cloud Collaboration Management** 浏览器标签页。如果测试成功，则选择“此测试成功。启用单一登录”，并单击“保存”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择**创建**。
 
### <a name="create-a-cisco-webex-test-user"></a>创建 Cisco Webex 测试用户

本部分的目的是在 Cisco Webex 中创建名为 Britta Simon 的用户。 Cisco Webex 支持实时预配和自动用户预配，这是默认启用的。 有关如何配置自动用户预配的更多详细信息，请参见[此处](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将授予用户 Britta Simon 访问 Cisco Webex 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Cisco Webex，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图。 接着转到目录视图，然后转到“企业应用程序”。  

2. 选择“所有应用程序”。

    ![分配用户][201] 

3. 在应用程序列表中，选择“Cisco Webex”。

    ![应用程序列表中的 Cisco Webex 链接](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”按钮。 然后，在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”。

6. 在“用户和组”对话框中，单击“选择”按钮。

7. 在“添加分配”对话框中选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择 Cisco Webex 磁贴时，应会自动登录到 Cisco Webex 应用程序。

有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

