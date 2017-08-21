---
title: "教程：Azure Active Directory 与 ServiceNow 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 ServiceNow 以及 ServiceNow Express 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: f4180601e720351a8b0e95ccffe7712028cfba08
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017

---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>教程：Azure Active Directory 与 ServiceNow 集成
在本教程中，了解如何将 ServiceNow 和 ServiceNow Express 与 Azure Active Directory (Azure AD) 集成。

将 ServiceNow 和 ServiceNow Express 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 ServiceNow 和 ServiceNow Express
* 可以让用户使用其 Azure AD 帐户自动登录到 ServiceNow 和 ServiceNow Express（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 ServiceNow 以及 ServiceNow Express 的集成，需要以下项：

* Azure AD 订阅
* 对于 ServiceNow，需要 Calgary 版本或更高版本的 ServiceNow 实例或租户
* 对于 ServiceNow Express，需要 Helsinki 版本或更高版本的 ServiceNow Express 实例
* ServiceNow 租户必须启用[多提供程序单一登录插件](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)。 这可以通过[提交服务请求](https://hi.service-now.com)完成。 

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 除非必要，请勿使用生产环境。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ServiceNow
2. 针对 ServiceNow 或 ServiceNow Express 配置和测试 Azure AD 单一登录

## <a name="adding-servicenow-from-the-gallery"></a>从库中添加 ServiceNow
若要配置 ServiceNow 或 ServiceNow Express 与 Azure AD 的集成，需要从库中将 ServiceNow 添加到托管 SaaS 应用列表。 

**若要从库中添加 ServiceNow，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“ServiceNow”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. 在结果窗格中，选择“ServiceNow”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 ServiceNow 或 ServiceNow Express 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 ServiceNow 用户。 换句话说，需要建立 Azure AD 用户与 ServiceNow 中相关用户之间的链接关系。
通过将 Azure AD 中“用户名”的值分配为 ServiceNow 中“用户名”的值来建立此链接关系。 若要配置和测试 ServiceNow 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[针对 ServiceNow 配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on-for-servicenow)** - 让用户能够使用此功能。
2. **[针对 ServiceNow Express 配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - 让用户能够使用此功能。
3. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[创建 ServiceNow 测试用户](#creating-a-servicenow-test-user)** - 在 ServiceNow 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
5. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
6. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

> [!NOTE]
> 如果想要配置 ServiceNow，请省略步骤 2。 同理，如果想要配置 ServiceNow Express，请省略步骤 1。
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>为 ServiceNow 配置 Azure AD 单一登录
1. 在 Azure AD 经典门户的“ServiceNow”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC749323.png "配置单一登录")

2. 在“你希望用户如何登录 ServiceNow”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC749324.png "配置单一登录")

3. 在“配置应用设置”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "配置应用 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“ServiceNow 登录 URL”文本框中，使用以下模式键入用户用于登录 ServiceNow 应用程序的 URL：`https://<instance-name>.service-now.com`。
   
    b. 在“标识符”文本框中，使用以下模式键入用户用于登录 ServiceNow 应用程序的 URL：`https://<instance-name>.service-now.com`。
   
    c. 单击“下一步”

4. 若要使 Azure AD 自动配置 ServiceNow 进行基于 SAML 的身份验证，请在“自动配置单一登录”窗体中输入 ServiceNow 实例名称、管理员用户名和管理员密码，并单击“配置”。 请注意，为使此操作生效，提供的管理员用户名必须具有在 ServiceNow 中分配的 **security_admin** 角色。 否则，若要手动配置 ServiceNow 作为 SAML 标识提供者使用 Azure AD，请单击“手动配置应用程序进行单一登录”，然后单击“下一步”，完成后续步骤。
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "配置应用 URL")

5. 在“在 ServiceNow 上配置单一登录”页上，单击“下载证书”，将证书文件本地保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC749325.png "配置单一登录")

6. 以管理员身份登录到 ServiceNow 应用程序。

7. 请按照以下步骤操作，激活“集成 - 多提供程序单一登录安装程序”插件：
   
    a. 在左侧导航窗格中，转到“系统定义”部分，然后单击“插件”。
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "激活插件")
   
    b.保留“数据库类型”设置，即设置为“共享”。 搜索“集成 - 多提供程序单一登录安装程序”。
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "激活插件")
   
    c. 选择插件。 右键单击插件，选择“激活/升级”。
   
    d. 单击“激活”按钮。

8. 在左侧导航窗格中，单击“属性”。  
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "配置应用 URL")

9. 在“多提供程序 SSO 属性”对话框中，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "配置应用 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“启用多提供程序 SSO”，选择“是”。
   
    b. 对于“启用调试日志记录获取多提供程序 SSO 集成”，选择“是”。
   
    c. 在“...用户表上的字段”文本框中，键入“user_name”。
   
    d. 单击“保存” 。

10. 在左侧导航窗格中，单击“x509 证书”。
    
     ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "配置单一登录")

11. 在“X.509 证书”对话框中，单击“新建”。
    
     ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "配置单一登录")

