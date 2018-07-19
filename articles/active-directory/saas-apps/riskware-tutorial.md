---
title: 教程：Azure Active Directory 与 Riskware 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Riskware 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.openlocfilehash: 7705baa0ba912f24d7859110c75d36703aeb4a77
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041953"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>教程：Azure Active Directory 与 Riskware 的集成

本教程介绍如何将 Riskware 与 Azure Active Directory (Azure AD) 集成。

将 Riskware 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Riskware。
- 可让用户使用其 Azure AD 帐户自动登录到 Riskware（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Riskware 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Riskware 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Riskware
2. 配置和测试 Azure AD 单一登录

## <a name="adding-riskware-from-the-gallery"></a>从库中添加 Riskware
要配置 Riskware 与 Azure AD 的集成，需要从库中将 Riskware 添加到托管 SaaS 应用列表。

**若要从库中添加 Riskware，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Riskware”，在结果面板中选择“Riskware”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Riskware](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为“Britta Simon”的测试用户使用 Riskware 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Riskware 用户。 换句话说，需要建立 Azure AD 用户与 Riskware 中相关用户之间的链接关系。

若要配置和测试 Riskware 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Riskware 测试用户](#create-a-riskware-test-user)** - 在 Riskware 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Riskware 应用程序中配置单一登录。

**若要配置 Riskware 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Riskware** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

3. 在“Riskware 域和 URL”部分中，执行以下步骤：

    ![Riskware 域和 URL 单一登录信息](./media/riskware-tutorial/tutorial_riskware_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，键入使用以下模式的 URL：
    | 环境| URL 模式|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. 在“标识符(实体 ID)”文本框中，键入 URL：
    | 环境| URL 模式|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新此值。 若要获取此值，请与 [Riskware 客户端支持团队](mailto:support@pansoftware.com.au)联系。

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/riskware-tutorial/tutorial_general_400.png)

6. 在“Riskware 配置”部分，单击“配置 Riskware”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![Riskware 配置](./media/riskware-tutorial/tutorial_riskware_configure.png)

7. 在另一 Web 浏览器窗口中，以管理员身份登录到 Riskware 公司站点。

8. 在右上角，单击“维护”打开维护页。

    ![Riskware 配置 - 维护](./media/riskware-tutorial/tutorial_riskware_maintain.png)

9. 在维护页中，单击“身份验证”。

    ![Riskware 配置 - 身份验证](./media/riskware-tutorial/tutorial_riskware_authen.png)

10. 在“身份验证配置”页中执行以下步骤：

    ![Riskware 配置 - 身份验证配置](./media/riskware-tutorial/tutorial_riskware_config.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“SAML”作为身份验证的**类型**。

    b. 在“代码”文本框中，键入类似于 AZURE_UAT 的代码。

    c. 在“说明”文本框中，键入类似于“AZURE Configuration for SSO”的说明。

    d. 在“单一登录页”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    e. 在“注销页”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    f. 在“发布表单字段”文本框中，键入“发布响应”中包含 SAML 的字段名称（如 SAMLResponse）

    g. 在“XML 标识标记名称”文本框中键入属性，其中包含 SAML 响应中的唯一标识符（如 NameID）。

    h. 在记事本中打开从 Azure 门户下载的**元数据 XML**，复制元数据文件中的证书，并将其粘贴到“证书”文本框中

    i. 在“使用者 URL”文本框中，粘贴从支持团队获取的“回复 URL”值。

    j. 在“颁发者”文本框中，粘贴从支持团队获取的“标识符”值。

    > [!Note]
    > 若要获取这些值，请与 [Riskware 客户端支持团队](mailto:support@pansoftware.com.au)联系。

    k. 选中“使用 POST”复选框。

    l. 选中“使用 SAML 请求”复选框。

    m. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/riskware-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/riskware-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/riskware-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/riskware-tutorial/create_aaduser_04.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-riskware-test-user"></a>创建 Riskware 测试用户

要使 Azure AD 用户能够登录到 Riskware，必须将其预配到 Riskware 中。 在 Riskware 中，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Riskware。

2. 在右上角，单击“维护”打开维护页。 

    ![Riskware 配置 - 维护](./media/riskware-tutorial/tutorial_riskware_maintain.png)

3. 在维护页中，单击“人员”。

    ![Riskware 配置 - 人员](./media/riskware-tutorial/tutorial_riskware_people.png)

4. 选择“详细信息”选项卡，并执行以下步骤：

    ![Riskware 配置 - 详细信息](./media/riskware-tutorial/tutorial_riskware_details.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“人员类型”，例如“员工”。

    b. 在“名字”文本框中，输入用户的名字（如“Britta”）。

    c. 在“姓氏”文本框中，输入用户的姓氏（如“Simon”）。

5. 在“安全”选项卡中，执行以下步骤：

    ![Riskware 配置 - 安全性](./media/riskware-tutorial/tutorial_riskware_security.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“身份验证”部分下，选择设置的“身份验证”模式（例如“AZURE Configuration for SSO”）。

    b. 在“登录详细信息”部分下的“用户 ID”文本框中，输入类似于 **brittasimon@contoso.com** 的用户电子邮件。

    c. 在“密码”文本框中，输入用户的密码。

6. 在“组织”选项卡中执行以下步骤：

    ![Riskware 配置 - 组织](./media/riskware-tutorial/tutorial_riskware_org.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择作为 **Level1** 组织的选项。

    b. 在“人员的主要工作区”部分下的“位置”文本框中，键入自己的位置。

    c. 在“员工”部分下，选择“员工状态”，例如“休假”。

7. 单击“ **保存**”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 Riskware 的访问权限，使其能够使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 Riskware，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Riskware”。

    ![应用程序列表中的 Riskware 链接](./media/riskware-tutorial/tutorial_riskware_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Riskware”磁贴，即可自动登录到 Riskware 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

