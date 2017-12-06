---
title: "教程：Azure Active Directory 与 Salesforce Sandbox 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Salesforce 沙盒之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: 128d04fdf191b60441b695efef2bf602920d80e6
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教程：Azure Active Directory 与 Salesforce 沙盒集成

本教程介绍如何将 Salesforce 沙盒与 Azure Active Directory (Azure AD) 集成。

将 Salesforce 沙盒与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Salesforce 沙盒。
- 可让用户使用其 Azure AD 帐户自动登录到 Salesforce 沙盒（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Salesforce 沙盒的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Salesforce 沙盒单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Salesforce 沙盒
2. 配置和测试 Azure AD 单一登录

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>从库中添加 Salesforce 沙盒
若要配置 Salesforce 沙盒与 Azure AD 的集成，需要从库中将 Salesforce 沙盒添加到托管的 SaaS 应用列表。

**若要从库中添加 Salesforce 沙盒，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中键入 Salesforce Sandbox，在结果面板中选择“Salesforce Sandbox”，并单击“添加”按钮来添加该应用程序。

    ![结果列表中的 Salesforce 沙盒](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Salesforce 沙盒的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Salesforce 沙盒用户。 换句话说，需要建立 Azure AD 用户与 Salesforce 沙盒中相关用户之间的关联。

可通过将 Azure AD 中“用户名”的值指定为 Salesforce 沙盒中“用户名”的值来建立此关联。

若要配置和测试 Salesforce 沙盒的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Salesforce 沙盒测试用户](#create-a-salesforce-sandbox-test-user)** - 在 Salesforce 沙盒中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Salesforce 沙盒应用程序中配置单一登录。

**若要配置 Salesforce 沙盒的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Salesforce 沙盒”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. 在“Salesforce 沙盒域和 URL”部分中，执行以下步骤：

    ![Salesforce 沙盒域和 URL 单一登录信息](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入值：`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    b. 在“标识符”文本框中，使用以下模式键入值：`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`
    
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)获取这些值。

4. 在“SAML 签名证书”部分中单击“证书”，然后在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_400.png)

6. 在“Salesforce 沙盒配置”部分，单击“配置 Salesforce 沙盒”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

7. 在浏览器中打开新选项卡并登录到 Salesforce 沙盒管理员帐户。

8. 单击页面右上角设置图标下的“安装”。

    ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/configure1.png)

9. 向下滚动到导航窗格中的“设置”，单击“标识”，以展开相关部分。 然后单击“单一登录设置”。

    ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso.png)

10. 选择“已启用 SAML”，并单击“保存”。

    ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-enable-saml.png)

11. 若要配置 SAML 单一登录设置，请单击“新建”。

    ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-admin-sso-new.png)

12. 在“SAML 单一登录设置”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-saml-config.png)

    a. 在“名称”文本框中，键入配置的名称（例如：*SPSSOWAAD_Test*）。 

    b. 将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“颁发者”字段中

    c. 在“实体 ID”文本框中，输入 `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`（如果它是要添加到目录的第一个 Salesforce 沙盒实例）。 如果已添加 Salesforce 沙盒实例，则对于**实体 ID** 键入**登录 URL**，其格式应为：`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`  
 
    d.单击“下一步”。 要上传“标识提供者证书”，请单击“选择文件”来浏览并选择已从 Azure 门户下载的证书文件。  

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 作为 SAML 标识类型，选择以下选项之一：
    
      * 如果用户的 Salesforce 用户名传递到 SAML 断言中，则选择“断言包含用户的 Salesforce 用户名”

      * 如果将来自用户对象的联合 ID 传递到 SAML 断言中，则选择“断言包含来自用户对象的联合 ID”

      * 如果将来自用户对象的用户 ID 传递到 SAML 断言中，则选择“断言包含来自用户对象的用户 ID”
 
    f. 对于“SAML 标识位置”，请选择“标识位于 Subject 语句的 NameIdentifier 元素中”。

    g. 对于“服务提供程序发起的请求绑定”，请选择“HTTP POST”。 

    h. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“单一登录服务 URL”的值。 

    i. SFDC 不支持 SAML 注销。  作为解决方法，将 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` 粘贴到“标识提供者注销 URL”文本框中。

    j. 单击“保存” 。

### <a name="enable-your-domain"></a>启用域
本部分假定已创建域。  有关详细信息，请参阅[定义域名](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。

**若要启用域，请执行以下步骤：**

1. 在 Salesforce 的左侧导航窗格中，单击“公司设置”展开相关部分，然后单击“我的域”。
   
     ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-my-domain.png)
   
   >[!NOTE]
   >请确保域配置正确。 

2. 在“身份验证配置”部分中，单击“编辑”，并对于“身份验证服务”，选择上一部分中的 SAML 单一登录设置名称，最后单击“保存”。
   
   ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/sf-edit-auth-config.png)

配置域后，用户应使用域 URL 登录到 Salesforce 沙盒。  

若要获取 URL 的值，请单击上一部分中创建的 SSO 配置文件。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-salesforce-sandbox-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-salesforce-sandbox-test-user"></a>创建 Salesforce 沙盒测试用户

在本部分中，将在 Salesforce 沙盒中创建名为 Britta Simon 的用户。 Salesforce 沙盒支持在默认情况下保持启用的实时预配。
此部分不存在任何操作项。 尝试访问 Salesforce 沙盒时，如果 Salesforce 沙盒中没有用户，系统会创建一个新用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Salesforce 沙盒的权限，允许其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Salesforce 沙盒，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Salesforce 沙盒”。

    ![应用程序列表中的 Salesforce 沙盒链接](./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Salesforce 沙盒”磁贴时，用户应自动登录到 Salesforce 沙盒应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-sandbox-tutorial/tutorial_general_203.png

