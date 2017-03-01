---
title: "教程：Azure Active Directory 与 Chromeriver 的集成 | Microsoft 文档"
description: "了解如何将 Chromeriver 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 445c5600-e340-4724-a9cb-3cfaf5770b70
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 90dcbc7744677703bf37469953a8ea2713765b40
ms.openlocfilehash: 8345391d6bb84115284a990b302d764805ef209a
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>教程：Azure Active Directory 与 Chromeriver 的集成
本教程的目标是展示 Azure 与 Chromeriver 的集成。  

在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* 启用了 Chromeriver 单一登录的订阅

完成本教程后，分配到 Chromeriver 的 Azure AD 用户将能够在你的 Chromeriver 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Chromeriver 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "方案")

## <a name="enable-the-application-integration-for-chromeriver"></a>为 Chromeriver 启用应用程序集成
本部分的目的是概述如何为 Chromeriver 启用应用程序集成。

**若要为 Chromeriver 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
      ![从库添加应用程序](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Chromeriver”。
   
   ![应用程序库](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "应用程序库")
7. 在结果窗格中，选择“Chromeriver”，然后单击“完成”以添加该应用程序。
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Chromeriver 证明自己的身份。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Chromeriver”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "配置单一登录")
2. 在“你希望用户如何登录到 Chromeriver”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "配置单一登录")
3. 在“配置应用设置”页上，执行以下步骤：
   
   ![配置应用设置](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "配置应用设置")
   
   1. 在“回复 URL”文本框中，键入 Chromeriver **AssertionConsumerService URL**（例如：*https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*）。  
   
     >[!NOTE]
     >可以从 Chromeriver 支持团队那里获取该值。
     >  
   2. 单击“下一步”
4. 在“在 Chromeriver 处配置单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "配置单一登录")
5. 将下载的元数据文件发送到 Chromeriver 支持团队。
   
 >[!NOTE]
 >必须由 Chromeriver 支持团队执行实际的 SSO 配置。 在为订阅启用了 SSO 后，你将收到通知。
 >

6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Chromeriver，必须将其预配到 Chromeriver 中。  

* 对于 Chromeriver，需要由 Chromeriver 支持团队创建用户帐户。

>[!NOTE]
>可以使用 Chromeriver 提供的任何其他 Chromeriver 用户帐户创建工具或 API 来预配 Azure Active Directory 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Chromeriver，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Chromeriver”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


