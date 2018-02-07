---
title: "教程：Azure Active Directory 与 Palo Alto Networks - 管理 UI 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Palo Alto Networks - 管理 UI 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: 8e54630d97dee2388ffc9c8877faeac269df1609
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>教程：Azure Active Directory 与 Palo Alto Networks - 管理 UI 集成

在本教程中，了解如何将 Palo Alto Networks - 管理 UI 与 Azure Active Directory (Azure AD) 进行集成。

将 Palo Alto Networks - 管理 UI 与 Azure AD 集成可提供以下好处：

- 可在 Azure AD 中控制谁有权访问 Palo Alto Networks - 管理 UI。
- 可以让用户通过其 Azure AD 帐户自动登录到 Palo Alto Networks - 管理 UI（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Palo Alto Networks - 管理 UI 的集成，需具有以下项目：

- Azure AD 订阅
- Palo Alto Networks 新一代防火墙或 Panorama（针对防火墙的集中管理系统）

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Palo Alto Networks - 管理 UI
2. 配置和测试 Azure AD 单一登录

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>从库添加 Palo Alto Networks - 管理 UI
若要配置 Palo Alto Networks - 管理 UI 与 Azure AD 的集成，需要从库中将 Palo Alto Networks - 管理 UI 添加到托管的 SaaS 应用列表。

若要从库中添加 Palo Alto Networks - 管理 UI，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Palo Alto Networks - 管理 UI”，从结果面板选择“Palo Alto Networks - 管理 UI”，然后单击“添加”按钮来添加应用程序。

    ![结果列表中的 Palo Alto Networks - 管理 UI](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户，配置和测试 Palo Alto Networks - 管理 UI 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Palo Alto Networks - 管理 UI 用户。 换句话说，需要建立 Azure AD 用户与 Palo Alto Networks - 管理 UI 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Palo Alto Networks - 管理 UI 中“Username”的值来建立此链接关系。

若要配置和测试 Palo Alto Networks - 管理 UI 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Palo Alto Networks - 管理 UI 测试用户](#create-a-palo-alto-networks---admin-ui-test-user)** - 在 Palo Alto Networks - 管理 UI 中创建 Britta Simon 的对应用户，以链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，可以在 Azure 门户中启用 Azure AD 单一登录，并在 Palo Alto Networks - 管理 UI 应用程序中配置单一登录。

若要配置 Palo Alto Networks - 管理 UI 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Palo Alto Networks - 管理 UI”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

3. 在“Palo Alto Networks - 管理 UI 域和 URL”上，执行以下步骤：

    ![Palo Alto Networks - 管理 UI 域和 URL 单一登录信息](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<Customer Firewall FQDN>/php/login.php`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<Customer Firewall FQDN>/SAML20/SP`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请联系 [Palo Alto Networks - 管理 UI 客户端支持团队](https://support.paloaltonetworks.com/support)。 
 
4. Palo Alto Networks - 管理 UI 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
5. 在“单一登录”对话框上的“用户属性”部分中，配置 SAML 令牌属性（如上图所示），并执行以下步骤：注意属性值仅为示例，请为 username 和 adminrole 映射相应的值。 另外还有一个可选属性“accessdomain”，用于限制管理员访问防火墙上的特定虚拟系统。
        
    | 属性名称 | 属性值 |
    | --- | --- |    
    | username | user.userprincipalname |
    | adminrole | customadmin |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d.单击“下一步”。 单击“确定”

    > [!NOTE]
    > 有关属性的详细信息，可参阅以下文章。
    > 1. 适用于管理 UI 的管理角色配置文件 (adminrole)：https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile
    > 2. 适用于管理 UI 的设备访问域 (accessdomain)：https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain

6. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_400.png)

8. 在另一个浏览器窗口中，以管理员身份打开 Palo Alto 网络防火墙管理 UI。

9. 单击“设备”。

    ![配置 Palo Alto 单一登录](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

10. 从左侧导航栏选择“SAML 标识提供者”，并单击“导入”以导入元数据文件。

    ![配置 Palo Alto 单一登录](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

11. 在“导入”窗口执行以下操作

    ![配置 Palo Alto 单一登录](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. 在“配置文件名称”文本框中提供名称，如 Azure AD 管理 UI。
    
    b. 在“标识提供者元数据”中，单击“浏览”并选择从 Azure 门户下载的元数据 xml 文件。
    
    c. 单击 **“确定”**

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>创建 Palo Alto Networks - 管理 UI 测试用户

Palo Alto Networks - 管理 UI 支持实时用户预配，以便在身份验证成功后自动在系统中创建用户（若尚不存在）。 无需在此处执行任何操作。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Palo Alto Networks - 管理 UI 的权限，使她能够使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 指定到 Palo Alto Networks - 管理 UI，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Palo Alto Networks - 管理 UI”。

    ![应用程序列表中的 Palo Alto Networks - 管理 UI 链接](./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Palo Alto Networks - 管理 UI”磁贴时，应会自动登录到 Palo Alto Networks - 管理 UI 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltoadmin-tutorial/tutorial_general_203.png

