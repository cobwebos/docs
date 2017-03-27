---
title: "教程：Azure Active Directory 与 Cisco Spark 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Cisco Spark 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 931004d458fd0cbf3e002805ca701d777ddc57c2
ms.openlocfilehash: f0f12b1667b7f45fd164fac658502c93e8afb855
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>教程：Azure Active Directory 与 Cisco Spark 的集成
本教程介绍了如何将 Cisco Spark 与 Azure Active Directory (Azure AD) 进行集成。

将 Cisco Spark 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Cisco Spark
* 可以让用户使用其 Azure AD 帐户自动登录到 Cisco Spark 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Cisco Spark 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 **Cisco Spark** 单一登录 (SSO) 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Cisco Spark
2. 配置和测试 Azure AD SSO

## <a name="add-cisco-spark-from-the-gallery"></a>从库中添加 Cisco Spark
若要配置 Cisco Spark 与 Azure AD 的集成，需要从库中将 Cisco Spark 添加到托管 SaaS 应用列表。

**若要从库中添加 Cisco Spark，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Cisco Spark”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)
7. 在结果窗格中，选择“Cisco Spark”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Cisco Spark 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Cisco Spark 用户。 换句话说，需要在 Azure AD 用户与 Cisco Spark 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Cisco Spark 中“用户名”的值来建立此链接关系。 若要配置并测试 Cisco Spark 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Cisco Spark 测试用户](#creating-a-cisco-spark-test-user)** - 在 Cisco Spark 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO
本部分的目的是在 Azure 经典门户中启用 Azure AD SSO 并在 Cisco Spark 应用程序中配置单一登录。

Cisco Spark 应用程序需要 SAML 断言包含特定属性。 请为此应用程序配置以下属性。 可从应用程序的“属性”选项卡管理这些属性的值。 以下屏幕截图显示一个示例。

![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**若要配置 Cisco Spark 的 Azure AD SSO，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Cisco Spark** 应用程序集成页上，在顶部菜单中单击“属性”。
   
    ![配置单一登录][5]
2. 在“SAML 令牌属性”对话框中，执行以下步骤：
  1. 单击“添加用户属性”，打开“添加用户属性”对话框。
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
  2. 在“属性名称”文本框中，键入“uid”。
  3. 从“属性值”列表中，选择“user.userprincipal”。
  4. 单击“完成”。 然后，单击页面底部的“应用更改”。
3. 在顶部菜单中，单击“快速启动”。
   
    ![配置单一登录][6]
4. 在经典门户中，在 **Cisco Spark** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][7] 
5. 在“你希望用户如何登录到 Cisco Spark”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)
6. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)
  1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://web.ciscospark.com/#/signin`。
  2. 单击“下一步”。
7. 在“在 Cisco Spark 处配置单一登录”页上，单击“下载元数据”，然后将文件保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)
8. 使用你的完整管理员凭据登录到 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。
9. 选择“设置”，在“身份验证”部分下单击“修改”。
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
9. 选择“集成第三方标识提供者。(高级)”，然后转到下一个屏幕。
10. 单击“下载元数据文件”，然后将该文件保存在计算机上。
11. 在“导入 Idp 元数据”页上，将 Azure AD 元数据文件拖放到页面上，或者使用文件浏览器选项找到并上载 Azure AD 元数据文件。 然后，选择“元数据中要求证书由证书颁发机构签名(更安全)”并单击“下一步”。 
   
   ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)
12. 选择“测试 SSO 连接”，当新的浏览器标签页打开时，通过登录使用 Azure AD 进行身份验证。
13. 返回到 **Cisco Cloud Collaboration Management** 浏览器标签页。 如果测试成功，则选择“此测试成功。启用单一登录”，然后单击“下一步”。
14. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
15. 在“单一登录确认”页上，单击“完成”。  
    
     ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在经典门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 
  1. 在“用户类型”中，选择“你的组织中的新用户”。
  2. 在“用户名”文本框中，键入“BrittaSimon”。
  3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 
  1. 在“名字”文本框中，键入“Britta”。  
  2. 在“姓氏”文本框中，键入“Simon”。
  3. 在“显示名称”文本框中，键入“Britta Simon”。
  4. 在“角色”列表中，选择“用户”。
  5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 
  1. 写下“新密码”的值。
  2. 单击“完成”。   

### <a name="create-a-cisco-spark-test-user"></a>创建 Cisco Spark 测试用户
在本部分中，将在 Cisco Spark 中创建一个名为 Britta Simon 的用户。 在本部分中，将在 Cisco Spark 中创建一个名为 Britta Simon 的用户。

1. 使用你的完整管理员凭据转到 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。
2. 单击“用户”，然后单击“管理用户”。
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 
3. 在“管理用户”窗口中，选择“手动添加或修改用户”，然后单击“下一步”。
4. 选择“姓名和电子邮件地址”。 然后，如下所述填写文本框：
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
  1. 在“名字”文本框中，键入“Britta”。 
  2. 在“姓氏”文本框中，键入“Simon”。
  3. 在“电子邮件地址”文本框中，键入 **britta.simon@contoso.com**。
5. 单击加号以添加 Britta Simon。 然后单击“下一步”。
6. 在“为用户添加服务”窗口中，单击“保存”，然后单击“完成”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，将通过向 Britta Simon 授予对 Cisco Spark 的访问权限，使她能够使用 Azure SSO。

![分配用户][200] 

**若要将 Britta Simon 分配到 Cisco Spark，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Cisco Spark”。
   
    ![配置单一登录](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“所有用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD SSO 配置。

当在访问面板中单击 Cisco Spark 磁贴时，应当会自动登录到 Cisco Spark 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png

