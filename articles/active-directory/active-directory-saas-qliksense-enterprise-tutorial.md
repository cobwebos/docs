---
title: "教程：Azure Active Directory 与 Qlik Sense Enterprise 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Qlik Sense Enterprise 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 91f8a95bdab98f079b748b5391e9b611378c6e79
ms.openlocfilehash: ce18993832ce4953a1c3b21707206cdd06580edd


---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>教程：Azure Active Directory 与 Qlik Sense Enterprise 集成
在本教程中，了解如何将 Qlik Sense Enterprise 与 Azure Active Directory (Azure AD) 集成。

将 Qlik Sense Enterprise 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Qlik Sense Enterprise
* 可以让用户使用其 Azure AD 帐户自动登录到 Qlik Sense Enterprise（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Qlik Sense Enterprise 的集成，需要以下项：

* Azure AD 订阅
* 已启用 Qlik Sense Enterprise 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Qlik Sense Enterprise
2. 配置和测试 Azure AD 单一登录

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>从库中添加 Qlik Sense Enterprise
若要配置 Qlik Sense Enterprise 与 Azure AD 的集成，需要从库中将 Qlik Sense Enterprise 添加到托管 SaaS 应用列表。

**若要从库中添加 Qlik Sense Enterprise，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]

4. 在页面底部单击“添加”。
   
    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]

6. 在搜索框中，键入“Qlik Sense Enterprise”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. 在结果窗格中，选择“Qlik Sense Enterprise”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Qlik Sense Enterprise 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Qlik Sense Enterprise 用户。 换句话说，需要在 Azure AD 用户与 Qlik Sense Enterprise 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 Qlik Sense Enterprise 中“用户名”的值来建立此链接关系。

