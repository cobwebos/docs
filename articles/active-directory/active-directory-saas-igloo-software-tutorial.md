---
title: "教程：Azure Active Directory 与 Igloo Software 集成 | Microsoft Docs"
description: "了解如何使用 Igloo Software 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 06e70434d61af0f4d704bd5bc95e3c30672e4c15
ms.openlocfilehash: 5711f9957f0c982f8193d07d536d6665c7a46ec1
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>教程：Azure Active Directory 与 Igloo Software 集成
本教程的目的是说明 Azure 与 Igloo Software 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 [Igloo Software](http://www.igloosoftware.com/) 单一登录的订阅

完成本教程后，分配到 Igloo Software 的 Azure AD 用户将能够在 Igloo Software 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)通过单一登录方式访问应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Igloo Software 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "方案")

## <a name="enable-the-application-integration-for-igloo-software"></a>为 Igloo Software 启用应用程序集成
本部分的目的是概述如何为 Igloo Software 启用应用程序集成。

**若要为 Igloo Software 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“Igloo Software”。
   
   ![应用程序库](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "应用程序库")
7. 在结果窗格中，选择“Igloo Software”，然后单击“完成”以添加该应用程序。
   
   ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
   
## <a name="configure-sso"></a>配置 SSO

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Igloo Software 进行身份验证。  

在此过程中，需要将 base-64 编码的证书上载到 Central Desktop 租户。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Igloo Software”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "配置单一登录")
2. 在“你希望用户如何登录 Igloo Software”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![Microsoft Azure AD 单一登录](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD 单一登录")
3. 在“配置应用 URL”页上的“Igloo Software 登录 URL”文本框中，使用模式“*https://company.igloocommunities.com/?signin*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "配置应用 URL")
4. 在“配置 Igloo Software 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件本地保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Igloo Software 公司站点。
6. 转到“控制面板”。
   
   ![控制面板](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "控制面板")
7. 在“成员身份”选项卡中，单击“登录设置”。
   
   ![登录设置](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "登录设置")
8. 在“SAML 配置”部分中，单击“配置 SAML 身份验证”。
   
   ![SAML 配置](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML Configuration")
9. 在“常规配置”部分中，执行以下步骤：
   
   ![常规配置](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "常规配置")
   1. 在“连接名称”文本框中，键入配置的自定义名称。
   2. 在 Azure 经典门户的“配置 Igloo Software 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“IdP 登录 URL”文本框中。
   3. 在 Azure 经典门户的“配置 Igloo Software 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“IdP 注销 URL”文本框中。
   4. 在**注销响应和请求 HTTP 类型**中，选择“POST”。
   5. 基于下载的证书创建文本文件。    
   
       >[!TIP]
       >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。 
       > 
   6. 从文本文件版本的证书中删除第一行和最后一行，复制剩余证书文本，然后将其粘贴到“公共证书”文本框中。
10. 在“响应和身份验证配置”中，执行以下步骤：
    
    ![响应和身份验证配置](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "响应和身份验证配置")
  1. 对于“标识提供者”，选择“Microsoft ADFS”。
  2. 对于“标识符类型”，选择“电子邮件地址”。
  3. 在“电子邮件属性”文本框中，键入“emailaddress”。
  4. 在“名字属性”文本框中，键入“givenname”。
  5. 在“姓氏属性”文本框中，键入“surname”。
11. 执行以下步骤以完成配置：
    
    ![登录时创建用户](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "登录时创建用户")   
  1. 对于“登录时创建用户”，选择“用户登录时在站点中创建新用户”。
  2. 对于“登录设置”，选择“使用‘登录’屏幕上的 SAML 按钮”。
  3. 单击“保存” 。
12. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

没有操作项可用于配置预配到 Igloo Software 的用户。  

当分配的用户尝试使用访问面板登录到 Igloo Software 时，Igloo Software 会检查该用户是否存在。  如果尚无用户帐户可用，Igloo software 将自动创建该用户。

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

**若要将用户分配到 Igloo Software，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“Igloo Software”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


