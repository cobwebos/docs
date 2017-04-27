---
title: "教程：Azure Active Directory 与 ScreenSteps 集成 | Microsoft Docs"
description: "了解如何使用 ScreenSteps 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 7fbb8874367bda9be618332947ffa2c9942fe4ec
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>教程：Azure Active Directory 与 ScreenSteps 集成
本教程的目的是说明 Azure 与 ScreenSteps 的集成。
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* ScreenSteps 租户

完成本教程后，已向 ScreenSteps 分配的 Azure AD 用户将能够使用单一登录 (SSO)，在 ScreenSteps 公司站点（服务提供商发起的登录）处或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 ScreenSteps 启用应用程序集成
2. 配置单一登录 (SSO) 
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-screensteps-tutorial/IC778516.png "方案")

## <a name="enable-the-application-integration-for-screensteps"></a>为 ScreenSteps 启用应用程序集成
本部分的目的是概述如何为 ScreenSteps 启用应用程序集成。

**若要为 ScreenSteps 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![应用程序](./media/active-directory-saas-screensteps-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。

    ![添加应用程序](./media/active-directory-saas-screensteps-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

    ![从库添加应用程序](./media/active-directory-saas-screensteps-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“ScreenSteps”。

    ![应用程序库](./media/active-directory-saas-screensteps-tutorial/IC778517.png "应用程序库")
7. 在结果窗格中，选择“ScreenSteps”，然后单击“完成”以添加该应用程序。

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configure-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 ScreenSteps 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“ScreenSteps”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。

    ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778519.png "配置单一登录")
2. 在“你希望用户如何登录 ScreenSteps”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。

    ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778520.png "配置单一登录")

3. 在另一个 Web 浏览器窗口中，以管理员身份登录 ScreenSteps 公司站点。

4. 单击“帐户管理”。

    ![帐户管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "帐户管理")

5. 单击“单一登录”。

    ![远程身份验证](./media/active-directory-saas-screensteps-tutorial/IC778524.png "远程身份验证")

6. 单击“创建单一登录终结点”。

    ![远程身份验证](./media/active-directory-saas-screensteps-tutorial/IC778525.png "远程身份验证")

7. 在“创建单一登录终结点”部分中，执行以下步骤：

    ![创建身份验证终结点](./media/active-directory-saas-screensteps-tutorial/IC778526.png "创建身份验证终结点")

    1. 在“标题”文本框中，键入标题。
    2. 从“模式”列表中，选择“SAML”。
    3. 单击“创建” 。

8. 编辑新的终结点。

    ![编辑终结点](./media/active-directory-saas-screensteps-tutorial/IC778528.png "Edit endpoint")

9. 在“编辑单一登录终结点”部分中，执行以下步骤：

    ![远程身份验证终结点](./media/active-directory-saas-screensteps-tutorial/IC778527.png "远程身份验证终结点")

    1. 将 **SAML 使用者 URL**复制到剪贴板。

10. 在 Azure 经典门户的“配置应用 URL”页上，在“ScreenSteps 登录 URL”文本框中，粘贴 **SAML 使用者 URL**，然后单击“下一步”。

    ![配置应用 URL](./media/active-directory-saas-screensteps-tutorial/IC778521.png "配置应用 URL")

11. 在“配置 ScreenSteps 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。

    ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778522.png "配置单一登录")


12. 返回“编辑单一登录终结点”部分，并执行以下步骤：

    ![远程身份验证终结点](./media/active-directory-saas-screensteps-tutorial/IC778527.png "远程身份验证终结点")

    1. 单击“上传新的 SAML 证书文件”，然后上传下载的证书。
    2. 在 Azure 经典门户的“配置 ScreenSteps 的单一登录”页上，复制“远程登录 URL”值，然后将其粘贴到“远程登录 URL”文本框中。
    3. 在 Azure 经典门户的“配置 ScreenSteps 的单一登录”页上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
    4. 选择用户预配后将被分配到的**组**。
    5. 单击“更新”。
    6. 返回“编辑单一登录终结点”。
    7. 单击“对帐户设为默认”按钮，对登录到 ScreenSteps 的所有用户使用此终结点。 或者可单击“添加到站点”按钮，对 **ScreenSteps** 中的特定站点使用此终结点。


12. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。

    ![配置单一登录](./media/active-directory-saas-screensteps-tutorial/IC778542.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置



## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 ScreenSteps，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“ScreenSteps”应用程序集成页上，单击“分配用户”。

    ![分配用户](./media/active-directory-saas-screensteps-tutorial/IC778548.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。


如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