若要使用 Qlik Sense Enterprise 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Qlik Sense Enterprise 测试用户](#creating-a-qliksense-enterprise-test-user)** - 在 Qlik Sense Enterprise 中创建 Britta Simon 的对应者，链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，在经典门户中启用 Azure AD 单一登录，并在 Qlik Sense Enterprise 应用程序中配置单一登录。

**若要使用 Qlik Sense Enterprise 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中，在“Qlik Sense Enterprise”应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 

2. 在“你希望用户如何登录 Qlik Sense Enterprise”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 
   
    a. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 Qlik Sense Enterprise 应用程序的 URL：**https://\<Qlik Sense Fully Qualifed Hostname\>:443/<Virtual Proxy Prefix\>/samlauthn/**。
   
    > [!NOTE]
    > 请注意此 URI 末尾的尾部反斜杠。  这是必需的。
    > 
    > 
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“下一步”

4. 在“配置 Qlik Sense Enterprise 的单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载元数据”，然后在计算机上保存该文件。  上传到 Qlik Sense 服务器之前，准备编辑此元数据文件。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“资源组名称” 的 Azure 数据工厂。

5. 准备联合元数据 XML 文件，以便将其上传到 Qlik Sense 服务器。
   
    > [!NOTE]
    > 将 IdP 元数据上传到 Qlik Sense 服务器之前，需要编辑该文件以删除信息，从而确保在 Azure AD 和 Qlik Sense 服务器之间操作正确。
    > 
    > 
   
    ![QlikSense][qs24]
   
    a. 在文本编辑器中打开从 Azure 下载的 FederationMetaData.xml 文件。
   
    b.保留“数据库类型”设置，即设置为“共享”。 搜索值“RoleDescriptor”。  会出现四个条目（两对开始和结束元素的标记）。
   
    c. 从文件中删除 RoleDescriptor 标记和标记之间的所有信息。
   
    d.单击“下一步”。 保存文件，将该文件放在便于取用处，以便稍后在本文档中使用。
6. 作为可以创建虚拟代理配置的用户，导航到 Qlik Sense Qlik 管理控制台 (QMC)。
7. 在 QMC 中，单击“虚拟代理”菜单项。
   
    ![QlikSense][qs6] 
8. 在屏幕的底部，单击“新建”按钮。
   
    ![QlikSense][qs7]
9. 将显示虚拟代理编辑屏幕。  屏幕右侧是显示配置选项的菜单。
   
    ![QlikSense][qs9]
10. 选中“标识”菜单选项后，输入 Azure 虚拟代理配置的标识信息。
    
    ![QlikSense][qs8]  
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 “说明”字段是虚拟代理配置的友好名称。  输入说明的值。
    
    b.保留“数据库类型”设置，即设置为“共享”。 “前缀”字段标识虚拟代理终结点，用于通过 Azure AD 单一登录连接到 Qlik Sense。  输入此虚拟代理服务器的唯一前缀名称。
    
    c. 会话非活动超时（分钟）是使用此虚拟代理进行连接产生的超时。
    
    d.单击“下一步”。 会话 Cookie 标头名称是用于存储用户在成功进行身份验证后接收到的 Qlik Sense 会话的会话标识符的 Cookie 名称。  此名称必须唯一。
11. 单击“身份验证”菜单选项，使其可见。  出现身份验证屏幕。
    
    ![QlikSense][qs10]
    
    a. “匿名访问模式”下拉列表决定匿名用户是否可以通过虚拟代理访问 Qlik Sense。  默认选项为非匿名用户。
    
    b.保留“数据库类型”设置，即设置为“共享”。 “身份验证方法”下拉列表决定虚拟代理要使用的身份验证方案。  从下拉列表中选择“SAML”。  会出现更多选项。
    
    c. 在“SAML 主机 URI 字段”中，输入用户通过此 SAML 虚拟代理访问 Qlik Sense 时将输入的主机名。  主机名是 Qlik Sense 服务器的 URI。
    
    d.单击“下一步”。 在“SAML 实体 ID”中，输入“SAML 主机 URI”字段中的相同值。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 “SAML IdP 元数据”是之前在“从 Azure AD 配置编辑联合身份验证元数据”部分中编辑的文件。  **上传 IdP 元数据之前，需要编辑该文件**以删除信息，从而确保在 Azure AD 和 Qlik Sense 服务器之间操作正确。  **如果文件尚未编辑，请参考以上说明进行操作。**  如果文件已编辑，单击“浏览”按钮并选择编辑过的元数据文件，将其上传到虚拟代理配置。
    
    f. 针对 SAML 属性（该属性表示 Azure AD 要发送到 Qlik Sense 服务器的“UserID”），输入属性名称或架构引用。  可从 Azure 应用屏幕后期配置中获取架构引用信息。  若要使用名称属性，请**输入 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
    
    g. 输入“用户目录”的值，当用户通过 Azure AD 对 Qlik Sense 服务器进行身份验证时，该值将附加到用户。  硬编码值必须用**方括号 []**包围。  若要使用 Azure AD SAML 断言中发送的属性，请在此文本框中输入属性名称，**不带**方括号。
    
    h. **SAML 签名算法**设置服务提供者（此案例中为 Qlik Sense 服务器）证书签名，以实现虚拟代理配置。  如果 Qlik Sense 服务器使用通过 Microsoft 增强 RSA 和 AES 加密提供程序生成的受信任证书，则将 SAML 签名算法更改为 **SHA 256**。
    
    i. SAML 属性映射部分允许将其他属性（例如，组）发送到 Qlik Sense，以在安全规则中使用。
12. 单击负载平衡菜单选项，使其可见。  出现负载平衡屏幕。
    
    ![QlikSense][qs11]
13. 单击“添加新服务器”节点按钮，选择一个或多个引擎节点，Qlik Sense 将向其发送会话以实现负载平衡，然后单击“添加”按钮。
    
    ![QlikSense][qs12]
14. 单击“高级”菜单选项，使其可见。 出现高级屏幕。
    
    ![QlikSense][qs13]
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 “主机允许列表”标识连接到 Qlik Sense 服务器时接受的主机名。  **输入连接到 Qlik Sense 服务器时用户将指定的主机名。** 主机名是与 SAML 主机 URI 相同的值，但没有 https://。
15. 单击“应用”按钮。
    
    ![QlikSense][qs14]
16. 单击“确定”以接受警告消息，该警告消息指出将重启链接到虚拟代理的代理。
    
    ![QlikSense][qs15]
17. 屏幕右侧出现“关联项”菜单。  单击“代理”菜单选项。
    
    ![QlikSense][qs16]
18. 出现代理屏幕。  单击底部的链接按钮，将代理链接到虚拟代理。
    
    ![QlikSense][qs17]
19. 选择用于支持此虚拟代理连接的代理节点，单击“链接”按钮。  链接后，该代理会显示在关联代理下。
    
    ![QlikSense][qs18]
    ![QlikSense][qs19]
20. 约五到十秒后显示“刷新 QMC”消息。  单击“刷新 QMC”按钮。
    
    ![QlikSense][qs20]
21. 刷新 QMC 时，单击虚拟代理菜单项。 屏幕上的表中将列出新的 SAML 虚拟代理条目。  单击虚拟代理条目。
    
    ![QlikSense][qs51]
22. 将激活屏幕底部的“下载 SP 元数据”按钮。  单击“下载 SP 元数据”按钮，将元数据保存到文件。
    
    ![QlikSense][qs52]
23. 打开 SP 元数据文件。  观察“entityID”条目和“AssertionConsumerService”条目。  这些值等效于Azure AD 应用程序配置中的“标识符”和“登录 URL”。 如果值不匹配，应在 Azure AD 应用配置向导中替换它们。
    
    ![QlikSense][qs53]
24. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]
25. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页中，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。

6. 在“用户配置文件”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-an-qlik-sense-enterprise-test-user"></a>创建 Qlik Sense Enterprise 测试用户
在本部分中，将在 Qlik Sense Enterprise 中创建一个名为“Britta Simon”的用户。 请与 Qlik Sense Enterprise 支持团队协作，在 Qlik Sense Enterprise 平台上添加用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 Qlik Sense Enterprise 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Qlik Sense Enterprise，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 

2. 在应用程序列表中，选择“Qlik Sense Enterprise”。
   
    ![配置单一登录](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

## <a name="testing-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Qlik Sense Enterprise 磁贴时，你应自动登录到 Qlik Sense Enterprise 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png



<!--HONumber=Nov16_HO4-->


