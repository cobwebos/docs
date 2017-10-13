---
title: "教程：Azure Active Directory 与 Replicon 集成 | Microsoft Docs"
description: "了解如何使用 Replicon 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2aeeceb61191962b62892b8409218684f76c6fa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>教程：Azure Active Directory 与 Replicon 集成
本教程的目的是说明 Azure 与 Replicon 的集成。 在本教程中概述的方案假定已有以下各项：

* 一个有效的 Azure 订阅
* Replicon 租户

完成本教程后，已向 Replicon 分配的 Azure AD 用户能够在 Replicon 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到该应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Replicon 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-replicon-tutorial/IC777798.png "方案")

## <a name="enable-the-application-integration-for-replicon"></a>为 Replicon 启用应用程序集成
本部分的目的是概述如何为 Replicon 启用应用程序集成。

**若要为 Replicon 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-replicon-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-replicon-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-replicon-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“Replicon”。
   
    ![应用程序库](./media/active-directory-saas-replicon-tutorial/IC777799.png "应用程序库")
7. 在结果窗格中，选择“Replicon”，并单击“完成”以添加该应用程序。
   
    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Replicon 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Replicon”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-replicon-tutorial/IC777801.png "配置单一登录")
2. 在“你希望用户如何登录 Replicon”页上，选择“Microsoft Azure AD 单一登录”，并单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-replicon-tutorial/IC777802.png "配置单一登录")
3. 在“配置应用 URL”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-replicon-tutorial/IC777803.png "配置应用 URL")
  1. 在“Replicon 登录 URL”文本框中，键入 Replicon 租户 URL（例如：*https://na2.replicon.com/company/saml2/sp-sso/post*）。
  2. 在“Replicon 答复 URL”文本框中，键入 Replicon **AssertionConsumerService** URL（例如：*https://global.replicon.com/!/saml2/company/sso/post*）。  
      
     >[!NOTE]
     >可以从以下网页的 Replicon 元数据中获取 URL：**https://global.replicon.com/!/saml2/\<YourCompanyKey\>**。
     > 
     > 
 
  3. 单击“下一步”。

4. 在“配置 Replicon 的单一登录”页上，要下载元数据，请单击“下载元数据”，然后将元数据保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-replicon-tutorial/IC777804.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Replicon 公司站点。

6. 若要配置 SAML 2.0，请执行以下步骤：
   
    ![启用 SAML 身份验证](./media/active-directory-saas-replicon-tutorial/IC777805.png "启用 SAML 身份验证")
  
  1. 要显示 **EnableSAML Authentication2** 对话框，请将以下内容追加到 URL 中的公司密钥后面：**/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
    * 下面显示完整 URL 的架构：  
   **https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
   2. 单击 **+** 展开 **v20Configuration** 部分。
   3. 单击 **+** 展开 **metaDataConfiguration** 部分。
   4. 单击“选择文件”，选择标识提供者元数据 XML 文件，并单击“提交”。

7. 在 Azure 经典门户中，选择“单一登录配置确认”，并单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-replicon-tutorial/IC778418.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 Replicon，必须将这些用户预配到 Replicon 中。  

对于 Replicon，预配是一项手动任务。

**若要配置用户预配，请执行以下步骤：**

1. 在 Web 浏览器窗口中，以管理员身份登录 Replicon 公司站点。
2. 转到“管理”\>“用户”。
   
    ![用户](./media/active-directory-saas-replicon-tutorial/IC777806.png "用户")
3. 单击“+添加用户”。
   
    ![添加用户](./media/active-directory-saas-replicon-tutorial/IC777807.png "添加用户")
4. 在“用户配置文件”部分中，执行以下步骤：
   
    ![用户配置文件](./media/active-directory-saas-replicon-tutorial/IC777808.png "用户配置文件")
   
  1. 在“登录名”文本框中，键入要预配的 Azure AD 用户的 Azure AD 电子邮件地址。
  2. 对于“身份验证类型”，选择“SSO”。
  3. 在“部门”文本框中，键入用户的部门。
  4. 对于“员工类型”，选择“管理员”。
  5. 单击“保存用户配置文件”。

>[!NOTE]
>可以使用任何其他 Replicon 用户帐户创建工具或 Replicon 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**要将用户分配到 Replicon，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。

2. 在“Replicon”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-replicon-tutorial/IC777809.png "分配用户")

3. 选择测试用户，单击“分配”，并单击“是”确认分配。
   
    ![是](./media/active-directory-saas-replicon-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

