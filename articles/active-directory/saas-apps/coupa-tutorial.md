---
title: 教程：Azure Active Directory 与 Coupa 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Coupa 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: jeedes
ms.openlocfilehash: adad60611f1447b78173368ed137205f077cb8b7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047804"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>教程：Azure Active Directory 与 Coupa 的集成

在本教程中，了解如何将 Coupa 与 Azure Active Directory (Azure AD) 集成。

将 Coupa 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Coupa。
- 可以让用户使用其 Azure AD 帐户自动登录到 Coupa（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Coupa 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Coupa 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Coupa
2. 配置和测试 Azure AD 单一登录

## <a name="adding-coupa-from-the-gallery"></a>从库中添加 Coupa
若要配置 Coupa 与 Azure AD 的集成，需要从库中将 Coupa 添加到托管 SaaS 应用列表。

**若要从库中添加 Coupa，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Coupa”，在结果面板中选择“Coupa”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Coupa](./media/coupa-tutorial/tutorial_coupa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Coupa 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Coupa 用户。 换句话说，需要建立 Azure AD 用户与 Coupa 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 Coupa 中“用户名”的值来建立此链接关系。

若要配置和测试 Coupa 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Coupa 测试用户](#create-a-coupa-test-user)** - 在 Coupa 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Coupa 应用程序中配置单一登录。

**若要配置 Coupa 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Coupa 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/coupa-tutorial/tutorial_coupa_samlbase.png)

3. 在“Coupa 域和 URL”部分中，执行以下步骤：

    ![Coupa 域和 URL 单一登录信息](./media/coupa-tutorial/tutorial_coupa_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<companyname>.coupahost.com`

    > [!NOTE]
    > 登录 URL 值不是实际值。 使用实际登录 URL 更新此值。 请联系 [Coupa 客户端支持团队](https://success.coupa.com/Support/Contact_Us?)获取此值。

    b. 在“标识符”文本框中，键入 URL：

    | 环境  | 代码 |
    |:-------------|----|
    | 沙盒 | `devsso35.coupahost.com`|
    | 生产 | `prdsso40.coupahost.com`|
    | | |

    c. 在“回复 URL”文本框中，键入 URL：

    | 环境 | 代码 |
    |------------- |----|
    | 沙盒 | `https://devsso35.coupahost.com/sp/ACS.saml2`|
    | 生产 | `https://prdsso40.coupahost.com/sp/ACS.saml2`|
    | | |

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/coupa-tutorial/tutorial_coupa_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/coupa-tutorial/tutorial_general_400.png)

6. 以管理员身份登录到 Coupa 公司站点。

7. 转到“设置”\>“安全控制”。

   ![安全性控制](./media/coupa-tutorial/ic791900.png "安全性控制")

8. 在“使用 Coupa 凭据进行登录”部分中，执行以下步骤：

    ![Coupa SP 元数据](./media/coupa-tutorial/ic791901.png "Coupa SP 元数据")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“使用 SAML 进行登录”。

    b. 单击“浏览”上传从 Azure 门户下载的元数据。

    c. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/coupa-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/coupa-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/coupa-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/coupa-tutorial/create_aaduser_04.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-coupa-test-user"></a>创建 Coupa 测试用户

为了使 Azure AD 用户能够登录到 Coupa，必须将其预配到 Coupa 中。  

* 对于 Coupa，需要手动执行预配。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Coupa** 公司站点。

2. 在顶部菜单中，单击“设置”，并单击“用户”。

   ![用户](./media/coupa-tutorial/ic791908.png "用户")

3. 单击“创建”。

   ![创建用户](./media/coupa-tutorial/ic791909.png "创建用户")

4. 在“用户创建”部分中，执行以下步骤：

   ![用户详细信息](./media/coupa-tutorial/ic791910.png "用户详细信息")

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 将要预配的有效 Azure Active Directory 帐户的**登录名**、**名字**、**姓氏**、**单一登录 ID**、**电子邮件**属性键入到相关文本框中。

   b. 单击“创建”。

   >[!NOTE]
   >Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
   >

>[!NOTE]
>可以使用 Coupa 提供的任何其他 Coupa 用户帐户创建工具或 API 来预配 AAD 用户帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Coupa 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 Coupa，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Coupa”。

    ![应用程序列表中的 Coupa 链接](./media/coupa-tutorial/tutorial_coupa_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Coupa 磁贴时，应该会自动登录 Coupa 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/coupa-tutorial/tutorial_general_01.png
[2]: ./media/coupa-tutorial/tutorial_general_02.png
[3]: ./media/coupa-tutorial/tutorial_general_03.png
[4]: ./media/coupa-tutorial/tutorial_general_04.png

[100]: ./media/coupa-tutorial/tutorial_general_100.png

[200]: ./media/coupa-tutorial/tutorial_general_200.png
[201]: ./media/coupa-tutorial/tutorial_general_201.png
[202]: ./media/coupa-tutorial/tutorial_general_202.png
[203]: ./media/coupa-tutorial/tutorial_general_203.png
