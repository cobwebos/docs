---
title: "教程：Azure Active Directory 与 Syncplicity 集成 | Microsoft 文档"
description: "了解如何使用 Syncplicity 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 044769ca5a74b90843cb29510b66d9ce7e90566c
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>教程：Azure Active Directory 与 Syncplicity 集成
本教程的目的是介绍如何在 Azure Active Directory (Azure AD) 和 Syncplicity 之间设置单一登录。

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Syncplicity 租户

完成本教程后，已向 Syncplicity 分配的 Azure AD 用户将能够在 Syncplicity 公司站点（服务提供商发起的登录）或使用 Azure AD 访问面板单一登录到应用程序。

1. 为 Syncplicity 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "方案")

## <a name="enable-the-application-integration-for-syncplicity"></a>为 Syncplicity 启用应用程序集成
本部分的目的是概述如何为 Syncplicity 启用应用程序集成。

**若要为 Syncplicity 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "从库添加应用程序")

6. 在**搜索框**中，键入“Syncplicity”。
   
    ![Syncplicity 应用程序库](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Syncplicity 应用程序库")

7. 在结果窗格中，选择“Syncplicity”，然后单击“完成”以添加该应用程序。
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

## <a name="configure-single-sign-on"></a>配置单一登录
本部分概述如何让用户使用基于 SAML 协议的联合身份验证通过其在 Azure Active Directory 中的帐户向 Syncplicity 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Syncplicity”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "配置单一登录")

2. 在“你希望用户如何登录 Syncplicity”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![Microsoft Azure AD 单一登录](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD 单一登录")

3. 在“配置应用 URL”页上，在“Syncplicity 登录 URL”文本框中，键入用户用于登录 Syncplicity 应用程序的 URL，然后单击“下一步”。 
   
    应用 URL 是 Syncplicity 租户 URL（例如：*http://company.Syncplicity.com*）：
   
    ![配置应用 URL](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "配置应用 URL")

4. 在“配置 Syncplicity 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "配置单一登录")

5. 登录到 **Syncplicity** 租户。

6. 在顶部菜单中，单击“管理员”，选择“设置”，然后单击“自定义域和单一登录”。
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7. 在“单一登录(SSO)”对话框页上，执行以下步骤：
   
    ![单一登录\(\)SSO](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \\\(SSO\\\)")   
  1. 在“自定义域”文本框中，键入域名。
  2. 选择“已启用”作为“单一登录状态”。
  3. 在 Azure 经典门户中的“配置 Syncplicity 的单一登录”对话框页上，复制“实体 ID”值，然后将其粘贴到“实体 ID”文本框中。
  4. 在 Azure 经典门户的“配置 Syncplicity 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“登录页 URL”文本框中。
  5. 在 Azure 经典门户的“配置 Syncplicity 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“远程页 URL”文本框中。
  6. 在“标识提供者证书”中，单击“选择文件”，然后上载已从 Azure 经典门户下载的证书。 
  7. 单击“保存更改”。

8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![确认](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "确认")

## <a name="configure-user-provisioning"></a>配置用户设置
要使 AAD 用户能够登录，必须将这些用户预配到 Syncplicity 应用程序中。 本部分介绍如何在 Syncplicity 中创建 AAD 用户帐户。

**若要将用户帐户预配到 Syncplicity，请执行以下步骤：**

1. 登录到 **Syncplicity** 租户（例如：*https://company.Syncplicity.com*）。

2. 单击“管理员”，然后选择“用户帐户”。

3. 单击“添加用户”。
   
    ![管理用户](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "管理用户")

4. 键入要预配的 AAD 帐户的**电子邮件地址**，选择“用户”作为**角色**，然后单击“下一步”。
   
    ![帐户信息](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "帐户信息")
   
    >[!NOTE]
    >AAD 帐户持有者将收到一封电子邮件，其中包含用于确认和激活帐户的链接。 
    > 

5. 选择新用户应属于你的公司中的哪个组，然后单击“下一步”。
   
    ![组成员身份](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "组成员身份")
   
    >[!NOTE]
    >如果没有列出任何组，直接单击“下一步”。 
    > 

6. 在用户的计算机上选择要置于 Syncplicity 的控制下的文件夹，然后单击“下一步”。
   
    ![Syncplicity 文件夹](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Syncplicity 文件夹")

>[!NOTE]
>可以使用 Syncplicity 提供的任何其他 Syncplicity 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Syncplicity，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。

2. 在“Syncplicity”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


