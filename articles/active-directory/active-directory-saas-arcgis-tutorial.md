---
title: "教程：Azure Active Directory 与 ArcGIS 集成 | Microsoft Docs"
description: "了解如何将 ArcGIS 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4b6ad270d7aded7e4ea7343e53ffd8fc3a59d1c6


---
# <a name="tutorial-azure-active-directory-integration-with-arcgis"></a>教程：Azure Active Directory 与 ArcGIS 集成
本教程旨在展示 Azure 与 ArcGIS 集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用的订阅上的 ArcGIS 单一登录

完成本教程后，分配到 ArcGIS 的 Azure AD 用户将能够在 ArcGIS 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 支持 ArcGIS 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario")

## <a name="enabling-the-application-integration-for-arcgis"></a>支持 ArcGIS 的应用程序集成
本部分旨在概述如何支持 ArcGIS 的应用程序集成。

### <a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>若要支持 ArcGIS 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入“ArcGIS”。
   
   ![应用程序库](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Applcation Gallery")
7. 在“结果”窗格中，选择“ArcGIS”，然后单击“完成”，添加该应用程序。
   
   ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 ArcGIS 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“ArcGIS”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 ArcGIS”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“ArcGIS 登录 URL”文本框中，使用以下模式“*https://company.maps.arcgis.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configure App URL")
4. 在“配置 ArcGIS 的单一登录”页面上，单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configure Single Sign-On")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 ArcGIS 公司站点。
6. 单击“编辑设置”。
   
   ![单击“设置”](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")
7. 单击“安全”。
   
   ![“安全”](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Security")
8. 在“企业登录”下，单击“设置标识提供者”。
   
   ![企业登录](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")
9. 在“设置标识提供者”配置页上，执行以下步骤：
   
   ![设置标识提供者](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")
   
   1. 在“名称”文本框中，键入组织的名称。
   2. 对于“企业标识提供者提供元数据所使用的方法”，请选择“文件”。
   3. 若要上传下载的元数据文件，请单击“选择文件”。
   4. 单击“设置标识提供者”。
10. 在 Azure 经典门户中，选择单一登录配置确认，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录 ArcGIS，必须对其进行预配才能使其登录 ArcGIS。  
就 ArcGIS 来说，预配任务需要手动完成。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 登录“ArcGIS”租户。
2. 单击“邀请成员”。
   
   ![邀请成员](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")
3. 选择“自动添加成员且无需发送电子邮件”，然后单击“下一步”。
   
   ![自动添加成员](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")
4. 在“成员”对话框页上，执行以下步骤：
   
   ![添加和查看](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")
   
   1. 输入想要预配的有效 AAD 帐户的“名字”、“姓氏”和“电子邮件”。
   2. 单击“添加和查看”。
5. 查看输入的数据，然后单击“添加成员”。
   
   ![添加成员](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

> [!NOTE]
> 可使用其他任何 ArcGIS 用户帐户创建工具或 ArcGIS 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>若要将用户分配到 ArcGIS，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“ArcGIS”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


