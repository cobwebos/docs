---
title: "教程：Azure Active Directory 与 SCC LifeCycle 集成 | Microsoft Docs"
description: "了解如何使用 SCC LifeCycle 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 9a30bcca720ff135d0180d73f46e78403e9bca43
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>教程：Azure Active Directory 与 SCC LifeCycle 集成
本教程的目的是说明 Azure 与 SCC LifeCycle 的集成。  

在本教程中概述的方案假定已有以下各项：

* 一个有效的 Azure 订阅
* 已启用 SCC LifeCycle 单一登录 (SSO) 的订阅

完成本教程后，已向 SCC LifeCycle 分配的 Azure AD 用户能够在 SCC LifeCycle 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 SCC LifeCycle 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "方案")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>为 SCC LifeCycle 启用应用程序集成
本部分的目的是概述如何为 SCC LifeCycle 启用应用程序集成。

**若要为 SCC LifeCycle 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“SCC LifeCycle”。
   
    ![应用程序库](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "应用程序库")
7. 在结果窗格中，选择“SCC LifeCycle”，并单击“完成”以添加该应用程序。
   
    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 SCC LifeCycle 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“SCC LifeCycle”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "配置单一登录")
2. 在“你希望用户如何登录 SCC LifeCycle”页上，选择“Microsoft Azure AD 单一登录”，并单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "配置单一登录")
3. 在“配置应用 URL”页上的“登录 URL”文本框中，使用以下模式键入用户用于登录 SCC LifeCycle 应用程序的 URL：*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*，并单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "配置应用 URL")
4. 在“配置 SCC LifeCycle 的单一登录”页上，单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "配置单一登录")
5. 将该元数据文件转发给 SCC LifeCycle 支持团队。
   
   >[!NOTE]
   >单一登录必须由 SCC LifeCycle 支持团队来启用。
   > 
   > 

6. 在 Azure 经典门户中，选择“单一登录配置确认”，并单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 SCC LifeCycle，必须将这些用户预配到 SCC LifeCycle 中。 没有相关操作项，因此无法通过配置将用户预配到 SCC LifeCycle。

当已分配的用户尝试登录到 SCC LifeCycle 时，系统会根据需要自动创建 SCC LifeCycle 帐户。

>[!NOTE]
>可以使用任何其他 SCC LifeCycle 用户帐户创建工具或 SCC LifeCycle 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**要将用户分配到 SCC LifeCycle，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“SCC LifeCycle”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "分配用户")
3. 选择测试用户，单击“分配”，并单击“是”确认分配。
   
    ![是](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

