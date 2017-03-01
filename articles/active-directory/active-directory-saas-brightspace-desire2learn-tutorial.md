---
title: "教程：Azure Active Directory 与 Brightspace by Desire2Learn 集成 | Microsoft Docs"
description: "了解如何使用 Brightspace by Desire2Learn 与 Azure Active Directory 以启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: cdd7a42f12c145327249ff7c03ede9a465374e35
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>教程：Azure Active Directory 与 Brightspace by Desire2Learn 集成
本教程的目的是说明 Azure 与 Brightspace by Desire2Learn 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Brightspace by Desire2Learn 单一登录 (SSO) 的订阅

完成本教程后，分配到 Brightspace by Desire2Learn 的 Azure AD 用户将能够在 Brightspace by Desire2Learn 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Brightspace by Desire2Learn 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "方案")

## <a name="enable-the-application-integration-for-brightspace-by-desire2learn"></a>为 Brightspace by Desire2Learn 启用应用程序集成
本部分旨在概述如何为 Brightspace by Desire2Learn 启用应用程序集成。

**若要为 Brightspace by Desire2Learn 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入 **Brightspace by Desire2Learn**。
   
   ![应用程序库](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Apllication Gallery")
7. 在结果窗格中，选择“Brightspace by Desire2Learn”，然后单击“完成”以添加该应用程序。
   
   ![Brightspace by Desire2Learn](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace by Desire2Learn")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Brightspace by Desire2Learn 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Brightspace by Desire2Learn”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "配置单一登录")
2. 在“你希望用户如何登录 Brightspace by Desire2Learn”页面上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "配置单一登录")
3. 在“配置应用 URL”页上，执行以下步骤：
   
   ![配置应用 URL](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "配置应用 URL")   
   1. 在“登录 URL”文本框中，键入用户用来登录 **Brightspace by Desire2Learn** 的 URL（例如：*https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*）。
   2. 单击“下一步”。
4. 在“配置 Brightspace by Desire2Learn 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "配置单一登录")
5. 将下载的元数据文件发送到 Brightspace by Desire2Learn 支持团队。
   
   >[!NOTE]
   >你的 Brightspace by Desire2Learn 支持团队需要真正配置 SSO。
   >在为订阅启用了 SSO 后，你将收到通知。
   > 
 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Brightspace by Desire2Learn，必须将其预配到 Brightspace by Desire2Learn 中。  

就 Brightspace by Desire2Learn 来说，用户帐户需由 Brightspace by Desire2Learn 支持团队创建。

>[!NOTE]
>可以使用 Brightspace by Desire2Learn 提供的任何其他 Brightspace by Desire2Learn 用户帐户创建工具或 API 来预配 Azure Active Directory 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Brightspace by Desire2Learn，请执行以下步骤：**
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Brightspace by Desire2Learn”应用程序集成页上，单击“分配用户”。
   
  ![分配用户](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
  ![是](./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


