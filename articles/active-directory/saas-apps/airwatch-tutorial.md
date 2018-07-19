---
title: 教程：Azure Active Directory 与 AirWatch 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 AirWatch 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: f3bbcbb70759e7a995797cf89ad75a2a39314927
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048474"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>教程：Azure Active Directory 与 AirWatch 集成

在本教程中，了解如何将 AirWatch 与 Azure Active Directory (Azure AD) 集成。

将 AirWatch 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 AirWatch
- 可以让用户使用其 Azure AD 帐户自动登录到 AirWatch（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 AirWatch 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 AirWatch 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 AirWatch
2. 配置和测试 Azure AD 单一登录

## <a name="adding-airwatch-from-the-gallery"></a>从库添加 AirWatch
若要配置 AirWatch 与 Azure AD 的集成，需要将库中的 AirWatch 添加到托管的 SaaS 应用列表。

若要从库添加 AirWatch，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在“搜索框”中，键入“AirWatch”。

    ![创建 Azure AD 测试用户](./media/airwatch-tutorial/tutorial_airwatch_search.png)

5. 在结果窗格中，选择“AirWatch”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 AirWatch 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 AirWatch 用户。 换句话说，需要建立 Azure AD 用户与 AirWatch 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 AirWatch 中“用户名”的值，建立此链接关系。

若要配置和测试 AirWatch 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 AirWatch 测试用户](#creating-a-airwatch-test-user) - 在 AirWatch 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 AirWatch 应用程序中配置单一登录。

若要配置 AirWatch 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“AirWatch”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. 在“AirWatch 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/airwatch-tutorial/tutorial_airwatch_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. 在“标识符”文本框中，键入 `AirWatch` 值。

    > [!NOTE] 
    > 此值不是真实值。 使用实际登录 URL 更新此值。 若要获取此值，请与 [AirWatch 客户端支持团队](http://www.air-watch.com/company/contact-us/)联系。 
 
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。

    ![配置单一登录](./media/airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. 在“AirWatch 配置”部分中，单击“配置 AirWatch”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/airwatch-tutorial/tutorial_airwatch_configure.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/airwatch-tutorial/tutorial_general_400.png)
<CS>
7. 在其他 Web 浏览器窗口中，以管理员身份登录 AirWatch 公司站点。

8. 在左侧导航窗格中，单击“帐户”，并单击“管理员”。
   
   ![管理员](./media/airwatch-tutorial/ic791920.png "管理员")

9. 展开“设置”菜单，并单击“目录服务”。
   
   ![设置](./media/airwatch-tutorial/ic791921.png "设置")

10. 单击“用户”选项卡，在“基 DN”文本框中键入域名，然后单击“保存”。
   
   ![用户](./media/airwatch-tutorial/ic791922.png "用户")

11. 单击“服务器”选项卡。
   
   ![服务器](./media/airwatch-tutorial/ic791923.png "服务器")

12. 执行以下步骤：
    
    ![上传](./media/airwatch-tutorial/ic791924.png "上传")   
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 对于“目录类型”，选择“无”。

    b. 选择“将 SAML 用于身份验证”。

    c. 若要上传下载的证书，请单击“上传”。

13. 在“请求”部分中，执行以下步骤：
    
    ![请求](./media/airwatch-tutorial/ic791925.png "请求")  

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 对于“请求绑定类型”，请选择“POST”。

    b. 在 Azure 门户的“配置 Airwatch 的单一登录”对话框上，复制“SAML 单一登录服务 URL”值，然后将其粘贴到“标识提供程序单一登录 URL”文本框。

    c. 对于“NameID 格式”，请选择“电子邮件地址”。

    d. 单击“ **保存**”。

14. 再次单击“用户”选项卡。
    
    ![用户](./media/airwatch-tutorial/ic791926.png "用户")

15. 在“属性”部分中，执行以下步骤：
    
    ![属性](./media/airwatch-tutorial/ic791927.png "属性")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“对象标识符”文本框中，键入 **http://schemas.microsoft.com/identity/claims/objectidentifier**。

    b. 在“用户名”文本框中，键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。

    c. 在“显示名称”文本框中，键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。

    d. 在“名字”文本框中，键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。

    e. 在“姓氏”文本框中，键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。

    f. 在“电子邮件”文本框中，键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。

    g. 单击“ **保存**”。

<CE>

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/airwatch-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/airwatch-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/airwatch-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/airwatch-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 Britta Simon 的**电子邮件地址**。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-airwatch-test-user"></a>创建 AirWatch 测试用户

要使 Azure AD 用户能够登录 AirWatch，必须对其进行预配才能使其登录 AirWatch。

* 对于 AirWatch，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 **AirWatch** 公司站点。
2. 在左侧导航窗格中，单击“帐户”，并单击“用户”。
   
   ![用户](./media/airwatch-tutorial/ic791929.png "用户")
3. 在“用户”菜单中，单击“列表视图”，并依次单击“添加”\>“添加用户”。
   
   ![添加用户](./media/airwatch-tutorial/ic791930.png "添加用户")
4. 在“添加 / 编辑用户”对话框中，执行以下步骤：

   ![添加用户](./media/airwatch-tutorial/ic791931.png "添加用户")   
   1. 键入希望在相关文本框中预配的有效 Azure Active Directory 帐户的“用户名”、“密码”、“确认密码”、“名字”、“姓氏”、“电子邮件地址”。
   2. 单击“ **保存**”。

>[!NOTE]
>可使用其他任何 AirWatch 用户帐户创建工具或 AirWatch 提供的 API 预配 AAD 用户帐户。
>  

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 AirWatch 的访问权限，支持 Britta Simon 使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 AirWatch，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“AirWatch”。

    ![配置单一登录](./media/airwatch-tutorial/tutorial_airwatch_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/airwatch-tutorial/tutorial_general_01.png
[2]: ./media/airwatch-tutorial/tutorial_general_02.png
[3]: ./media/airwatch-tutorial/tutorial_general_03.png
[4]: ./media/airwatch-tutorial/tutorial_general_04.png

[100]: ./media/airwatch-tutorial/tutorial_general_100.png

[200]: ./media/airwatch-tutorial/tutorial_general_200.png
[201]: ./media/airwatch-tutorial/tutorial_general_201.png
[202]: ./media/airwatch-tutorial/tutorial_general_202.png
[203]: ./media/airwatch-tutorial/tutorial_general_203.png

