---
title: "教程：Azure Active Directory 与 Zoom 集成 | Microsoft Docs"
description: "了解如何使用 Microsoft Docs 与 Azure Active Directory 启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f5640d84774898e1c51c5dcfa52aab781cddf044
ms.openlocfilehash: eb4966d74ffc4360605f6c88570bfb7833abaef1
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>教程：Azure Active Directory 与 Zoom 的集成
本教程旨在说明 Azure 与 Zoom 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Zoom 租户

完成本教程后，分配到 Zoom 的 Azure AD 用户可单一登录到 Zoom 公司站点的应用程序（服务提供商启动的登录），或可使用[访问面板简介](active-directory-saas-access-panel-introduction.md)

在本教程中概述的方案由以下构建基块组成：

1. 为 Zoom 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-zoom-tutorial/IC784693.png "方案")

## <a name="enabling-the-application-integration-for-zoom"></a>为 Zoom 启用应用程序集成
本部分旨在概述如何为 Zoom 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>若要为 Zoom 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-zoom-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-zoom-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-zoom-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索框”中，键入“Zoom”。
   
   ![应用程序库](./media/active-directory-saas-zoom-tutorial/IC784694.png "应用程序库")
7. 在“结果”窗格中，选择“Zoom”，然后单击“完成”，添加该应用程序。
   
   ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Zoom 进行身份验证。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Zoom”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-zoom-tutorial/IC784696.png "配置单一登录")
2. 在“你希望用户如何登录 Zoom”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-zoom-tutorial/IC784697.png "配置单一登录")
3. 在“配置应用 URL”页上的“Zoom 登录 URL”文本框中，使用以下模式“http://company.zoom.us”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-zoom-tutorial/IC784698.png "配置应用 URL")
4. 在“配置 Zoom 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-zoom-tutorial/IC784699.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zoom 公司站点。
6. 单击“单一登录” 选项卡。
   
   ![单一登录](./media/active-directory-saas-zoom-tutorial/IC784700.png "单一登录")
7. 单击“安全控制”，然后转到“单一登录”设置。
8. 在“单一登录”部分中，执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-zoom-tutorial/IC784701.png "单一登录")
   
   1. 在 Azure 经典门户的“配置 Zoom 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“登录页 URL”文本框中。
   2. 在 Azure 经典门户的“配置 Zoom 的单一登录”对话框页上，复制“单一注销服务 URL”值，然后将其粘贴到“注销页 URL”文本框中。
   3. 根据下载的证书创建 **base-64 编码**文件。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. 在记事本中打开 base-64 编码证书、将其内容复制到剪贴板，然后将其粘贴到“标识提供者证书”文本框
   5. 在 Azure 经典门户的“配置 Zoom 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中。
   6. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-zoom-tutorial/IC784702.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
为使 Azure AD 用户能够登录到 Zoom，必须将其预配 Zoom 中。  
就 Zoom 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 **Zoom** 公司站点。
2. 单击“帐户管理”选项卡，然后单击“用户管理”。
3. 在“用户管理”部分，单击“添加用户”。
   
   ![用户管理](./media/active-directory-saas-zoom-tutorial/IC784703.png "用户管理")
4. 在“添加用户”页上，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-zoom-tutorial/IC784704.png "添加用户")
   
   1. 对于“用户类型”，请选择“基本”。
   2. 在“电子邮件”文本框中，键入要预配的有效 AAD 帐户的电子邮件地址。
   3. 单击 **“添加”**。

> [!NOTE]
> 可以使用 Zoom 提供的任何其他 Zoom 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-zoom-perform-the-following-steps"></a>若要将用户分配到 Zoom，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Zoom”应用程序集成页面上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-zoom-tutorial/IC784705.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-zoom-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


