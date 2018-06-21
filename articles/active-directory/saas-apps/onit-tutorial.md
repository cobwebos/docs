---
title: 教程：Azure Active Directory 与 Onit 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Onit 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 50c5deab6cdfced64299fc770e19b504d4846807
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228734"
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>教程：Azure Active Directory 与 Onit 的集成

本教程介绍如何将 Onit 与 Azure Active Directory (Azure AD) 集成。

将 Onit 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Onit。
- 可使用户通过其 Azure AD 帐户自动登录 Onit（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Onit 的集成，需要以下项目：

- Azure AD 订阅
- 启用 Onit 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Onit
2. 配置和测试 Azure AD 单一登录

## <a name="adding-onit-from-the-gallery"></a>从库中添加 Onit
若要配置 Onit 与 Azure AD 的集成，需要从库中将 Onit 添加到托管 SaaS 应用列表。

**若要从库中添加 Onit，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Onit”，在结果面板中选择“Onit”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 Onit](./media/onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Onit 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Onit 用户。 换句话说，需要建立 Azure AD 用户与 Onit 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 Onit 中“用户名”的值来建立此链接关系。

若要使用 Onit 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Onit 测试用户](#create-an-onit-test-user)** - 在 Onit 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)**，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Onit 应用程序中配置单一登录。

**若要使用 Onit 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Onit”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/onit-tutorial/tutorial_onit_samlbase.png)

3. 在“Onit 域和 URL”部分中，执行以下步骤：

    ![Onit 域和 URL 单一登录信息](./media/onit-tutorial/tutorial_onit_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<sub-domain>.onit.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<sub-domain>.onit.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Onit 客户端支持团队](https://www.onit.com/support)获取这些值。 
 
4. 在“SAML 签名证书”部分中，复制证书的“指纹”值。

    ![证书下载链接](./media/onit-tutorial/tutorial_onit_certificate.png) 

5. Onit 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可从应用程序的“属性”选项卡管理这些属性的值。 以下屏幕截图显示一个示例。 

    ![配置单一登录](./media/onit-tutorial/tutorial_onit_attribute.png) 

6. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ------------------- | -------------------- |
    | 电子邮件 | user.mail |
    
    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/onit-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/onit-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”留空。
    
    e. 单击“确定” 。

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/onit-tutorial/tutorial_general_400.png)

8. 在“Onit 配置”部分，单击“配置 Onit”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![Onit 配置](./media/onit-tutorial/tutorial_onit_configure.png)

9. 在另一 Web 浏览器窗口中，以管理员身份登录到 Onit 公司站点。

10. 在顶部菜单中，单击“管理”。
   
   ![管理](./media/onit-tutorial/IC791174.png "Administration")
11. 单击“编辑公司”。
   
   ![编辑公司](./media/onit-tutorial/IC791175.png "编辑公司")
   
12. 单击“安全”选项卡。
    
    ![编辑公司信息](./media/onit-tutorial/IC791176.png "编辑公司信息")

13. 在“安全”选项卡中，执行以下步骤：

    ![单一登录](./media/onit-tutorial/IC791177.png "单一登录")

    a. 选择“单一登录和密码”作为“身份验证策略”。
    
    b. 在“Idp 目标 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 在“Idp 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 在“Idp 证书指纹(SHA1)”文本框中，粘贴从 Azure 门户复制的证书“指纹”值。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/onit-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/onit-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/onit-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/onit-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-onit-test-user"></a>创建 Onit 测试用户

要让 Azure AD 用户登录 Onit，必须将其预配到 Onit 中。  

使用 Onit 时，预配属手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Onit** 公司站点。
2. 单击“添加用户”。
   
   ![管理](./media/onit-tutorial/IC791180.png "Administration")
3. 在“添加用户”对话框页上，执行以下步骤：
   
   ![添加用户](./media/onit-tutorial/IC791181.png "添加用户")
   
  1. 在相关文本框中键入要预配的有效 Azure AD 帐户的“名称”和“电子邮件地址”。
  2. 单击“创建”。    
   
 > [!NOTE]
 > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Onit 的权限，使她能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Onit，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Onit”。

    ![应用程序列表中的 Onit 链接](./media/onit-tutorial/tutorial_onit_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Onit 磁贴，即可自动登录 Onit 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onit-tutorial/tutorial_general_01.png
[2]: ./media/onit-tutorial/tutorial_general_02.png
[3]: ./media/onit-tutorial/tutorial_general_03.png
[4]: ./media/onit-tutorial/tutorial_general_04.png

[100]: ./media/onit-tutorial/tutorial_general_100.png

[200]: ./media/onit-tutorial/tutorial_general_200.png
[201]: ./media/onit-tutorial/tutorial_general_201.png
[202]: ./media/onit-tutorial/tutorial_general_202.png
[203]: ./media/onit-tutorial/tutorial_general_203.png

