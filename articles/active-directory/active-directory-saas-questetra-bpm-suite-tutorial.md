---
title: "教程：Azure Active Directory 与 Questetra BPM Suite 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Questetra BPM Suite 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 7ae75446c9d19ce15a82caa9604658a528ab9941
ms.contentlocale: zh-cn
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>教程：Azure Active Directory 与 Questetra BPM Suite 集成
本教程旨在说明如何将 Questetra BPM Suite 与 Azure Active Directory (Azure AD) 集成。  
将 Questetra BPM Suite 与 Azure AD 集成提供以下优势： 

* 可在 Azure AD 中控制谁有权访问 Questetra BPM Suite 
* 可以让用户使用其 Azure AD 帐户自动登录 Questetra BPM Suite（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Questetra BPM Suite 的集成，需备齐以下项目：

* Azure AD 订阅
* 已启用 [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) 单一登录的订阅

> [!NOTE]
> 测试本教程中的步骤时，建议不要使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
本教程中概述的方案包括三个主要构建基块：

1. 从库中添加 Questetra BPM Suite 
2. 配置并测试 Azure AD 单一登录

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>从库中添加 Questetra BPM Suite
若要配置 Questetra BPM Suite 与 Azure AD 的集成，需要从库中将 Questetra BPM Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 Questetra BPM Suite，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]

4. 在页面底部单击“添加”。
   
    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]

6. 在搜索框中，键入“Questetra BPM Suite”。
   
    ![应用程序][5]

7. 在结果窗格中，选择“Questetra BPM Suite”，然后单击“完成”以添加该应用程序。

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 Questetra BPM Suite 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Questetra BPM Suite 用户。 换句话说，需要在 Azure AD 用户与 Questetra BPM Suite 中相关用户之间建立链接关系。  
通过将 Azure AD 中**用户名**的值分配为 Questetra BPM Suite 中**用户名**的值来建立此链接关系。

若要配置和测试 Questetra BPM Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Questetra BPM Suite 测试用户](#creating-a-questetra-bpm-suite-test-user)** - 在 Questetra BPM Suite 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 Questetra BPM Suite 应用程序中配置单一登录。

**若要配置 Questetra BPM Suite 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Questetra BPM Suite”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][8]

2. 在“你希望用户如何登录 Questetra BPM Suite”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![Azure AD 单一登录][9]

3. 在其他 Web 浏览器窗口中，以管理员身份登录 **Questetra BPM Suite** 公司站点。

4. 在顶部菜单中，单击“系统设置”。 
   
    ![Azure AD 单一登录][10]

5. 若要打开“SingleSignOnSAML”页，请单击“SSO (SAML)”。 
   
    ![Azure AD 单一登录][11]

6. 在 Azure 经典门户中，在“配置应用设置”对话框页上，执行以下步骤： 
   
    ![配置应用设置][13]
   
    a. 在 **Questetra BPM Suite** 公司站点上的“SP 信息”部分，复制“ACS URL”，然后将其粘贴到“登录 URL”文本框中。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 **Questetra BPM Suite** 公司站点上的“SP 信息”部分，复制“实体 ID”，然后将其粘贴到“颁发者 URL”文本框中。
   
    c. 在 **Questetra BPM Suite** 公司站点上的“SP 信息”部分，复制“ACS URL”，然后将其粘贴到“回复 URL”文本框中。
   
    d.单击“下一步”。 单击“下一步”。

7. 在“配置 Questetra BPM Suite 的单一登录”页上，单击“下载证书”，然后将证书文件保存在本地计算机上。
   
    ![配置单一登录][14]

8. 在 **Questetra BPM Suite** 公司站点上，执行以下步骤： 
   
    ![配置单一登录][15]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“启用单一登录”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 Azure 经典门户中，复制“颁发者 URL”值，然后将其粘贴到“实体 ID”文本框中。
   
    c. 在 Azure 经典门户中，复制“单一登录服务 URL”值，然后将其粘贴到“登录页 URL”文本框中。
   
    d.单击“下一步”。 在 Azure 经典门户中，复制“单一注销服务 URL”值，然后将其粘贴到“注销页 URL”文本框中。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“NameID 格式”文本框中，键入“urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress”。

    f. 从下载的证书创建 base-64 编码的文件。 

    >[!TIP] 
    >有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

    g. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“验证证书”文本框中。 

    h. 单击“保存” 。

1. 在 Azure 经典门户上，选择“单一登录配置确认”，然后单击“下一步”。 
   
    ![什么是 Azure AD Connect][17]

2. 在“单一登录确认”页上，单击“完成”。  
   
    ![什么是 Azure AD Connect][18]


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户][100] 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中单击“用户”。
   
    ![创建 Azure AD 测试用户][101] 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户][102] 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户][103]
   
    a. 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。

6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户][104] 
   
    a. 在“名字”文本框中，键入“Britta”。 
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户][105]  

8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户][106]   
   
    a. 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-questetra-bpm-suite-test-user"></a>创建 Questetra BPM Suite 测试用户
本部分的目的是在 Questetra BPM Suite 中创建名为“Britta Simon”的用户。

**若要在 Questetra BPM Suite 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录 Questetra BPM Suite 公司站点。
2. 转到“系统设置”>“用户列表”>“新建用户”。 
3. 在“新建用户”对话框中，执行以下步骤： 
   
    ![创建测试用户][300] 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 Azure AD 中 Britta 的用户名。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“电子邮件”文本框中，键入 Azure AD 中 Britta 的用户名。
   
    c. 在“密码”文本框中，键入密码。

4. 单击“添加新用户”。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 Questetra BPM Suite 的权限，允许她使用 Azure 单一登录。

![什么是 Azure AD Connect][200]

**若要将 Britta Simon 分配到 Questetra BPM Suite，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![什么是 Azure AD Connect][201]
2. 在应用程序列表中，选择“Questetra BPM Suite”。
   
    ![什么是 Azure AD Connect][205]
3. 在顶部菜单中，单击“用户”。
   
    ![什么是 Azure AD Connect][202]
4. 在“用户”列表中，选择“Britta Simon”。
   
    ![什么是 Azure AD Connect][203]
5. 在底部工具栏中，单击“分配”。
   
    ![什么是 Azure AD Connect][204]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  
在访问面板中单击“Questetra BPM Suite”磁贴，即可自动登录 Questetra BPM Suite 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

