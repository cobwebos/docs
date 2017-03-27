---
title: "教程：Azure Active Directory 与 Aha! 集成 | Microsoft 文档"
description: "了解如何使用 Aha! 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 76b3bab6c995b93e907d32814c851de5105b8b33
ms.openlocfilehash: b293545b35954e60569a56b900c247f55ddbec1e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>教程：Azure Active Directory 与 Aha! 集成
本教程旨在展示 Azure 与 Aha! 集成  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Aha! 已启用单一登录 (SSO) 的订阅

完成本教程后，分配到 Aha! 的 Azure AD 用户 将能够在 Aha! 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

* 支持 Aha! 的应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-aha-tutorial/IC798944.png "方案")

## <a name="enable-the-application-integration-for-aha"></a>为 Aha! 启用应用程序集成
本部分旨在概述如何为 Aha! 启用应用程序集成。

**若要为 Aha! 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-aha-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-aha-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-aha-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索框”中，键入“Aha!”。
   
   ![应用程序库](./media/active-directory-saas-aha-tutorial/IC798945.png "应用程序库")
7. 在“结果”窗格中，选择“Aha!”，然后单击“完成”，添加该应用程序。
   
   ![Aha!](./media/active-directory-saas-aha-tutorial/IC802746.png "Aha!")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用户能够 使用基于 SAML 协议的联合身份验证通过 Azure AD 中的帐户对 Aha! 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Aha!” 在应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-aha-tutorial/IC798946.png "配置单一登录")
2. 在**你希望用户如何登录 Aha!** 页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-aha-tutorial/IC798947.png "配置单一登录")
3. 在“配置应用 URL”页面的“Aha!**登录 URL”**文本框中，键入用户用于登录 Aha! 应用程序的 URL （例如：“*https://company.aha.io/session/new*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-aha-tutorial/IC798948.png "配置应用 URL")
4. 在“配置 Aha! 的单一登录” 页面上，若要下载元数据文件，请单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-aha-tutorial/IC798949.png "配置单一登录")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 Aha! 公司站点。
6. 在顶部菜单中，单击“设置”。
   
   ![设置](./media/active-directory-saas-aha-tutorial/IC798950.png "设置")
7. 单击“帐户”。
   
   ![配置文件](./media/active-directory-saas-aha-tutorial/IC798951.png "配置文件")
8. 单击“安全性和单一登录”。
   
   ![安全性和单一登录](./media/active-directory-saas-aha-tutorial/IC798952.png "安全性和单一登录")
9. 在“单一登录”部分中，对于“标识提供者”，选择“SAML2.0”。
   
   ![安全性和单一登录](./media/active-directory-saas-aha-tutorial/IC798953.png "安全性和单一登录")
10. 在“单一登录”配置页上，执行以下步骤：
    
   ![单一登录](./media/active-directory-saas-aha-tutorial/IC798954.png "单一登录")    
    1. 在“名称”文本框中，键入配置名称。
    2. 对于“配置使用”，请选择“元数据文件”。
    3. 若要上传下载的元数据文件，请单击“浏览”。
    4. 单击“更新”。
11. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
   ![配置单一登录](./media/active-directory-saas-aha-tutorial/IC798955.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录 Aha!，必须对其进行预配才能使其登录 Aha!。  

* 就 Aha! 来说，预配是自动化任务。 不存在任何操作项。

如有必要，在第一次尝试单一登录时将自动创建用户。

>[!NOTE]
>可以使用其他任何 Aha! 用户帐户创建工具或 Aha! 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Aha!，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Aha!” 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-aha-tutorial/IC798956.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-aha-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


