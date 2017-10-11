---
title: "教程：Azure Active Directory 与 Salesforce Sandbox 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Salesforce 沙盒之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 90e08b9cf2feb93de4877bec9734352949896dca
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教程：Azure Active Directory 与 Salesforce 沙盒集成

本教程介绍如何将 Salesforce 沙盒与 Azure Active Directory (Azure AD) 集成。

将 Salesforce 沙盒与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Salesforce 沙盒
- 可让用户使用其 Azure AD 帐户自动登录到 Salesforce 沙盒（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Salesforce 沙盒的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Salesforce 沙盒单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Salesforce 沙盒
2. 配置和测试 Azure AD 单一登录

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>从库中添加 Salesforce 沙盒
若要配置 Salesforce 沙盒与 Azure AD 的集成，需要从库中将 Salesforce 沙盒添加到托管的 SaaS 应用列表。

**若要从库中添加 Salesforce 沙盒，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，输入 **Salesforce 沙盒**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_search.png)

5. 在结果面板中，选择“Salesforce 沙盒”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Salesforce 沙盒的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Salesforce 沙盒用户。 换句话说，需要建立 Azure AD 用户与 Salesforce 沙盒中相关用户之间的关联。

通过将 Azure AD 中“用户名”的值指定为 Salesforce 沙盒中“用户名”的值来建立此关联。

若要配置和测试 Salesforce 沙盒的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Salesforce Sandbox 测试用户](#creating-a-salesforce-sandbox-test-user)** -若要链接到用户的 Azure AD 表示的 Salesforce Sandbox 中具有 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Salesforce 沙盒应用程序中配置单一登录。

**若要配置 Salesforce 沙盒的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Salesforce 沙盒”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. 在“Salesforce 沙盒域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_url.png)

     在“登录 URL”文本框中，使用以下模式键入值：`https://<subdomain>.my.salesforce.com`

    > [!NOTE] 
    > 此值不是真实值。 使用实际登录 URL 更新此值。与 [Salesforce 沙盒客户端支持团队](https://help.salesforce.com/support)联系以获取此值。


4. 如果已为目录中的另一个 Salesforce 沙盒实例配置单一登录，则还必须配置**标识符**以使用与**登录 URL** 相同的值。 
    
    >[!Note]
    >通过选中“配置应用 URL”对话框页上的“显示高级设置”复选框，可以找到“标识符”字段 


5. 在“SAML 签名证书”部分中单击“证书”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_400.png)

7. 在“Salesforce 沙盒配置”部分，单击“配置 Salesforce 沙盒”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_configure.png) 
<CS>
8. 在浏览器中打开新选项卡并登录到 Salesforce 沙盒管理员帐户。

9. 在顶部菜单中，单击“设置”。

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/IC781024.png)
10. 在左侧导航窗格中，单击“安全控制”，并单击“单一登录设置”。

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/IC781025.png)
11. 在“单一登录设置”部分中，执行以下步骤：![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/IC781026.png)
     
     a.  选择“已启用 SAML”。 

     b.  单击“新建” 。

12. 在“SAML 单一登录设置”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/IC781027.png)

    a.在“名称”文本框中，键入配置的名称（例如：SPSSOWAAD\_Test）。 

    b. 将“SMAL 实体 ID”值粘贴到“证书颁发者”文本框。

    c. 在“实体 ID”文本框中，输入 **https://test.salesforce.com**（如果它是要添加到目录的第一个 Salesforce 沙盒实例）。 如果已添加 Salesforce 沙盒实例，则对于**实体 ID** 键入**登录 URL**，其格式应为：`http://company.my.salesforce.com`  
 
    d.单击“下一步”。 单击“浏览”以上传已下载的证书。  

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 对于“SAML 标识类型”，选择“断言包含用户对象的联合 ID”。
 
    f. 对于“SAML 标识位置”，请选择“标识位于 Subject 语句的 NameIdentifier 元素中”。

    g. 将“单一登录服务 URL”粘贴到“标识提供者登录 URL”文本框。 

    h. SFDC 不支持 SAML 注销。  一种解决方法是，将“https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0”粘贴到“标识提供者注销 URL”文本框中。

    i. 对于“服务提供程序发起的请求绑定”，请选择“HTTP POST”。 

    j. 单击“保存” 。

### <a name="enable-your-domain"></a>启用域
本部分假定已创建域。  有关详细信息，请参阅[定义域名](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。

**若要启用域，请执行以下步骤：**

1. 在左侧导航窗格中，单击“域管理”，并单击“我的域”。
   
     ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/IC781029.png)
   
   >[!NOTE]
   >请确保域配置正确。 

2. 在“登录页设置”部分中，单击“编辑”，并对于“身份验证服务”，选择上一部分中的 SAML 单一登录设置名称，最后单击“保存”。
   
   ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/IC781030.png)

配置域后，用户应使用域 URL 登录到 Salesforce 沙盒。  

若要获取 URL 的值，请单击上一部分中创建的 SSO 配置文件。    

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_02.png) 

3. 在对话框顶部，单击“添加”以打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforcesandbox-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-salesforce-sandbox-test-user"></a>创建 Salesforce 沙盒测试用户

在本部分中，将在 Salesforce 沙盒中创建名为 Britta Simon 的用户。 Salesforce 沙盒支持在默认情况下保持启用的实时预配。
此部分不存在任何操作项。 尝试访问 Salesforce 沙盒时，如果 Salesforce 沙盒中没有用户，系统会创建一个新用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Salesforce 沙盒的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Salesforce 沙盒，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Salesforce 沙盒”。

    ![配置单一登录](./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_salesforcesandbox_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置用户预配](active-directory-saas-salesforce-sandbox-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforcesandbox-tutorial/tutorial_general_203.png

