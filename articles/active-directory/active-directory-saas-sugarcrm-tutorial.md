---
title: "教程：Azure Active Directory 与 SugarCRM 集成 | Microsoft 文档"
description: "了解如何使用 SugarCRM 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: acd11de9f2b987c3c0bc6d74cff8019aca92f437
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>教程：Azure Active Directory 与 SugarCRM 集成
本教程的目的是说明 Azure 与 Sugar CRM 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Sugar CRM 单一登录 (SSO) 的订阅

完成本教程后，已向 Sugar CRM 分配的 Azure AD 用户将能够在 Sugar CRM 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以 SSO 方式登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 Sugar CRM 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "方案")

## <a name="enable-the-application-integration-for-sugar-crm"></a>为 Sugar CRM 启用应用程序集成
本部分的目的是概述如何为 Sugar CRM 启用应用程序集成。

**若要为 Sugar CRM 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "从库添加应用程序")

6. 在**搜索框**中，键入“Sugar CRM”。
   
    ![应用程序库](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "应用程序库")

7. 在结果窗格中，选择“Sugar CRM”，然后单击“完成”以添加该应用程序。
   
    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

## <a name="configure-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Sugar CRM 进行身份验证。  

在此过程中，需要将 base-64 编码的证书上载到 Sugar CRM 租户。  

如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Sugar CRM”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "配置单一登录")

2. 在“你希望用户如何登录 Sugar CRM”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "配置单一登录")

3. 在“配置应用 URL”页的“Sugar CRM 登录 URL”文本框中，键入用户用于登录 Sugar CRM 应用程序的 URL（例如：“http://company.sugarondemand.com”），然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "配置应用 URL")

4. 在“配置 Sugar CRM 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "配置单一登录")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Sugar CRM 公司站点。

6. 转到“管理员”。
   
    ![管理员](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "管理员")

7. 在“管理”部分中，单击“密码管理”。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administration")

8. 选择“启用 SAML 身份验证”。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administration")

9. 在“SAML 身份验证”部分中，执行以下步骤：
   
    ![SAML 身份验证](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML 身份验证")   
  1. 在 Azure 经典门户的“配置 Sugar CRM 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
  2. 在 Azure 经典门户的“配置 Sugar CRM 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“SLO URL”文本框中。
  3. 基于下载的证书创建一个 **Base-64 编码**的文件。
      
     >[!TIP]
     >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。 
     > 

  4. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“X.509 证书”文本框中。
  5. 单击“保存” 。

10. 在 Azure 经典门户中的“配置 Sugar CRM 的单一登录”对话框页上，选择“单一登录配置确认”，然后单击“完成”。
    
    ![配置单一登录](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "配置单一登录")

## <a name="configure-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 Sugar CRM，必须将这些用户预配到 Sugar CRM 中。

对于 Sugar CRM，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **Sugar CRM** 公司站点。
2. 转到“管理员”。
   
    ![管理员](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "管理员")

3. 在“管理”部分中，单击“用户管理”。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administration")

4. 转到“用户”\>“创建新用户”。
   
    ![创建新用户](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Create New User")

5. 在“用户配置文件”选项卡上，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "New User")
  * 在相关文本框中键入有效 Azure Active Directory 用户的用户名、姓氏和电子邮件地址。
6. 对于“状态”，选择“活动”。

7. 在“密码”选项卡中，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "New User")
  1. 在相关文本框中键入密码。
  2. 单击“保存” 。

>[!NOTE]
>可以使用 Sugar CRM 提供的任何其他 Sugar CRM 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Sugar CRM，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。

2. 在“Sugar CRM”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