12. 在“x.509 证书”对话框中，执行以下步骤：
    
     ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "配置单一登录")
    
     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“新建” 。
    
     b. 在“名称”文本框中，键入配置名称（例如：**TestSAML2.0**）。
    
     c. 选择“活动”。
    
     d. 对于“格式”，选择“PEM”。
    
     e. 对于“类型”，选择“信任存储证书”。
    
     f. 在记事本中打开 Base64 编码的证书，将内容复制到剪贴板，然后将其粘贴到“PEM 证书”文本框中。
    
     g. 单击“更新”。

13. 在左侧导航窗格中，单击“标识提供者”。
    
     ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "配置单一登录")

14. 在“标识提供者”对话框中，单击“新建”：
    
     ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "配置单一登录")

15. 在“标识提供者”对话框中，单击“SAML2 Update1?”：
    
     ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "配置单一登录")

16. 在“SAML2 Update1 属性”对话框中，执行以下步骤：
    
     ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "配置单一登录")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入配置名称（例如：**SAML 2.0**）。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户字段”文本框中，键入“电子邮件”或“user_name”，具体取决于哪个字段用于唯一标识 ServiceNow 部署中的用户。 

    > [!NOTE] 
    > 可以配置 Azure AD 以发出 Azure AD 用户 ID（用户主体名称）或电子邮件地址作为 SAML 令牌中的唯一标识符，方法是转到 Azure 经典门户的“ServiceNow”>“属性”>“单一登录”部分，并将所需字段映射到“nameidentifier”属性。 Azure AD 中为所选属性存储的值（例如用户主体名称）必须匹配 ServiceNow 中为输入字段存储的值（例如 user_name）

    c. 在 Azure AD 经典门户中，复制“标识提供者 ID”值，然后将其粘贴到“标识提供者 URL”文本框中。

    d. 在 Azure AD 经典门户中，复制“身份验证请求 URL”值，然后将其粘贴到“标识提供者 AuthnRequest”文本框中。

    e. 在 Azure AD 经典门户中，复制“单一注销服务 URL”值，然后将其粘贴到“标识提供者 SingleLogoutRequest”文本框中。

    f. 在“ServiceNow 主页”文本框中，键入 ServiceNow 实例主页的 URL。

    > [!NOTE] 
    > ServiceNow 实例主页是 **ServieNow 租户 URL** 和 **/navpage.do** 的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。

    g. 在“实体 ID/颁发者”文本框中，键入 ServiceNow 租户的 URL。

    h. 在“受众 URL”文本框中，键入 ServiceNow 租户的 URL。 

    i. 在“IDP 的 SingleLogoutRequest 协议绑定”文本框中，键入“urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect”。

    j. 在 NameID 策略文本框中，键入“urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified”。

    k. 取消选择“创建 AuthnContextClass”。

    l. 在“AuthnContextClassRef 方法”中，键入 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`。 如果是纯粹的云组织，才需要此操作。 如果正在使用本地 ADFS 或 MFA 进行身份验证，不应配置此值。 

    m. 在“时钟偏差”文本框中，键入“60”。

    n. 对于“单一登录脚本”，选择“MultiSSO_SAML2_Update1”。

    o. 对于“x509 证书”，选择在上一步已创建的证书。

    p. 单击“提交”。 

1. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。 
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "配置单一登录")

2. 在“单一登录确认”页上，单击“完成”。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "配置单一登录")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>为 ServiceNow Express 配置 Azure AD 单一登录
1. 在 Azure AD 经典门户的“ServiceNow”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC749323.png "配置单一登录")

2. 在“你希望用户如何登录 ServiceNow”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC749324.png "配置单一登录")

3. 在“配置应用设置”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/IC769497.png "配置应用 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“ServiceNow 登录 URL”文本框中，使用以下模式键入用户用于登录 ServiceNow 应用程序的 URL：`https://<instance-name>.service-now.com`。
   
    b. 在“颁发者 URL”文本框中，使用以下模式键入用户用于登录 ServiceNow 应用程序的 URL `https://<instance-name>.service-now.com`。
   
    c. 单击“下一步”

4. 单击“手动配置应用程序进行单一登录”，然后单击“下一步”并完成后续步骤。
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "配置应用 URL")

5. 在“在 ServiceNow 处配置单一登录”页上，单击“下载证书”，将证书文件本地保存在计算机，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC749325.png "配置单一登录")

6. 以管理员身份登录到 ServiceNow Express 应用程序。

7. 在左侧导航窗格中，单击“单一登录”。  
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "配置应用 URL")

