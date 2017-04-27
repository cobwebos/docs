---
title: "教程：Azure Active Directory 与 Bamboo HR 集成 | Microsoft Docs"
description: "了解如何使用 Bamboo HR 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e190cfdf6d7a9dee86ed7b9072dc51ca8c993d32
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>教程：Azure Active Directory 与 Bamboo HR 集成
本教程的目的是说明 Azure 与 BambooHR 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用了 BambooHR 单一登录 (SSO) 的订阅

完成本教程后，分配到 BambooHR 的 Azure AD 用户将能够在 BambooHR 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 BambooHR 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "方案")

## <a name="enable-the-application-integration-for-bamboohr"></a>为 BambooHR 启用应用程序集成
本部分旨在概述如何为 BambooHR 启用应用程序集成。

**若要为 BambooHR 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“BambooHR”。
   
   ![应用程序库](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "应用程序库")
7. 在“结果”窗格中，选择“BambooHR”，然后单击“完成”以添加该应用程序。
   
   ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 BambooHR 证明自己的身份。  

在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“BambooHR”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![方案](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "方案")
2. 在“你希望用户如何登录 BambooHR”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "配置单一登录")
3. 在“配置应用 URL”页的“BambooHR 登录 URL”文本框中，键入用户用于登录 BambooHR 应用程序的 URL（例如：https://company.bamboohr.com），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "配置应用 URL")
4. 在“配置 BambooHR 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 BambooHR 公司站点。
6. 在主页上执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "单一登录")   
   1. 单击“应用”。
   2. 在左侧的应用菜单中，单击“单一登录”。
   3. 单击“SAML 单一登录”。
7. 在“SAML 单一登录”部分中，执行以下步骤：
   
   ![SAML 单一登录](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML 单一登录")
   
   1. 在 Azure 经典门户的“配置 BambooHR 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“SSO 登录 URL”****文本框中。
   2. 根据下载的证书创建 **base-64 编码**文件。  
   
      >[!TIP]
      >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。 
      > 
   3. 在记事本中打开 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“X.509 证书”文本框中
   4. 单击“保存” 。
8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 BambooHR，必须将其预配到 BambooHR 中。  

* 就 BambooHR 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **BambooHR** 站点。
2. 在顶部工具栏中，单击“设置”。
   
   ![设置](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "设置")
3. 单击“概览”。
4. 在左侧导航窗格中，转到“安全”**\>**“用户”。
5. 在相关文本框中键入要预配的有效 AAD 帐户的用户名、密码和电子邮件地址。
6. 单击“保存” 。

>[!NOTE]
>可使用其他任何 BambooHR 用户帐户创建工具或 BambooHR 提供的 API 预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 BambooHR，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“BambooHR”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


