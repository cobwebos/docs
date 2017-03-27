---
title: "教程：Azure Active Directory 与 Concur 的集成 | Microsoft 文档"
description: "了解如何将 Concur 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1eee0a5d-24fa-4986-9aef-3c543cfe3296
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 87f8547486d9c77aeaa49e574b3c05e1a1fd877a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-concur"></a>教程：Azure Active Directory 与 Concur 的集成
本教程的目标是展示 Azure 与 Concur 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Concur 中的租户

在本教程中概述的方案由以下构建基块组成：

* 为 Concur 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-concur-tutorial/IC769766.png "方案")

>[!NOTE]
>为 Concur 订阅配置基于 SAML 的联合 SSO 是一个单独的任务，必须联系 Concur 来执行。 
> 

## <a name="enable-the-application-integration-for-concur"></a>为 Concur 启用应用程序集成
本部分的目的是概述如何为 Concur 启用应用程序集成。

**若要为 Concur 启用应用程序集成，请执行以下步骤：**
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
  ![Active Directory](./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
  ![应用程序](./media/active-directory-saas-concur-tutorial/IC700994.png "应用程序")
4. 若要打开**应用程序库**，请单击“添加应用”，然后单击“为组织添加要使用的应用程序”。
   
  ![想要做什么？](./media/active-directory-saas-concur-tutorial/IC700995.png "想要做什么？")
5. 在**搜索框**中，键入“Concur”。
   
  ![应用程序库](./media/active-directory-saas-concur-tutorial/IC721727.png "应用程序库")
6. 在结果窗格中，选择“Concur”，然后单击“完成”以添加该应用程序。
   
  ![Concur](./media/active-directory-saas-concur-tutorial/IC721728.png "Concur")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Concur 证明自己的身份。

>[!NOTE]
>为 Concur 订阅配置基于 SAML 的联合 SSO 是一个单独的任务，必须联系 Concur 来执行。
> 

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Concur** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-concur-tutorial/IC769767.png "配置单一登录")
2. 在“你希望用户如何登录到 Concur”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-concur-tutorial/IC769768.png "配置单一登录")
3. 在“配置应用 URL”页上，在“Concur 登录 URL”文本框中，键入你的 Concur 租户登录 URL，然后单击“下一步”： 
   
   ![配置登录 URL](./media/active-directory-saas-concur-tutorial/IC769769.png "Configure sign in URL")
4. 在“在 Concur 处配置单一登录”页上，执行以下步骤。
   
   ![配置登录 URL](./media/active-directory-saas-concur-tutorial/IC769770.png "Configure sign in URL")
   1. 单击“下载元数据”，然后将数据文件保存在计算机上。
   2. 联系 Concur 支持团队来为你的租户配置 SSO。
   3. 选择单一登录配置确认，然后单击“完成”，关闭“配置单一登录”对话框。  
   
   >[!NOTE]
   >为 Concur 订阅配置基于 SAML 的联合 SSO 是一个单独的任务，必须联系 Concur 来执行。 
   > 

## <a name="configure-user-provisioning"></a>配置用户设置
本部分的目的是概述如何对 Concur 启用 Active Directory 用户帐户的预配。

若要在费用服务中启用应用，必须正确设置和使用 Web 服务管理配置文件。 不要简单地将 WS 管理角色添加到用于 T&E 管理功能的现有管理员配置文件。

Concur 顾问或客户管理员必须创建一个单独的 Web 服务管理员配置文件，并且客户管理员必须使用此配置文件执行 Web 服务管理员功能（例如，启用应用）。 这些配置文件必须独立于客户管理员的日常 T&E 管理配置文件（T&E 管理配置文件不应当分配 WSAdmin 角色）。

在创建用于启用应用的配置文件时，需要在用户配置文件字段中输入客户管理员的名称。 这将分配对配置文件的所有权。在创建配置文件后，客户必须使用此配置文件登录以在“Web 服务”菜单内针对合作伙伴应用单击“启用”按钮。

出于以下原因，不应当使用用于普通 T&E 管理的配置文件执行此操作。

1. 必须由客户在启用应用后显示的对话窗口上单击“是”。 此单击将确认客户同意合作伙伴应用程序访问其数据，因此你或合作伙伴无法单击该“是”按钮。
2. 如果使用 T&E 管理配置文件启用了应用的客户管理员离开了公司（导致该配置文件被停用），则使用该配置文件启用的任何应用都不再工作，直到使用另一个活动的 WS 管理配置文件启用该应用。 这就是建议你创建单独的 WS 管理配置文件的原因。
3. 如果管理员离开了公司，需要时可以将与 WS 管理配置文件关联的名称更改为替代管理员，这不会影响已启用的应用，因为不需要停用该配置文件。

**若要配置用户预配，请执行以下步骤：**

1. 登录到你的 **Concur** 租户。
2. 从“管理”菜单中，选择“Web 服务”。
   
   ![Concur 租户](./media/active-directory-saas-concur-tutorial/IC721729.png "Concur tenant")
3. 在左侧，从“Web 服务”窗格中，选择“启用合作伙伴应用程序”。
   
   ![启用合作伙伴应用程序](./media/active-directory-saas-concur-tutorial/IC721730.png "Enable Partner Application")
4. 从“启用应用程序”列表中，选择“Azure Active Directory”，然后单击“启用”。
   
   ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")
5. 单击“是”以关闭“确认操作”对话框。
   
   ![确认操作](./media/active-directory-saas-concur-tutorial/IC721732.png "Confirm Action")
6. 在 Azure 经典门户中，从应用程序列表中选择“Concur”以打开“Concur”对话框页。
7. 若要打开“配置用户预配”对话框页，请单击“配置用户预配”。
8. 输入 Concur 管理员的用户名和密码，然后单击“下一步”。
9. 若要完成配置，请在“确认”页上单击“完成”按钮。

现在可以创建一个测试帐户，等待 10 分钟，然后验证该帐户是否已同步到 Concur。

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Concur，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Concur** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-concur-tutorial/IC769771.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-concur-tutorial/IC767830.png "是")

现在应等待 10 分钟，然后验证该帐户是否已同步到 Concur。

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


