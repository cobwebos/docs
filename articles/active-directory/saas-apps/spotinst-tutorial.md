---
title: 教程：Azure Active Directory 与 Spotinst 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Spotinst 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.openlocfilehash: 7edf1cbc5cc351e25a9ae7b319768376ea9968a3
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266806"
---
# <a name="tutorial-azure-active-directory-integration-with-spotinst"></a>教程：Azure Active Directory 与 Spotinst 集成

本教程将介绍如何将 Spotinst 与 Azure Active Directory (Azure AD) 集成。

Spotinst 与 Azure AD 的集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Spotinst。
- 可让用户使用其 Azure AD 帐户自动登录到 Spotinst（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需详细了解 SaaS 应用与 Azure AD 的集成，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 Spotinst 的集成，需具备以下项：

- Azure AD 订阅
- 启用了 Spotinst 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Spotinst
2. 配置和测试 Azure AD 单一登录

## <a name="adding-spotinst-from-the-gallery"></a>从库中添加 Spotinst
要配置 Spotinst 到 Azure AD 的集成，需要将库中的 Spotinst 添加到托管的 SaaS 应用的列表。

要从库中添加 Spotinst，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Spotinst”，在结果面板中选择“Spotinst”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Spotinst](./media/spotinst-tutorial/tutorial_spotinst_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 Spotinst 的 Azure AD 单一登录。

要使用单一登录，Azure AD 需要知道与 Azure AD 用户对应的 Spotinst 用户。 换句话说，需要建立 Azure AD 用户与 Spotinst 中相关用户之间的链接关系。

要配置和测试 Spotinst 的 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Spotinst 测试用户](#create-a-spotinst-test-user) - 在 Spotinst 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Spotinst 应用程序中配置单一登录。

要配置 Spotinst 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户的 Spotinst 应用程序集成页面上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/spotinst-tutorial/tutorial_spotinst_samlbase.png)

3. 在“Spotinst 域和 URL”部分，如果要在启用了 IDP 的模式下配置应用程序，请执行下列步骤：

    ![Spotinst 域和 URL 单一登录信息](./media/spotinst-tutorial/tutorial_spotinst_url1.png)

    a. 选中“显示高级 URL 设置”。

    b. 在“中继状态”文本框中，键入值：`<ID>`

    c. 如果要在启用了 SP 的模式下配置应用程序，请在“登录 URL”文本框中键入 URL：`https://console.spotinst.com`

    > [!NOTE]
    > “中继状态”值不是实际值。 用户将使用实际的中继状态值更新该值（详见本教程的稍后部分）。

4. Spotinst 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示了一个示例。

    ![配置单一登录](./media/spotinst-tutorial/tutorial_Spotinst_attribute.png)

5. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 属性名称 | 属性值 |
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |
    
    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/spotinst-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/spotinst-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”留空。

    e. 单击“确定”

6. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/spotinst-tutorial/tutorial_spotinst_certificate.png) 

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/spotinst-tutorial/tutorial_general_400.png)

8. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Spotinst。

9. 单击屏幕右上方的用户图标，再单击“设置”。

    ![Spotinst 设置](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

10. 单击顶部的“安全性”选项卡，然后选择“标识提供者”并执行以下步骤：

    ![Spotinst 安全性](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. 复制实例的“中继状态”值并将其粘贴到 Azure 门户中“Spotinst 域和 URL”部分的“中继状态”文本框。

    b. 单击“浏览”，上传从 Azure 门户下载的元数据 xml 文件

    c. 单击“保存”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/spotinst-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/spotinst-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/spotinst-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/spotinst-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-spotinst-test-user"></a>创建 Spotinst 测试用户

本部分旨在 Spotinst 中创建名为 Britta Simon 的用户。

1. 如果已在启用了 SP 的模式下配置应用程序，请执行以下步骤：

   a. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Spotinst。

   b. 单击屏幕右上方的用户图标，再单击“设置”。

    ![Spotinst 设置](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. 单击“用户”，然后选择“添加用户”。

    ![Spotinst 设置](./media/spotinst-tutorial/adduser1.png)

    d. 在“添加用户”部分中，执行以下步骤：

    ![Spotinst 设置](./media/spotinst-tutorial/adduser2.png)

    * 在“全名”文本框中输入用户的全名，例如 BrittaSimon。

    * 在“电子邮件”文本框中，输入用户的电子邮件地址（例如 brittasimon@contoso.com）。

    * 为组织角色、帐户角色和帐户选择组织特定的详细信息。

2. 如果已在启用了 IDP 的模式下配置应用程序，则此部分中没有操作项。 Spotinst 支持实时预配（该预配默认启用）。 尝试访问 Spotinst 期间，如果尚不存在用户，则会创建一个新用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Spotinst 的访问权限支持她使用 Azure 单一登录。

![分配用户角色][200] 

要将 Britta Simon 分配到 Spotinst，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Spotinst”。

    ![应用程序列表中的 Spotinst 链接](./media/spotinst-tutorial/tutorial_spotinst_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Spotinst”磁贴时，用户应会自动登录到 Spotinst 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/spotinst-tutorial/tutorial_general_01.png
[2]: ./media/spotinst-tutorial/tutorial_general_02.png
[3]: ./media/spotinst-tutorial/tutorial_general_03.png
[4]: ./media/spotinst-tutorial/tutorial_general_04.png

[100]: ./media/spotinst-tutorial/tutorial_general_100.png

[200]: ./media/spotinst-tutorial/tutorial_general_200.png
[201]: ./media/spotinst-tutorial/tutorial_general_201.png
[202]: ./media/spotinst-tutorial/tutorial_general_202.png
[203]: ./media/spotinst-tutorial/tutorial_general_203.png

