---
title: "教程：Azure Active Directory 与 Picturepark 集成 | Microsoft 文档"
description: "了解如何使用 Picturepark 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a6b6610bb4d4b427f525934146340a9cca6f52cb
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>教程：Azure Active Directory 与 Picturepark 的集成
本教程的目的是介绍 Azure 与 Picturepark 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Picturepark 租户

完成本教程以后，分配到 Picturepark 的 Azure AD 用户就可以通过单一登录登录到 Picturepark 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 Picturepark 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-picturepark-tutorial/IC795055.png "方案")

## <a name="enable-the-application-integration-for-picturepark"></a>为 Picturepark 启用应用程序集成
本部分的目的是概述如何为 Picturepark 启用应用程序集成。

**若要为 Picturepark 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-picturepark-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-picturepark-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-picturepark-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Picturepark”。
   
   ![应用程序库](./media/active-directory-saas-picturepark-tutorial/IC795056.png "应用程序库")
7. 在结果窗格中，选择“Picturepark”，然后单击“完成”添加该应用程序。
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")


## <a name="configure-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Picturepark 进行身份验证。  

配置 Picturepark 的 SSO 需要检索证书的指纹值。  

如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Picturepark”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795058.png "配置单一登录")
2. 在“你希望用户如何登录 Picturepark”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795059.png "配置单一登录")
3. 在“配置应用 URL”页的“Picturepark 登录 URL”文本框中，使用“*http://company.picturepark.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-picturepark-tutorial/IC795060.png "配置应用 URL")
4. 在“配置 Picturepark 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795061.png "配置单一登录")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 Picturepark 公司站点。
6. 在顶部工具栏中，单击“管理工具”，然后单击“管理控制台”。
   
   ![管理控制台](./media/active-directory-saas-picturepark-tutorial/IC795062.png "管理控制台")
7. 单击“身份验证”，然后单击“标识提供者”。
   
   ![身份验证](./media/active-directory-saas-picturepark-tutorial/IC795063.png "身份验证")
8. 在“标识提供者配置”部分执行以下步骤：
   
   ![标识提供者配置](./media/active-directory-saas-picturepark-tutorial/IC795064.png "标识提供者配置")
   
   1. 单击 **“添加”**。
   2. 键入配置名称。
   3. 选择“设为默认值”。
   4. 在 Azure 经典门户的“配置 Picturepark 的单一登录”对话框页上，复制“SAML SSO URL”值，然后将其粘贴到“颁发者 URI”文本框中。
   5. 从导出的证书中复制“指纹”值，然后将其粘贴到“可信颁发者指纹”文本框中。  
      
      >[!TIP]
      >有关更多详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。
      >
      >

9. 单击“JoinDefaultUsersGroup”。
10. 若要在“声明”文本框中设置 **Emailaddress** 属性，请键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 并单击“保存”。

      ![配置](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration")
11. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795066.png "配置单一登录")

## <a name="configure-user-provisioning"></a>配置用户设置
若要让 Azure AD 用户登录 Picturepark，必须将其预配到 Picturepark 中。  

 * 使用 Picturepark 时，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Picturepark** 租户。
2. 在顶部工具栏中，单击“管理工具”，然后单击“用户”。
   
   ![用户](./media/active-directory-saas-picturepark-tutorial/IC795067.png "用户")
3. 在“用户概述”选项卡中，单击“新建”。
   
   ![用户管理](./media/active-directory-saas-picturepark-tutorial/IC795068.png "用户管理")
4. 在“创建用户”对话框中，执行以下步骤：
   
   ![创建用户](./media/active-directory-saas-picturepark-tutorial/IC795069.png "创建用户")
   
  1. 键入：希望在相关文本框中预配的有效 Azure Active Directory 用户的“电子邮件地址”、“密码”、“确认密码”、“名字”、“姓氏”、“公司”、“国家或地区”、“邮政编码”、“城市”。
  2. 选择一种**语言**。
  3. 单击“创建” 。

>[!NOTE]
>可以使用任何其他 Picturepark 用户帐户创建工具或 Picturepark 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Picturepark，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Picturepark”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-picturepark-tutorial/IC795070.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-picturepark-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


