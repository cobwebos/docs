---
title: "教程：Azure Active Directory 与 Thirdlight 集成 | Microsoft 文档"
description: "了解如何使用 Thirdlight 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 0ed8c8a24fd5125690c8fadee4918c25498b6693
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>教程：Azure Active Directory 与 Thirdlight 集成
本教程的目的是说明 Azure 与 Thirdlight 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Thirdlight 单一登录 (SSO) 的订阅

完成本教程后，已向 Thirdlight 分配的 Azure AD 用户将能够在 Thirdlight 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以 SSO 方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Thirdlight 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-thirdlight-tutorial/IC805836.png "方案")

## <a name="enable-the-application-integration-for-thirdlight"></a>为 Thirdlight 启用应用程序集成
本部分的目的是概述如何为 Thirdlight 启用应用程序集成。

**若要为 Thirdlight 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-thirdlight-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-thirdlight-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-thirdlight-tutorial/IC749322.png "从库添加应用程序")

6. 在搜索框中，键入“Thirdlight”。
   
    ![应用程序库](./media/active-directory-saas-thirdlight-tutorial/IC805837.png "应用程序库")

7. 在结果窗格中，选择“Thirdlight”，然后单击“完成”以添加该应用程序。
   
    ![ThirdLight](./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

## <a name="configure-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Thirdlight 进行身份验证。  

配置 Thirdlight 的 SSO 需要检索证书的指纹值。

如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Thirdlight”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-thirdlight-tutorial/IC805839.png "配置单一登录")

2. 在“你希望用户如何登录 Thirdlight”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-thirdlight-tutorial/IC805840.png "配置单一登录")

3. 在“配置应用 URL”页的“Thirdlight 登录 URL”文本框中，键入用户用于登录 Thirdlight 应用程序的 URL（例如：“*http://azuresso2.thirdlight.com/*”），然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-thirdlight-tutorial/IC805841.png "配置应用 URL")

4. 在“配置 Thirdlight 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-thirdlight-tutorial/IC805842.png "配置单一登录")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Thirdlight 公司站点。

6. 转到“配置”\>“系统管理”，然后单击“SAML2”。
   
    ![系统管理](./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administration")

7. 在 SAML2 配置部分中，执行以下步骤：
   
    ![SAML 单一登录](./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML 单一登录")   
 1. 选择“启用 SAML2 单一登录”。 
 2. 选择“从 XML 加载 IdP 元数据”作为 **IdP 元数据的源**。 
 3. 打开下载的元数据文件，复制其内容，然后将其粘贴到“IdP 元数据 XML”文本框中。 
 4. 单击“保存 SAML2 设置”。

8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-thirdlight-tutorial/IC805845.png "配置单一登录")

## <a name="configure-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 Thirdlight，必须将这些用户预配到 Thirdlight 中。  

* 对于 Thirdlight，预配是一项手动任务。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录 **Thirdlight** 公司站点。

2. 转到“用户”选项卡。

3. 选择“用户和组”。

4. 单击“添加新用户”按钮。

5. 输入**要预配的有效 AAD 帐户的用户名、名称或说明、电子邮件，并选择新成员的预设或组**。

6. 单击“创建” 。

>[!NOTE]
>可以使用任何其他 Thirdlight 用户帐户创建工具或 Thirdlight 提供的 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Thirdlight，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。

2. 在“Thirdlight”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-thirdlight-tutorial/IC805846.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-thirdlight-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


