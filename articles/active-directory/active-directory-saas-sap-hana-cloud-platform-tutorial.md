---
title: "教程：将 Azure Active Directory 与 SAP HANA 云平台集成 | Microsoft Docs"
description: "了解如何使用 SAP HANA 云平台与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e03bc2410a8d57363c558f723b3bfd0e69b3f4c0
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>教程：将 Azure Active Directory 与 SAP HANA 云平台集成
本教程的目的是说明 Azure 与 SAP HANA 云平台的集成。

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* SAP HANA 云平台帐户

完成本教程后，已向 SAP HANA 云平台分配的 Azure AD 用户将能够使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到该应用程序。

>[!IMPORTANT]
>用户需要部署自己的应用程序或订阅 SAP HANA 云平台帐户上的应用程序来测试单一登录。 在本教程中，将在帐户中部署应用程序。
> 
> 

在本教程中概述的方案由以下构建基块组成：

1. 为 SAP HANA 云平台启用应用程序集成
2. 配置单一登录 (SSO)
3. 向用户分配角色
4. 分配用户

![方案](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "方案")

## <a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>为 SAP HANA 云平台启用应用程序集成
本部分的目的是概述如何为 SAP HANA 云平台启用应用程序集成。

**若要为 SAP HANA 云平台启用应用程序集成，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“SAP HANA 云平台”。
   
    ![应用程序库](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "应用程序库")
7. 在结果窗格中，选择“SAP HANA 云平台”，然后单击“完成”以添加该应用程序。
   
    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 SAP HANA 云平台进行身份验证。

在此过程中，需要将 base-64 编码的证书上载到 SAP HANA 云平台租户。  

如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“SAP HANA 云平台”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "配置单一登录")
2. 在“你希望用户如何登录 SAP HANA 云平台”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "配置单一登录")
3. 在另一个 Web 浏览器窗口中，登录到 SAP HANA 云平台考核中心，网址为 https://account.\<landscape host\>.ondemand.com/cockpit（例如：*https://account.hanatrial.ondemand.com/cockpit*）。
4. 单击“信任”选项卡。
   
    ![信任](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "信任")
5. 在“信任管理”部分中，执行以下步骤：
   
    ![获取元数据](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "获取元数据")
   
   1. 单击“本地服务提供程序”选项卡。
   2. 若要下载 SAP HANA 云平台元数据文件，请单击“获取元数据”。
6. 在 Azure Active 经典门户的“配置应用 URL”页上，执行以下步骤，然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "配置应用 URL")
   
   1. 在“登录 URL”文本框中，键入用户用于登录“SAP HANA 云平台”应用程序的 URL。 这是“SAP HANA 云平台”应用程序中受保护资源的特定于帐户的 URL。 此 URL 基于以下模式：*https://\<applicationName\>\<accountName\>.\<landscape host\>.ondemand.com/\<path\_to\_protected\_resource\>*（例如：*https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*）
      
     >[!NOTE]
     >这是“SAP HANA 云平台”应用程序中要求用户进行身份验证的 URL。
     > 

   2. 打开下载的 SAP HANA 云平台元数据文件，然后找到 **ns3:AssertionConsumerService** 标记。
   3. 复制 **Location** 属性的值，然后将其粘贴到“SAP HANA 云平台回复 URL”文本框中。

7. 在“配置 SAP HANA 云平台的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将该文件保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "配置单一登录")
8. 在 SAP HANA 云平台考核中心上的“本地服务提供程序”部分中，执行以下步骤：
   
    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "信任管理")
   
  1. 单击“编辑”。
  2. 对于“配置类型”，选择“自定义”。
  3. 对于“本地提供程序名称” ，保留默认值。
  4. 若要生成**签名密钥**和**签名证书**密钥对，请单击“生成密钥对”。
  5. 对于“主体传播” ，选择“禁用”。
  6. 对于“强制身份验证”，选择“禁用”。
  7. 单击“保存” 。

9. 单击“受信任的标识提供者”选项卡，然后单击“添加受信任的标识提供者”。
   
    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "信任管理")
   
    >[!NOTE]
    >若要管理受信任的标识提供者的列表，需要已在本地服务提供程序部分中选择自定义配置类型。 如果选择“默认”配置类型，则对 SAP ID 服务有不可编辑的隐式信任。 如果选择“无”，则不具有任何信任设置。
    > 
    > 

10. 单击“常规” 选项卡，然后单击“浏览”以上载已下载的元数据文件。
    
    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "信任管理")
    
    >[!NOTE]
    >上载元数据文件后，将自动填充**单一登录 URL**、**单一注销 URL** 和**签名证书**的值。
    > 
    > 

11. 单击“属性”选项卡。
12. 在“属性”选项卡中，执行以下步骤：
    
    ![属性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "属性") 
  * 单击“添加基于断言的属性”，然后添加以下基于断言的属性：
       
    | 断言属性 | 主体属性 |
    | --- | --- |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname |名 |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname |姓 |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress |email 
   
     >[!NOTE]
     >属性的配置取决于如何开发 HCP 上的应用程序，即它们应在 SAML 响应中预期哪些属性，以及它们在哪个名称（主体属性）下通过代码访问此属性。
     > 
     >  

    1.  屏幕截图中的**默认属性**仅用于说明目的。 它并不是使方案正常工作所必需的。   
    2.  屏幕截图中所示的**主体属性**的名称和值取决于如何开发应用程序。 很可能你的应用程序需要不同的映射。
     
13. 在 Azure 经典门户中的“配置 SAP HANA 云平台的单一登录”对话框页上，选择“单一登录配置确认”，然后单击“完成”。
    
    ![配置单一登录](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "配置单一登录")

###<a name="assertion-based-groups"></a>基于断言的组
可选步骤是，可以为 Azure Active Directory 标识提供者配置基于断言的组。

使用 SAP HANA 云平台上的组可以将一个或多个用户动态分配到 SAP HANA 云平台应用程序中的一个或多个角色，具体由 SAML 2.0 断言中的属性值确定。 

例如，如果断言包含属性“contract=temporary”，你可能希望将所有受影响的用户添加到组“TEMPORARY”。 组“TEMPORARY”可能包含 SAP HANA 云平台帐户中部署的一个或多个应用程序中的一个或多个角色。
 
若想要同时将多个用户分配到 SAP HANA 云平台帐户中的应用程序的一个或多个角色，请使用基于断言的组。 如果只想将一个或少量用户分配到特定角色，建议直接在 SAP HANA 云平台考核中心的“授权”选项卡上分配它们。

## <a name="assign-a-role-to-a-user"></a>向用户分配角色
要使 Azure AD 用户能够登录 SAP HANA 云平台，必须为他们分配 SAP HANA 云平台中的角色。

**若要向用户分配角色，请执行以下步骤：**

1. 登录到 **SAP HANA 云平台**考核中心。
2. 执行以下操作：
   
   ![授权](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "授权")
   
  1. 单击“授权”。
  2. 单击“用户”选项卡。
  3. 在“用户”文本框中，键入用户的电子邮件地址。
  4. 单击“分配”，将用户分配到角色。
  5. 单击“保存” 。

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 SAP HANA 云平台，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“SAP HANA 云平台”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


