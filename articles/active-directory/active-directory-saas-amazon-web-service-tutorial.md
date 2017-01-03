---
title: "教程：Azure Active Directory 与 Amazon Web Services (AWS) 集成 | Microsoft Docs"
description: "了解如何将 Amazon Web Services (AWS) 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bc60476253fd3e646fbd08cd6749e06fb403959c
ms.openlocfilehash: 7ce638ae0cdca9f6114acae458a2cdea38a7793f


---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>教程：Azure Active Directory 与 Amazon Web Services (AWS) 集成
本教程旨在介绍如何将 Amazon Web Services (AWS) 与 Azure Active Directory (Azure AD) 集成。  
将 Amazon Web Services (AWS) 与 Azure AD 集成提供以下优势： 

* 可在 Azure AD 中控制谁有权访问 Amazon Web Services (AWS) 
* 可使用户通过其 Azure AD 帐户自动登录 Amazon Web Services (AWS)（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Amazon Web Services (AWS) 的集成，需要具有以下项：

* Azure AD 订阅
* 已启用 Amazon Web Services (AWS) 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
本教程中概述的方案包括三个主要构建基块：

1. 从库中添加 Amazon Web Services (AWS) 
2. 配置和测试 Azure AD 单一登录

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>从库中添加 Amazon Web Services (AWS)
若要配置 Amazon Web Services (AWS) 与 Azure AD 的集成，需要从库将 Amazon Web Services (AWS) 添加到托管 SaaS 应用列表。

### <a name="to-add-amazon-web-services-aws-from-the-gallery-perform-the-following-steps"></a>若要从库中添加 Amazon Web Services (AWS)，请执行以下步骤：
1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1] 

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。 
   
    ![应用程序][2]

4. 在页面底部单击“添加”。 
   
    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。 
   
    ![应用程序][4]

6. 在搜索框中，键入 **Amazon Web Services (AWS)**。
   
    ![应用程序][5]

7. 在“结果”窗格中，选择“Amazon Web Services (AWS)”，然后单击“完成”，添加该应用程序。
   
    ![应用程序][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分旨在介绍如何基于名为“Britta Simon”的测试用户，配置和测试 Amazon Web Services (AWS) 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要了解 Amazon Web Services (AWS) 中与 Azure AD 的用户对应的用户。 换句话说，需在 Azure AD 用户与 Amazon Web Services (AWS) 中的相关用户间建立链接关系。  
可以通过将 Azure AD 中的“用户名”值分配为 Amazon Web Services (AWS) 中“用户名”的值来建立此链接关系。

若要配置和测试 Amazon Web Services (AWS) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Amazon Web Services (AWS) 测试用户](#creating-a-halogen-software-test-user)** - 在 Amazon Web Service (AWS) 中创建 Britta Simon 的对应用户，并将其链接到她在 Azure AD 中的代表。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-single-sign-on"></a>配置 Azure AD 单一登录
本部分要在 Azure 经典门户中启用 Azure AD 单一登录，并在 Amazon Web Services (AWS) 应用程序中配置单一登录。  
Amazon Web Services (AWS) 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 **SAML 令牌属性**配置。 以下屏幕截图显示一个示例。

![配置单一登录][27]

**若要配置 Amazon Web Services (AWS) 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Amazon Web Services (AWS)”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][7]

2. 在“你希望用户如何登录 Amazon Web Services (AWS)”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录][8]

3. 在“配置应用设置”对话框页上，单击“下一步”。 
   
    ![配置应用设置][9]

4. 在“配置 Amazon Web Services (AWS) 的单一登录”页面上，单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
    ![配置单一登录][10]

5. 在其他浏览器窗口中，以管理员身份登录 Amazon Web Services (AWS) 公司站点。

6. 单击“控制台主页”。
   
    ![配置单一登录][11]

7. 单击“标识和访问管理”。 
   
    ![配置单一登录][12]

8. 单击“标识提供者”，然后单击“创建提供者”。 
   
    ![配置单一登录][13]

9. 在“配置提供者”对话框页上，执行以下步骤： 
   
    ![配置单一登录][14]
   
     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“提供者类型”，请选择“SAML”。
   
     b. 在“提供者名称”文本框中，键入提供者名称（例如：*WAAD*）。
   
     c. 若要上传下载的元数据文件，请单击“选择文件”。
   
     d.单击“下一步”。 单击“下一步”。

10. 在“验证提供者信息”对话框页上，单击“创建”。 
    
    ![配置单一登录][15]

