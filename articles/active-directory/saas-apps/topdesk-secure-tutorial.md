---
title: 教程：Azure Active Directory 与 TOPdesk - Secure 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 TOPdesk - Secure 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 2da2a2cae3993f7c29726b842db6767d4300cacc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045251"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>教程：Azure Active Directory 与 TOPdesk - Secure 集成

本教程介绍如何将 TOPdesk - Secure 与 Azure Active Directory (Azure AD) 集成。

将 TOPdesk - Secure 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 TOPdesk - Secure。
- 可以让用户使用其 Azure AD 帐户自动登录到 TOPdesk - Secure（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 TOPdesk - Secure 的集成，需要以下项：

- Azure AD 订阅
- 已启用 TOPdesk - Secure 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 TOPdesk - Secure
2. 配置和测试 Azure AD 单一登录

## <a name="adding-topdesk---secure-from-the-gallery"></a>从库中添加 TOPdesk - Secure
要配置 TOPdesk - Secure 与 Azure AD 的集成，需要从库中将 TOPdesk - Secure 添加到托管 SaaS 应用列表。

**若要从库中添加 TOPdesk - Secure，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **TOPdesk - Secure**，在结果面板中选择“TOPdesk - Secure”，单击“添加”按钮添加该应用程序。

    ![结果列表中的 TOPdesk - Secure](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，将基于名为“Britta Simon”的测试用户配置和测试 TOPdesk - Secure 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 TOPdesk - Secure 用户。 换句话说，需要建立 Azure AD 用户与 TOPdesk - Secure 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 TOPdesk - Secure 中“用户名”的值来建立此链接关系。

若要配置和测试 TOPdesk - Secure 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 TOPdesk - Secure 测试用户](#create-a-topdesk---secure-test-user)** - 在 TOPdesk - Secure 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 TOPdesk - Secure 应用程序中配置单一登录。

**若要配置 TOPdesk - Secure 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“TOPdesk - Secure”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. 在“TOPdesk - Secure 域和 URL”部分中，执行以下步骤：

    ![TOPdesk - Secure 域和 URL 单一登录信息](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<companyname>.topdesk.net`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 本教程稍后会说明“回复 URL”。 请联系 [TOPdesk - Secure 客户端支持团队](http://www.topdesk.com/us/support)获取这些值。 

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/topdesk-secure-tutorial/tutorial_general_400.png)

6. 在“TOPdesk - Secure 配置”部分中，单击“配置 TOPdesk - Secure”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![TOPdesk - Secure 配置](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. 以管理员身份登录 **TOPdesk - Secure** 公司站点。

8. 在“TOPdesk”菜单中，单击“设置”。

    ![设置](./media/topdesk-secure-tutorial/ic790598.png "设置")

9. 单击“登录设置”。

    ![登录设置](./media/topdesk-secure-tutorial/ic790599.png "登录设置")

10. 展开“登录设置”菜单，并单击“常规”。

    ![常规](./media/topdesk-secure-tutorial/ic790600.png "常规")

11. 在“SAML 登录”配置部分的“安全”部分中，执行以下步骤：

    ![技术设置](./media/topdesk-secure-tutorial/ic790855.png "技术设置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“下载”下载公共元数据文件，并将该文件保存到本地计算机上。
   
    b. 打开元数据文件，并找到“AssertionConsumerService”节点。
    
    ![断言使用者服务](./media/topdesk-secure-tutorial/ic790856.png "断言使用者服务")
   
    c. 复制 **AssertionConsumerService** 值，并将此值粘贴到“TOPdesk - Secure 域和 URL”部分中的“回复 URL”文本框内。

12. 若要创建证书文件，请执行以下步骤：
    
    ![证书](./media/topdesk-secure-tutorial/ic790606.png "证书")
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在 Azure 门户中打开下载的元数据文件。

    b. 展开其 **xsi:type** 为 **fed:ApplicationServiceType** 的 **RoleDescriptor** 节点。

    c. 复制 **X509Certificate** 节点的值。

    d. 将复制的 **X509Certificate** 值保存到本地计算机上的文件中。

13. 在“公共”部分中，单击“添加”。
    
    ![添加](./media/topdesk-secure-tutorial/ic790607.png "Add")

14. 在“SAML 配置助手”对话框页上，执行以下步骤：
    
    ![SAML 配置助手](./media/topdesk-secure-tutorial/ic790608.png "SAML 配置助手")
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 若要从 Azure 门户上传已下载的元数据文件，请在“联合元数据”下单击“浏览”。

    b. 若要上载证书文件，请在“证书(RSA)”下，单击“浏览”。

    c. 若要上载从 TOPdesk 支持团队获得的徽标文件，请在“徽标图标”下，单击“浏览”。

    d. 在“用户名属性”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。

    e. 在“显示名称”文本框中，键入配置名称。

    f. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/topdesk-secure-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/topdesk-secure-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/topdesk-secure-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/topdesk-secure-tutorial/create_aaduser_04.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-topdesk---secure-test-user"></a>创建 TOPdesk - Secure 测试用户

要使 Azure AD 用户能够登录 TOPdesk - Secure，必须将这些用户预配到 TOPdesk - Secure 中。  
对于 TOPdesk - Secure，预配是一项手动任务。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录 **TOPdesk - Secure** 公司站点。
2. 在顶部菜单中，单击“TOPdesk”\>“新建”\>“支持文件”\>“操作员”。
   
    ![操作员](./media/topdesk-secure-tutorial/ic790610.png "操作员")

3. 在“新建操作员”对话框中，执行以下步骤：
   
    ![新建操作员](./media/topdesk-secure-tutorial/ic790611.png "新建操作员")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“常规”选项卡。
   
    b. 在“姓氏”文本框中，键入用户的姓氏（如 Simon）。
   
    c. 在“位置”部分中选择帐户所在的**站点**。
   
    d. 在“TOPdesk 登录”部分的“登录名”文本框中，键入用户的登录名。
   
    e. 单击“ **保存**”。

> [!NOTE]
> 可以使用任何其他 TOPdesk - Secure 用户帐户创建工具或 TOPdesk - Secure 提供的 API 来预配 AAD 用户帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 TOPdesk - Secure 的权限，允许其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 TOPdesk - Secure，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“TOPdesk - Secure”。

    ![应用程序列表中的 TOPdesk - Secure 链接](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 TOPdesk - Secure 磁贴时，应会自动登录到 TOPdesk - Secure 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-secure-tutorial/tutorial_general_203.png

