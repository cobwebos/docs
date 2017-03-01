---
title: "教程：Azure Active Directory 与 Kudos 集成 | Microsoft Docs"
description: "了解如何使用 Kudos 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ea39ca12135fded44a8e70ec87616f79007e0200
ms.openlocfilehash: 79915c69e2d40529ec5ad96a632e2e5b2ac4dd5e
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>教程：Azure Active Directory 与 Kudos 集成
本教程的目的是说明 Azure 与 Kudos 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Kudos 租户

完成本教程后，已向 Kudos 分配的 Azure AD 用户将能够在 Kudos 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Kudos 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-kudos-tutorial/IC787799.png "方案")

## <a name="enabling-the-application-integration-for-kudos"></a>为 Kudos 启用应用程序集成
本部分的目的是概述如何为 Kudos 启用应用程序集成。

**若要为 Kudos 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-kudos-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-kudos-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-kudos-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Kudos”。
   
   ![应用程序库](./media/active-directory-saas-kudos-tutorial/IC787800.png "应用程序库")
7. 在结果窗格中，选择“Kudos”，然后单击“完成”以添加该应用程序。
   
   ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Kudos 进行身份验证。  

在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Kudos”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-kudos-tutorial/IC787802.png "配置单一登录")
2. 在“你希望用户如何登录 Kudos”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-kudos-tutorial/IC787803.png "配置单一登录")
3. 在“配置应用 URL”页上的“Kudos 登录 URL”文本框中，使用模式 **https://company.kudosnow.com** 键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-kudos-tutorial/IC787804.png "配置应用 URL")
4. 在“配置 Kudos 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-kudos-tutorial/IC787805.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Kudos 公司站点。
6. 在顶部菜单中，单击“设置”。
   
   ![设置](./media/active-directory-saas-kudos-tutorial/IC787806.png "设置")
7. 单击“集成”\>“SSO”。
8. 在“SSO”部分中，执行以下步骤：
   
   ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")
   
   1. 在 Azure 经典门户的“配置 Kudos 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“登录 URL”文本框中。
   2. 基于下载的证书创建一个 **base-64 编码**的文件。 
   
      >[!TIP]
      >有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o) 
      > 
   3. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“X.509 证书”文本框中
   4. 在 Azure 经典门户的“配置 Kudos 的单一登录”对话框页上，复制“单一注销服务 URL”值，然后将其粘贴到“注销 URL”文本框中。
   5. 在“Kudos URL”文本框中，键入你的公司名称。
   6. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-kudos-tutorial/IC787808.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 Kudos，必须将这些用户预配到 Kudos 中。 

* 对于 Kudos，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **Kudos** 公司站点。
2. 在顶部菜单中，单击“设置”。
   
   ![设置](./media/active-directory-saas-kudos-tutorial/IC787806.png "设置")
3. 单击“用户管理员”。
4. 单击“用户”选项卡，然后单击“添加用户”。
   
   ![用户管理员](./media/active-directory-saas-kudos-tutorial/IC787809.png "用户管理员")
5. 在“添加用户”部分中，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-kudos-tutorial/IC787810.png "添加用户")
   
  1. 在相关文本框中键入要预配的有效 Azure Active Directory 帐户的“名字”、“姓氏”、“电子邮件地址”和其他详细信息。
   2. 单击“创建用户”。

>[!NOTE]
>可以使用 Kudos 提供的任何其他 Kudos 用户帐户创建工具或 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Kudos，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Kudos”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-kudos-tutorial/IC787811.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-kudos-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


