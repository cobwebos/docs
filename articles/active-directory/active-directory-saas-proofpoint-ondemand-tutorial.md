---
title: "教程：Azure Active Directory 与 Proofpoint on Demand 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Proofpoint on Demand 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7aa746b8c6f4b747a7dca0e9bf75e61b2707b7e1


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>教程：Azure Active Directory 与 Proofpoint on Demand 的集成
本教程介绍如何将 Proofpoint on Demand 与 Azure Active Directory (Azure AD) 集成。

将 Proofpoint on Demand 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Proofpoint on Demand
* 可以让用户使用其 Azure AD 帐户自动登录到 Proofpoint on Demand（单一登录，简称 SSO）。
* 可以在一个中心位置（即 Azure 经典门户）管理帐户。

如果想要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [What is application access and single sign-on with Azure Active Directory?](active-directory-appssoaccess-whatis.md)（什么是使用 Azure Active Directory 的应用程序访问和单一登录？）。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Proofpoint on Demand 的集成，需备齐以下项目：

* Azure AD 订阅
* Proofpoint on Demand 单一登录订阅

若要测试本教程中的步骤，请遵循以下建议：

* 除非必要，请勿使用生产环境。
* 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Proofpoint on Demand。
2. 配置和测试 Azure AD 单一登录。

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>从库中添加 Proofpoint on Demand
若要通过配置将 Proofpoint on Demand 集成到 Azure AD 中，需要从库中将 Proofpoint on Demand 添加到托管 SaaS 应用列表。

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory 图标][1]
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中单击“应用程序”。
   
    ![“应用程序”菜单项][2]
4. 在页面底部单击“添加”。
   
    ![“添加”按钮][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![“从库中添加应用程序”选项][4]
6. 在搜索框中，键入“Proofpoint on Demand”。
   
    ![在其中键入“Proofpoint on Demand”的框](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. 在结果窗格中，选择“Proofpoint on Demand”，然后单击“完成”添加该应用程序。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分需根据名为“Britta Simon”的测试用户的情况，使用 Proofpoint on Demand 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Proofpoint on Demand 用户。 换句话说，需要建立 Azure AD 用户与 Proofpoint on Demand 中相关用户的关联关系。

将 Azure AD 中“用户名”的值指定为 Proofpoint on Demand 中“用户名”的值即可建立此关联关系。

若要配置和测试 Proofpoint on Demand 的 Azure AD 单一登录，请完成以下过程：

1. [配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)，以使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)，目的是通过 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Proofpoint on Demand 测试用户](#creating-a-proofpoint-ondemand-test-user)，目的是在 Proofpoint on Demand 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. [分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#testing-single-sign-on)，目的是验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分需在经典门户中启用 Azure AD 单一登录，并在 Proofpoint on Demand 应用程序中配置单一登录。

1. 在经典门户中的“Proofpoint on Demand”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![“配置单一登录”按钮][6]
2. 在“你希望用户如何登录 Proofpoint on Demand”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![“Microsoft Azure AD 单一登录”选项按钮](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. 在“配置应用设置”页上，执行以下步骤：
   
    ![带有填充框的“配置应用设置”页](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”框中，键入用户用于登录 Proofpoint on Demand 应用程序的 URL。 请使用以下模式：**https://\<主机名\>.pphosted.com/ppssamlsp_hostname**
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“标识符”框中，使用以下模式键入 URL：**https://\<hostname/>.pphosted.com/ppssamlsp**
   
    c. 在“回复 URL”框中，使用以下模式键入 URL：**https://\<hostname/>.pphosted.com:portnumber/v1/samlauth/samlconsumer**
   
    d.单击“下一步”。 单击“下一步”。
4. 在“配置 Proofpoint on Demand 的单一登录”页上，执行以下步骤：
   
    ![带有“下载证书”按钮的“配置 Proofpoint on Demand 的单一登录”页](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“下一步”。
5. 若要为应用程序配置 SSO，请联系 Proofpoint on Demand 支持团队，向他们提供以下内容：
   
    • 下载的证书
   
    • 实体 ID
   
    • SAML SSO URL
6. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![用于确认已配置单一登录的复选框][10]
7. 在“单一登录确认”页上，单击“完成”。  
   
    ![“确认”页][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在经典门户中创建名为“Britta Simon”的测试用户。

![用户列表中的测试用户][20]

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory 图标](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![“用户”菜单项](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![“添加用户”按钮](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”页上，执行以下步骤： ![带填充框的“告诉我们有关此用户的信息”页](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“用户类型”框中，选择“组织中的新用户”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”页上，执行以下步骤：![带填充框的“用户配置文件”页](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”框中，键入“Britta”。  
   
   b.保留“数据库类型”设置，即设置为“共享”。 在“姓氏”框中，键入“Simon”。
   
   c. 在“显示名称”框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”页上，单击“创建”。
   
    ![用于创建临时密码的按钮](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. 在“获取临时密码”页上，执行以下步骤：
   
    ![带有密码信息的“获取临时密码”页](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”框中的值。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“完成”。   

### <a name="create-a-proofpoint-on-demand-test-user"></a>创建 Proofpoint on Demand 测试用户
在本部分中，将在 Proofpoint on Demand 中创建一个名为“Britta Simon”的用户。 请协助 Proofpoint on Demand 支持团队将用户添加到 Proofpoint on Demand 平台。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 Proofpoint on Demand 的权限，允许她使用 Azure 单一登录。

![用户信息，显示通过直接方法启用的访问][200]

1. 在经典门户的目录视图中，单击顶部菜单中的“应用程序”打开应用程序视图。
   
    ![“应用程序”菜单项][201]
2. 在应用程序列表中，选择“Proofpoint on Demand”。
   
    ![选中“Proofpoint on Demand”的应用程序列表](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. 在顶部菜单中，单击“用户”。
   
    ![“用户”菜单项][203]
4. 在用户列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![“分配”按钮][205]

### <a name="test-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Proofpoint on Demand”磁贴时，用户应自动登录到 Proofpoint on Demand 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO2-->


