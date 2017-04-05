---
title: "教程：Azure Active Directory 与 SumoLogic 集成 | Microsoft 文档"
description: "了解如何使用 SumoLogic 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 3fbf55ddc13e6489e81eb04c962e833600bfc846
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>教程：Azure Active Directory 与 SumoLogic 集成
本教程的目的是说明 Azure 与 SumoLogic 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* SumoLogic 租户

完成本教程后，已向 SumoLogic 分配的 Azure AD 用户将能够在 SumoLogic 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 SumoLogic 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-sumologic-tutorial/IC778549.png "方案")

## <a name="enable-the-application-integration-for-sumologic"></a>为 SumoLogic 启用应用程序集成
本部分的目的是概述如何为 SumoLogic 启用应用程序集成。

**若要为 SumoLogic 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-sumologic-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-sumologic-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-sumologic-tutorial/IC749322.png "从库添加应用程序")

6. 在**搜索框**中，键入“sumologic”。
   
    ![应用程序库](./media/active-directory-saas-sumologic-tutorial/IC778550.png "应用程序库")

7. 在结果窗格中，选择“SumoLogic”，然后单击“完成”以添加该应用程序。
   
    ![SumoLogic](./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

## <a name="configure-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 SumoLogic 进行身份验证。  

在此过程中，需要将 base-64 编码的证书上载到 SumoLogic 租户。  

如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“SumoLogic”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/IC778552.png "配置单一登录")

2. 在“你希望用户如何登录 SumoLogic”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/IC778553.png "配置单一登录")

3. 在“配置应用 URL”页上的“SumoLogic 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.SumoLogic.com*”键入 URL，然后单击“下一步”。
   
    ![配置 aoo URL](./media/active-directory-saas-sumologic-tutorial/IC778554.png "配置 aoo URL")

4. 在“配置 SumoLogic 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/IC778555.png "配置单一登录")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 SumoLogic 公司站点。

6. 转到“管理”\>“安全性”。
   
    ![管理](./media/active-directory-saas-sumologic-tutorial/IC778556.png "管理")

7. 单击“SAML”。
   
    ![全局安全设置](./media/active-directory-saas-sumologic-tutorial/IC778557.png "全局安全设置")

8. 从“选择配置或创建新配置”列表中，选择“Azure AD”，然后单击“配置”。
   
    ![配置 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778558.png "配置 SAML 2.0")

9. 在“配置 SAML 2.0”对话框上，执行以下步骤：
   
    ![配置 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/IC778559.png "配置 SAML 2.0")   
  1. 在“配置名称”文本框中，键入“Azure AD”。 
  2. 选择“调试模式”。
  3. 在 Azure 经典门户的“配置 SumoLogic 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中。
  4. 在 Azure 经典门户的“配置 SumoLogic 的单一登录”对话框页上，复制“身份验证请求 URL”值，然后将其粘贴到“身份验证请求 URL”文本框中。
  5. 基于下载的证书创建一个 **Base-64 编码**的文件。  
      
     >[!TIP]
     >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。
     >  

  6. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“X.509 证书”文本框中。
  7. 对于“电子邮件属性”，选择“使用 SAML 主题”。  
  8. 选择“SP 发起的登录配置”。
  9. 在“登录路径”文本框中，键入“Azure”，然后单击“保存”。

10. 在 Azure 经典门户中的“配置 SumoLogic 的单一登录”对话框页上，选择“单一登录配置确认”，然后单击“完成”。
    
    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/IC778560.png "配置单一登录")

## <a name="configure-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 SumoLogic，必须将这些用户预配到 SumoLogic 中。  

* 对于 SumoLogic，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **SumoLogic** 租户。

2. 转到“管理”\>“用户”。
   
    ![用户](./media/active-directory-saas-sumologic-tutorial/IC778561.png "用户")

3. 单击 **“添加”**。
   
    ![用户](./media/active-directory-saas-sumologic-tutorial/IC778562.png "用户")

4. 在“新建用户”对话框中，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-sumologic-tutorial/IC778563.png "New User")  
  1. 将要预配的 Azure AD 帐户的相关信息键入到“名字”、“姓氏”和“电子邮件”文本框中。
  2. 选择角色。
  3. 对于“状态”，选择“活动”。
  4. 单击“保存” 。

>[!NOTE]
>可以使用 SumoLogic 提供的任何其他 SumoLogic 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 SumoLogic，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。

2. 在“SumoLogic”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-sumologic-tutorial/IC778564.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-sumologic-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


