---
title: "教程：Azure Active Directory 与 AirWatch 集成 | Microsoft Docs"
description: "了解如何将 AirWatch 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5f2030abf8b6c2d5884f4c5c2cb29d89ff329227


---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>教程：Azure Active Directory 与 AirWatch 集成
本教程旨在展示 Azure 与 AirWatch 集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用的订阅上的 AirWatch 单一登录

完成本教程后，分配到 AirWatch 的 Azure AD 用户将能够在 AirWatch 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 支持 AirWatch 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")

## <a name="enabling-the-application-integration-for-airwatch"></a>支持 AirWatch 的应用程序集成
本部分旨在概述如何为 AirWatch 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>若要支持 AirWatch 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-airwatch-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-airwatch-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-airwatch-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索框”中，键入“AirWatch”。
   
   ![应用程序库](./media/active-directory-saas-airwatch-tutorial/IC791914.png "应用程序库")
7. 在“结果”窗格中，选择“AirWatch”，然后单击“完成”，添加该应用程序。
   
   ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 AirWatch 进行身份验证。  
作为此过程的一部分，要求创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“AirWatch”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-airwatch-tutorial/IC791916.png "配置单一登录")
2. 在“你希望用户如何登录 AirWatch”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-airwatch-tutorial/IC791917.png "配置单一登录")
3. 在“配置应用 URL”页的“AirWatch 登录 URL”文本框中，键入用户用于登录 AirWatch 应用程序的 URL（例如：“*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-airwatch-tutorial/IC791918.png "配置应用 URL")
4. 在“配置 AirWatch 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-airwatch-tutorial/IC791919.png "配置单一登录")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 AirWatch 公司站点。
6. 在左侧导航窗格中，单击“帐户”，然后单击“管理员”。
   
   ![管理员](./media/active-directory-saas-airwatch-tutorial/IC791920.png "管理员")
7. 展开“设置”菜单，然后单击“目录服务”。
   
   ![设置](./media/active-directory-saas-airwatch-tutorial/IC791921.png "设置")
8. 单击“用户”选项卡，在“基 DN”文本字段中键入域名，然后单击“保存”。
   
   ![用户](./media/active-directory-saas-airwatch-tutorial/IC791922.png "用户")
9. 单击“服务器”选项卡。
   
   ![服务器](./media/active-directory-saas-airwatch-tutorial/IC791923.png "服务器")
10. 执行以下步骤：
    
    ![上载](./media/active-directory-saas-airwatch-tutorial/IC791924.png "上载")
    
    1. 对于“目录类型”，选择“无”。
    2. 选择“将 SAML 用于身份验证”。
    3. 若要上传下载的证书，请单击“上传”。
11. 在“请求”部分中，执行以下步骤：
    
    ![请求](./media/active-directory-saas-airwatch-tutorial/IC791925.png "请求")
    
    1. 对于“请求绑定类型”，请选择“POST”。
    2. 在 Azure 经典门户的“配置 Airwatch 的单一登录”对话框上，复制“单一登录服务 URL”值，然后将其粘贴到“标识提供程序单一登录 URL”文本框。
    3. 对于“NameID 格式”，请选择“电子邮件地址”。
    4. 单击“保存” 。
12. 再次单击“用户”选项卡。
    
    ![用户](./media/active-directory-saas-airwatch-tutorial/IC791926.png "用户")
13. 在“属性”部分中，执行以下步骤：
    
    ![属性](./media/active-directory-saas-airwatch-tutorial/IC791927.png "属性")
    
    1. 在“对象标识符”文本框中，键入“http://schemas.microsoft.com/identity/claims/objectidentifier”。
    2. 在“用户名”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress”。
    3. 在“显示名称”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”。
    4. 在“名字”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”。
    5. 在“姓氏”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”。
    6. 在“电子邮件”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress”。
    7. 单击“保存” 。
14. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-airwatch-tutorial/IC791928.png "配置单一登录")
    
    ## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录 AirWatch，必须对其进行预配才能使其登录 AirWatch。  
就 AirWatch 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 **AirWatch** 公司站点。
2. 在左侧导航窗格中，单击“帐户”，然后单击“用户”。
   
   ![用户](./media/active-directory-saas-airwatch-tutorial/IC791929.png "用户")
3. 在“用户”菜单中，单击“列表视图”，然后依次单击“添加”\>“添加用户”。
   
   ![添加用户](./media/active-directory-saas-airwatch-tutorial/IC791930.png "添加用户")
4. 在“添加 / 编辑用户”对话框中，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-airwatch-tutorial/IC791931.png "添加用户")
   
   1. 键入希望在相关文本框中预配的有效 Azure Active Directory 帐户的“用户名”、“密码”、“确认密码”、“名字”、“姓氏”、“电子邮件地址”。
   2. 单击“保存” 。

> [!NOTE]
> 可使用其他任何 AirWatch 用户帐户创建工具或 AirWatch 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>若要将用户分配到 AirWatch，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“AirWatch”应用程序集成页面上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-airwatch-tutorial/IC791932.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-airwatch-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO5-->


