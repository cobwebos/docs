---
title: "教程：Azure Active Directory 与 Directions on Microsoft 的集成 | Microsoft 文档"
description: "了解如何将 Directions on Microsoft 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: efea82512dad3f2e5d1c0dd45e9e2f6db9a283d4
ms.openlocfilehash: a5538e89b1c23e4a2ad45c5515de8167ef95b46a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>教程：Azure Active Directory 与 Directions on Microsoft 的集成
本教程的目标是展示 Azure Active Directory 与 Directions on Microsoft 的集成。  

在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Directions on Microsoft 订阅

如果尚没有联合 Directions on Microsoft 订阅，请通过电子邮件向 **service@DirectionsOnMicrosoft.com** 发送请求。

完成本教程后，分配到 Directions on Microsoft 的 Azure Active Directory 用户将能够以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Directions on Microsoft 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "方案")

## <a name="enable-the-application-integration-for-directions-on-microsoft"></a>为 Directions on Microsoft 启用应用程序集成
本部分的目的是概述如何为 Directions on Microsoft 启用应用程序集成。

**若要为 Directions on Microsoft 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Directions on Microsoft”。
   
   ![应用程序库](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "应用程序库")
7. 在结果窗格中，选择“Directions on Microsoft”，然后单击“完成”以添加该应用程序。
   
   ![方案](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "方案")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Directions on Microsoft 证明自己的身份。

**若要配置 SSO，请执行以下步骤：**

1. 在 Azure 经典门户的“Directions on Microsoft”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![启用单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Enable Single Sign-On")
2. 在“你希望用户如何登录到 Directions on Microsoft”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![Microsoft Azure AD 单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel Sign-On")
3. 在“配置应用 URL”页上，在“登录 URL”文本框中，键入 **https://www.directionsonmicrosoft.com/user/login**，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "配置应用 URL")
4. 在“在 Directions on Microsoft 处配置单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "配置单一登录")
5. 将元数据文件发送给 Directions on Microsoft 支持团队 (*service@DirectionsOnMicrosoft.com*)。 若要使 Directions on Microsoft 支持团队能够找到你的联合网站成员资格，请在电子邮件中提供你的公司信息。
   
   >[!NOTE]
   >Directions on Microsoft 的单一登录需要由 Directions on Microsoft 支持团队来启用。 当启用单一登录后，你将收到通知。 
   > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
  ![配置单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

没有操作项可供你用来配置 Directions on Microsoft 的用户预配。  

当已分配的用户尝试使用访问面板登录到 Directions on Microsoft 时，Directions on Microsoft 会检查该用户是否存在。 如果尚无用户帐户可用，Directions on Microsoft 将自动创建该帐户。

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Directions on Microsoft，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Directions on Microsoft”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "是")


