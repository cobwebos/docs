---
title: "教程：Azure Active Directory 与 Abintegro 的集成 | Microsoft 文档"
description: "了解如何使用 Abintegro 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 99287e1f-4189-494a-97c8-e1c03d047fd3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ddbe54eb6f9565e0c42d138231dbde22592e75b5
ms.openlocfilehash: d6191e01450859a5e91b815df84d9a024bbee2c3
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-abintegro"></a>教程：Azure Active Directory 与 Abintegro 的集成
本教程的目的是说明 Azure 与 Abintegro 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Abintegro 单一登录 (SSO) 的订阅

完成本教程后，分配到 Abintegro 的 Azure AD 用户将能够在 Abintegro 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Abintegro 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-abintegro-tutorial/IC790076.png "方案")

## <a name="enable-the-application-integration-for-abintegro"></a>为 Abintegro 启用应用程序集成
本部分旨在概述如何为 Abintegro 启用应用程序集成。

**若要为 Abintegro 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-abintegro-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-abintegro-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-abintegro-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索框”中，键入 **abintegro**。
   
   ![应用程序库](./media/active-directory-saas-abintegro-tutorial/IC790077.png "应用程序库")
7. 在结果窗格中，选择“Abintegro”，然后单击“完成”以添加该应用程序。
   
   ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用基于 SAML 协议的联合身份验证，使用户通过其 Azure AD 中的帐户对 Abintegro 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Abintegro”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-abintegro-tutorial/IC790079.png "配置单一登录")
2. 在“你希望用户如何登录 Abintegro”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-abintegro-tutorial/IC790080.png "配置单一登录")
3. 在“配置应用 URL”页的“Abintegro 登录 URL”文本框中，键入用户用于登录 Abintegro 的 URL（例如 `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-abintegro-tutorial/IC790081.png "配置应用 URL")
4. 在“配置 Abintegro 的单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-abintegro-tutorial/IC790082.png "配置单一登录")
5. 将元数据文件发送给 Abintegro 支持团队。
   
   >[!NOTE]
   >单一登录配置必须由 Abintegro 支持团队执行。 配置完成后会立即收到通知。
   >  
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
  ![配置单一登录](./media/active-directory-saas-abintegro-tutorial/IC790083.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

没有相关操作项，因此无法通过配置将用户预配到 Abintegro。  
当已分配的用户尝试使用访问面板登录到 Abintegro 时，Abintegro 会检查该用户是否存在。  

如果尚无用户帐户可用，Abintegro 将自动创建该帐户。

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Abintegro，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Abintegro”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-abintegro-tutorial/IC790084.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-abintegro-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