11. 单击“角色”，然后单击“创建新角色”。 
    
    ![配置单一登录][16]

12. 在“设置角色名称”对话框中，执行以下步骤： 
    
    ![配置单一登录][17]
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“角色名称”文本框中，键入角色名称（例如：*TestUser*）。
    
    b. 单击“下一步”。

13. 在“选择角色类型”对话框中，执行以下步骤： 
    
    ![配置单一登录][18]
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“标识提供者角色的访问权限”。
    
    b. 在**授予 SAML 提供程序的 Web 单一访问 (WebSSO) 访问权限**部分中，单击“选择”。

14. 在“建立信任”对话框中，执行以下步骤：  
    
    ![配置单一登录][19]
    
     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于 SAML 提供者，选择之前创建的 SAML 提供者（例如：*WAAD*） 
    
     b. 单击“下一步”。

15. 在“验证角色信任”对话框中，单击“下一步”。 
    
    ![配置单一登录][32]

16. 在“附加策略”对话框中，单击“下一步”。  
    
    ![配置单一登录][33]

17. 在“审阅”对话框中，执行以下步骤：   
    
    ![配置单一登录][34]
    
     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 复制“角色 ARN”值。
    
     b. 复制“受信任实体”ARN 值。
    
     c. 单击“创建角色”。 

18. 在 Azure 经典门户上，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![什么是 Azure AD Connect][20]

19. 在“单一登录确认”页面上，单击“完成”，关闭“配置单一登录”对话框。
    
    ![什么是 Azure AD Connect][22]

20. 在顶部菜单中，单击“属性”，打开“SAML 令牌属性”对话框。 
    
    ![配置单一登录][21]

21. 单击“添加用户属性”。 
    
    ![配置单一登录][23]

22. 在“添加用户属性”对话框中，执行以下步骤。 
    
    ![配置单一登录][24] 
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“属性名称”文本框中，键入“https://aws.amazon.com/SAML/Attributes/Role”。
    
    b. 在“属性值”文本框中，键入“[角色 ARN 值]、[受信任实体 ARN 值]”。
    
    > [!TIP]
    > 这些都是在创建角色时从“审阅”对话框中复制的值。 
    > 
    > 
    
    c. 单击“完成”，关闭“添加用户属性”对话框。

23. 单击“添加用户属性”。 
    
    ![配置单一登录][23]

24. 在“添加用户属性”对话框中，执行以下步骤。 
    
    ![配置单一登录][25]

     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“属性名称”文本框中，键入“https://aws.amazon.com/SAML/Attributes/RoleSessionName”。

     b. 在“属性值”文本框中，键入或从下拉列表中选择“user.userprincipalname”。

    ![配置单一登录][35]

     c. 单击“完成”以关闭“添加用户属性”对话框。

1. 单击“应用更改”。 
   
 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分要在 Azure 经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。

    b. 在“用户名”文本框中，键入“BrittaSimon”。

    c. 单击“下一步”。

6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入 **Simon**。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-amazon-web-services-aws-test-user"></a>创建 Amazon Web Services (AWS) 测试用户
本部分要在 Amazon Web Services (AWS) 中创建名为“Britta Simon”的测试用户。

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-services-aws-perform-the-following-steps"></a>若要在 Amazon Web Services (AWS) 中创建名为“Britta Simon”的用户，请执行以下步骤：
1. 以管理员身份登录 **Amazon Web Services (AWS)** 公司站点。

2. 单击“控制台主页”图标。 
   
    ![配置单一登录][11]

3. 单击“标识和访问管理”。 
   
    ![配置单一登录][28]

4. 在仪表板中，单击“用户”，然后单击“创建新用户”。 
   
    ![配置单一登录][29]

5. 在“创建用户”对话框页上，执行以下步骤： 
   
    ![配置单一登录][30]
   
     a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“输入用户名称”文本框中，键入 Brita Simon 在 Azure AD 中的用户名 (userprincipalname)。
   
     b. 单击“创建” 。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 Amazon Web Services (AWS) 的权限，使她可使用 Azure 单一登录。

![分配用户][31]

**若要将 Britta Simon 分配到 Amazon Web Services (AWS)，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][26]

2. 在应用程序列表中，选择“Amazon Web Services (AWS)”。
   
    ![分配用户][27]

3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][25]

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][29]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。  
在访问面板中单击“Amazon Web Services (AWS)”磁贴时，应该会自动登录 Amazon Web Services (AWS) 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service-tutorial/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service-tutorial/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/user_attributes_01.png

























<!--HONumber=Dec16_HO5-->