8. 在“单一登录”对话框中，单击右上角的配置图标，设置以下属性：
   
    ![配置应用 URL](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "配置应用 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 将“启用多提供程序 SSO”切换到右侧。
   
    b. 将“为多提供程序 SSO 集成启用调试日志记录”切换到右侧。
   
    c. 在“...用户表上的字段”文本框中，键入“user_name”。
9. 在“单一登录”对话框中，单击“添加新证书”。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "配置单一登录")
10. 在“x.509 证书”对话框中，执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "配置单一登录")
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入配置名称（例如：**TestSAML2.0**）。
    
    b. 选择“活动”。
    
    c. 对于“格式”，选择“PEM”。
    
    d. 对于“类型”，选择“信任存储证书”。
    
    e. 基于下载的证书创建 Base64 编码的文件。
    
    > [!NOTE]
    > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。
    > 
    > 
    
    f. 在记事本中打开 Base64 编码的证书，将内容复制到剪贴板，然后将其粘贴到“PEM 证书”文本框中。
    
    g. 单击“更新”。
11. 在“单一登录”对话框中，单击“添加新 IdP”。
    
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "配置单一登录")
12. 在“添加新的标识提供者”对话框的“配置标识提供者”下，执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "配置单一登录")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入配置名称（例如：**SAML 2.0**）。

    b. 在 Azure AD 经典门户中，复制“标识提供者 ID”值，然后将其粘贴到“标识提供者 URL”文本框中。

    c. 在 Azure AD 经典门户中，复制“身份验证请求 URL”值，然后将其粘贴到“标识提供者 AuthnRequest”文本框中。

    d. 在 Azure AD 经典门户中，复制“单一注销服务 URL”值，然后将其粘贴到“标识提供者 SingleLogoutRequest”文本框中。

    e. 对于“标识提供者证书”，选择在上一步已创建的证书。


1. 单击“高级设置”，并在“其他标识提供者属性”下，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "配置单一登录")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“IDP 的 SingleLogoutRequest 协议绑定”文本框中，键入“urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect”。
   
    b. 在“NameID 策略”文本框中，键入“urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified”。    
   
    c. 在“AuthnContextClassRef 方法”中，键入“http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password”。
   
    d. 取消选择“创建 AuthnContextClass”。

2. 在“其他服务提供者属性”下，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "配置单一登录")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“ServiceNow 主页”文本框中，键入 ServiceNow 实例主页的 URL。
   
    > [!NOTE]
    > ServiceNow 实例主页是 **ServieNow 租户 URL** 和 **/navpage.do**的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。
    > 
    > 
   
    b. 在“实体 ID/颁发者”文本框中，键入 ServiceNow 租户的 URL。
   
    c. 在“受众 URI”文本框中，键入 ServiceNow 租户的 URL。 
   
    d. 在“时钟偏差”文本框中，键入“60”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“用户字段”文本框中，键入“电子邮件”或“user_name”，具体取决于哪个字段用于唯一标识 ServiceNow 部署中的用户。
   
    > [!NOTE]
    > 可以配置 Azure AD 以发出 Azure AD 用户 ID（用户主体名称）或电子邮件地址作为 SAML 令牌中的唯一标识符，方法是转到 Azure 经典门户的“ServiceNow”>“属性”>“单一登录”部分，并将所需字段映射到“nameidentifier”属性。 Azure AD 中为所选属性存储的值（例如用户主体名称）必须匹配 ServiceNow 中为输入字段存储的值（例如 user_name）
    > 
    > 
   
    f. 单击“保存” 。 

3. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。 
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "配置单一登录")

4. 在“单一登录确认”页上，单击“完成”。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
本部分的目的是概述如何对 ServiceNow 启用 Active Directory 用户帐户的用户预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 在 Azure 管理经典门户中的“ServiceNow”应用程序集成页上，单击“配置用户预配”。 
   
    ![用户设置](./media/active-directory-saas-servicenow-tutorial/IC769498.png "用户设置")

2. 在“输入用于启用自动用户预配的 ServiceNow 凭据”页上，提供以下配置设置：
   
     a. 在“ServiceNow 实例名称”文本框中，键入 ServiceNow 实例名称。
   
     b. 在“ServiceNow 管理员用户名”文本框中，键入 ServiceNow 管理员帐户名称。
   
     c. 在“ServiceNow 管理员密码”文本框中，键入此帐户的密码。
   
     d. 单击“验证”以验证配置。
   
     e. 单击“下一步”按钮，打开“后续步骤”页。
   
     f. 如果想要将所有用户预配到此应用程序，请选择“将目录中的所有用户帐户自动预配到此应用程序”。 
   
    ![后续步骤](./media/active-directory-saas-servicenow-tutorial/IC698804.png "后续步骤")
   
     g. 在“后续步骤”页上，单击“完成”保存配置。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。

6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-servicenow-test-user"></a>创建 ServiceNow 测试用户
在本部分中，将在 ServiceNow 中创建一个名为“Britta Simon”的用户。 在本部分中，将在 ServiceNow 中创建一个名为“Britta Simon”的用户。 如果不知道如何在 ServiceNow 或 ServiceNow Express 帐户中添加用户，请联系 ServiceNow 支持团队。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 ServiceNow 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 ServiceNow，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 

2. 在应用程序列表中，选择“ServiceNow”。
   
    ![配置单一登录](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 

4. 在“所有用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

单击“访问面板”中的 ServiceNow 磁贴时，应自动登录到 ServiceNow 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png

