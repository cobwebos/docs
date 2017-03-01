---
title: "教程：Azure Active Directory 与 Huddle 集成 | Microsoft Docs"
description: "了解如何使用 Huddle 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 65188de42470fc1b6c8b3ef5c0ef65b5b455c683
ms.openlocfilehash: 752cc3c6abaecfcc7fd8bcf7462102336ebaa97b
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>教程：Azure Active Directory 与 Huddle 集成
本教程的目的是说明 Azure 与 Huddle 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Huddle 单一登录 (SSO) 的订阅

完成本教程后，已向 Huddle 分配的 Azure AD 用户将能够在 Huddle 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Huddle 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![配置单一登录](./media/active-directory-saas-huddle-tutorial/IC787830.png "配置单一登录")

## <a name="enable-the-application-integration-for-huddle"></a>为 Huddle 启用应用程序集成
本部分的目的是概述如何为 Huddle 启用应用程序集成。

**若要为 Huddle 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-huddle-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-huddle-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-huddle-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“Huddle”。
   
   ![应用程序库](./media/active-directory-saas-huddle-tutorial/IC787831.png "应用程序库")
7. 在结果窗格中，选择“Huddle”，然后单击“完成”以添加该应用程序。
   
   ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
   
## <a name="configure-single-sign-on"></a>配置单一登录

此部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Huddle 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Huddle”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-huddle-tutorial/IC787833.png "配置单一登录")
2. 在“你希望用户如何登录 Huddle”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-huddle-tutorial/IC787834.png "配置单一登录")
3. 在“配置应用 URL”页上的“Huddle 登录 URL”文本框中，使用模式“*http://company.huddle.com*”键入 Huddle 租户的 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-huddle-tutorial/IC787835.png "配置应用 URL")
4. 在“配置 Huddle 的单一登录”页上，执行以下步骤：
   
   ![配置单一登录](./media/active-directory-saas-huddle-tutorial/IC787836.png "配置单一登录")
   
   1. 单击“下载证书”，然后将证书文件保存在计算机上。
   2. 复制“颁发者 URL”值、“SAML SSO URL”值和下载的证书，然后将其发送给 Huddle 支持团队。  
   
    >[!NOTE]
    >单一登录需要由 Huddle 支持团队启用。 配置完成时，你将收到通知。 
    > 
5. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-huddle-tutorial/IC787837.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 Huddle，必须将这些用户预配到 Huddle 中。 对于 Huddle，预配是一项手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Huddle** 公司站点。
2. 单击“工作区”。
3. 单击“人员”**\>“邀请人员”**。
   
   ![人员](./media/active-directory-saas-huddle-tutorial/IC787838.png "人员")
4. 在“创建新邀请”部分中，执行以下步骤：
   
   ![新建邀请](./media/active-directory-saas-huddle-tutorial/IC787839.png "New Invitation")
   
   1. 在“选择要邀请人员加入的团队”列表中，选择“团队”。
   2. 在相关文本框中键入要预配的有效 AAD 帐户的“电子邮件地址”。
   3. 单击“邀请”。   
   
    >[!NOTE]
    >Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。 
    > 

>[!NOTE]
>可以使用 Huddle 提供的任何其他 Huddle 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Huddle，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Huddle”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-huddle-tutorial/IC787840.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-huddle-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


