---
title: "教程：Azure Active Directory 与 Workday 的集成 | Microsoft Docs"
description: "了解如何使用 Workday 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: e6b1c8cbf45fea4f5af16231003ccc07a2a53233
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>教程：Azure Active Directory 与 Workday 的集成
本教程旨在说明 Azure 与 Workday 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Workday 中的租户

在本教程中概述的方案由以下构建基块组成：

1. 为 Workday 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 配置用户设置

![方案](./media/active-directory-saas-workday-tutorial/IC782919.png "方案")

## <a name="enabling-the-application-integration-for-workday"></a>为 Workday 启用应用程序集成
本部分旨在概述如何为 Salesforce 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>若要为 Workday 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-workday-tutorial/IC700994.png "应用程序")

4. 若要打开**应用程序库**，请单击“添加应用”，然后单击“为组织添加要使用的应用程序”。
   
    ![想要做什么？](./media/active-directory-saas-workday-tutorial/IC700995.png "想要做什么？")

5. 在搜索框中，键入“Workday”。
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6. 在结果窗格中，选择“Workday”，然后单击“完成”以添加该应用程序。
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Workday 进行身份验证。  
在此过程中，需要创建 Base-64 编码的证书。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在“Workday”应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-workday-tutorial/IC782920.png "配置单一登录")

2. 在“你希望用户如何登录 Workday”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-workday-tutorial/IC782921.png "配置单一登录")

3. 在“配置应用 URL”页上，执行以下步骤，然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-workday-tutorial/IC782957.png "配置应用 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入用户用于登录到 Workday 的 URL：`https://impl.workday.com/<tenant>/login-saml2.htmld`
   
    b.  在“Workday 回复 URL”文本框中，使用以下模式键入 Workday 回复 URL：`https://impl.workday.com/<tenant>/login-saml.htmld`
   
    > [!NOTE]
    > 回复 URL 必须具有一个子域（例如：www、wd2、wd3、wd3-impl、wd5 和 wd5-impl）。 可以使用类似于“*http://www.myworkday.com*”的 URL，但不能使用“*http://myworkday.com*”的 URL。 
    > 
    > 

4. 在“配置 Workday 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
 
    ![配置单一登录](./media/active-directory-saas-workday-tutorial/IC782922.png "配置单一登录")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Workday 公司站点。

6. 转到“菜单”\>“工作台”。
   
    ![工作台](./media/active-directory-saas-workday-tutorial/IC782923.png "工作台")

7. 转到“帐户管理”。
   
    ![帐户管理](./media/active-directory-saas-workday-tutorial/IC782924.png "帐户管理")

8. 转到“编辑租户设置 - 安全”。
   
    ![编辑租户安全性](./media/active-directory-saas-workday-tutorial/IC782925.png "编辑租户安全性")

9. 在“重定向 URL”部分中，执行以下步骤：
   
    ![重定向 URL](./media/active-directory-saas-workday-tutorial/IC7829581.png "重定向 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加行”。
   
    b. 在“登录重定向 URL”文本框和“移动重定向 URL”文本框中，键入在 Azure 经典门户的“配置应用 URL”中输入的 **Workday 租户 URL**。
   
    c. 在 Azure 经典门户的“配置 Workday 的单一登录”对话框页上，复制**单一注销服务 URL**，然后将其粘贴到“注销重定向 URL”文本框中。
   
    d.单击“下一步”。  在“环境”文本框中，键入环境名称。  

    >[!NOTE]
    > “环境”属性的值与租户 URL 的值绑定：  
    >-   如果 Workday 租户 URL 的域名以 impl 开头（例如：*https://impl.workday.com/\<tenant\>/login-saml2.htmld*），则“环境”属性必须设置为“实现”。  
    >-   如果域名以其他内容开头，则需要联系 Workday 以获取匹配的**环境**值。

1. 在“SAML 设置”部分中执行以下步骤：
   
    ![SAML 设置](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML 设置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  选择“启用 SAML 身份验证”。
   
    b.  单击“添加行”。

2. 在“SAML 标识提供者”部分中，执行以下步骤：
   
    ![SAML 标识提供者](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML 标识提供者")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“标识提供者名称”文本框中，键入提供者名称（例如：*SPInitiatedSSO*）。
   
    b. 在 Azure 经典门户的“配置 Workday 的单一登录”对话框页上，复制“标识提供者 ID”值，然后将其粘贴到“颁发者”文本框中。
   
    c. 选择“启用 Workday 发起的注销”。
   
    d.单击“下一步”。 在 Azure 经典门户的“配置 Workday 的单一登录”对话框页上，复制“单一注销服务 URL”值，然后将其粘贴到“注销请求 URL”文本框中。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“标识提供者公钥证书”，然后单击“创建”。 

    ![创建](./media/active-directory-saas-workday-tutorial/IC782928.png "创建")

    f. 单击“创建 x509 公钥”。 

    ![创建](./media/active-directory-saas-workday-tutorial/IC782929.png "创建")


1. 在“查看 x509 公钥”部分中，执行以下步骤： 
   
    ![查看 x509 公钥](./media/active-directory-saas-workday-tutorial/IC782930.png "查看 x509 公钥") 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入证书名称（例如：*PPE\_SP*）。
   
    b. 在**有效起始日期**文本框中，键入证书的“有效起始日期”属性值。
   
    c.  在**有效截止日期**文本框中，键入证书的“有效截止日期”属性值。
   
    > [!NOTE]
    > 可以双击下载的证书，以从中获取有效起始日期和有效截止日期。  该日期在“详细信息”选项卡下列出。
    > 
    > 
   
    d.单击“下一步”。 基于下载的证书创建一个 **Base-64 编码**的文件。  
   
    > [!TIP]
    > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  在记事本中打开 Base-64 编码的证书，然后复制其内容。
   
    f.  在“证书”文本框中，粘贴剪贴板的内容。
   
    g.  单击 **“确定”**。

2. 执行以下步骤： 
   
    ![SSO 配置](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO 配置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  启用“x509 私钥对”。
   
    b.  在“服务提供商 ID”文本框中，键入 **http://www.workday.com**。
   
    c.  选择“启用 SP 发起的 SAML 身份验证”。
   
    d.单击“下一步”。  在 Azure 经典门户的“配置 Workday 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“IdP SSO 服务 URL”文本框中。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择“不削弱 SP 发起的身份验证请求”。
   
    f. 对于**身份验证请求签名方法**，请选择 **SHA256**。 
   
    ![身份验证请求签名方法](./media/active-directory-saas-workday-tutorial/IC782932.png "身份验证请求签名方法") 
   
    g. 单击 **“确定”**。 
   
    ![确定](./media/active-directory-saas-workday-tutorial/IC782933.png "确定")

3. 在 Azure 经典门户中的“配置 Workday 的单一登录”页上，单击“下一步”。 
   
    ![配置单一登录](./media/active-directory-saas-workday-tutorial/IC782934.png "配置单一登录")

4. 在“单一登录确认”页上，单击“完成”。 
   
    ![配置单一登录](./media/active-directory-saas-workday-tutorial/IC782935111.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
若要将测试用户预配到 Workday 中，需要联系 Workday 支持团队。  
Workday 支持团队将为你创建用户。

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-workday-perform-the-following-steps"></a>若要将用户分配到 Workday，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“Workday”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-workday-tutorial/IC782935.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-workday-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


