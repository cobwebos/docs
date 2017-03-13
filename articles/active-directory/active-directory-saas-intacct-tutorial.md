---
title: "教程：Azure Active Directory 与 Intacct 集成 | Microsoft Docs"
description: "了解如何使用 Intacct 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>教程：Azure Active Directory 与 Intacct 集成
本教程的目的是说明 Azure 与 Intacct 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Intacct 租户

完成本教程后，分配到 Intacct 的 Azure Active Directory (Azure AD) 用户将能够在 Intacct 公司站点上（服务提供商启动的登录）或者使用[访问面板](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Intacct 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-intacct-tutorial/IC790030.png "方案")

## <a name="enable-the-application-integration-for-intacct"></a>为 Intacct 启用应用程序集成
本部分的目的是概述如何为 Intacct 启用应用程序集成。

**若要为 Intacct 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左窗格中，单击“Active Directory”图标。

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

   ![应用程序](./media/active-directory-saas-intacct-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。

   ![添加应用程序](./media/active-directory-saas-intacct-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”页中，单击“从库中添加应用程序”。

   ![从库中添加应用程序](./media/active-directory-saas-intacct-tutorial/IC749322.png "从库中添加应用程序")
6. 在搜索框中，输入“Intacct”。

   ![应用程序库](./media/active-directory-saas-intacct-tutorial/IC790031.png "应用程序库")
7. 在结果窗格中，选择“Intacct”，然后单击“完成”以添加该应用程序。

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户通过他们在 Azure AD 中的帐户向 Intacct 进行身份验证。 使用基于 SAML 协议的联合身份验证进行此身份验证。  

在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Intacct”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”页。

   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790033.png "配置单一登录")
2. 在“你希望用户如何登录 Intacct”页上，选择“Windows Azure AD 单一登录”，然后单击“下一步”。

   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790034.png "配置单一登录")
3. 在“配置应用 URL”页上的“登录 URL”文本框中，输入使用“https://Intacct.com/company”模式的 URL，然后单击“下一步”。

   ![配置应用 URL](./media/active-directory-saas-intacct-tutorial/IC790035.png "配置应用 URL")
4. 在“配置 Intacct 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。

   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790036.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Intacct 公司站点。
6. 单击“公司”选项卡，然后单击“公司信息”。

   ![公司](./media/active-directory-saas-intacct-tutorial/IC790037.png "公司")
7. 单击“安全性”选项卡，然后单击“编辑”。

   ![安全](./media/active-directory-saas-intacct-tutorial/IC790038.png "安全")
8. 在“单一登录(SSO)”部分中，执行以下步骤：

   ![单一登录](./media/active-directory-saas-intacct-tutorial/IC790039.png "单一登录")

   1. 选择“启用单一登录”。
   2. 对于“标识提供者类型”，选择“SAML 2.0”。
   3. 在 Azure 经典门户的“配置 Intacct 的单一登录”页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者 URL”框中。
   4. 在 Azure 经典门户的“配置 Intacct 的单一登录”页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”框中。
   5. 基于下载的证书创建一个 **base-64 编码**的文件。 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。      
   6. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“证书”框中。
   7. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”页。

   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790040.png "配置单一登录")

## <a name="configure-user-provisioning"></a>配置用户设置

要设置 Azure AD 用户使其能够登录 Intacct，必须将这些用户预配到 Intacct 中。 对于 Intacct，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Intacct** 租户。
2. 单击“公司”选项卡，然后单击“用户”。

   ![用户](./media/active-directory-saas-intacct-tutorial/IC790041.png "用户")
3. 单击“添加”选项卡。

   ![添加](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")
4. 在“用户信息”部分中，执行以下步骤：

   ![用户信息](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")

   1. 在“用户信息”部分中输入要预配的 Azure AD 帐户的**用户 ID**、**姓氏**、**名字**、**电子邮件地址**、**职务**和**电话**。
   2. 选择要预配的 Azure AD 帐户的**管理员特权**。
   3. 单击“保存” 。 Azure AD 帐户持有者将收到一封电子邮件，并打开用于在激活帐户前确认其帐户的链接。

>[!NOTE]
>若要预配 Azure AD 用户帐户，可以使用任何其他 Intacct 用户帐户创建工具或 Intacct 提供的 API。
>

## <a name="assign-users"></a>分配用户
若要测试配置，需要将 Azure AD 用户分配到 Intacct。 分配用户之后，这些用户可以访问你的应用程序。

**若要将用户分配到 Intacct，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Intacct”应用程序集成页上，单击“分配用户”。

   ![分配用户](./media/active-directory-saas-intacct-tutorial/IC790044.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。

   ![是](./media/active-directory-saas-intacct-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Intacct 的权限，将她设置为使用 Azure 单一登录。

![分配用户][200]

**若要将 Britta Simon 分配到 Intacct，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图，转到目录视图，转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Intacct”。

    ![配置单一登录](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. 在“管理”菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮，然后在“添加分配”中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”的用户列表中，选择“Britta Simon”。

6. 在“用户和组”中，单击“选择”按钮。

7. 在“添加分配”中，单击“分配”按钮。



### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Intacct”磁贴时，用户应自动登录到 Intacct 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

