---
title: "教程：Azure Active Directory 与 Coupa 的集成 | Microsoft 文档"
description: "了解如何将 Coupa 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: c952975919cfd948f1b9ea93ff2ac2641a53f923
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>教程：Azure Active Directory 与 Coupa 的集成
本教程的目标是展示 Azure 与 Coupa 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Coupa 单一登录 (SSO) 的订阅

完成本教程后，分配到 Coupa 的 Azure AD 用户能够使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Coupa 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-coupa-tutorial/IC791897.png "方案")

## <a name="enable-the-application-integration-for-coupa"></a>为 Coupa 启用应用程序集成
本部分的目的是概述如何为 Coupa 启用应用程序集成。

**若要为 Coupa 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-coupa-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-coupa-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-coupa-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Coupa”。
   
   ![应用程序库](./media/active-directory-saas-coupa-tutorial/IC791898.png "应用程序库")
7. 在结果窗格中，选择“Coupa”，并单击“完成”以添加该应用程序。
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Coupa 证明自己的身份。  

配置 Coupa 的单一登录需要检索证书的指纹值。 如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

**若要配置单一登录，请执行以下步骤：**

1. 以管理员身份登录到 Coupa 公司站点。
2. 转到“设置”\>“安全控制”。
   
   ![安全性控制](./media/active-directory-saas-coupa-tutorial/IC791900.png "安全性控制")
3. 要将 Coupa 元数据文件下载到计算机，请单击“下载并导入 SP 元数据”。
   
   ![Coupa SP 元数据](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP 元数据")
4. 在另一个浏览器窗口中，登录到 Azure 经典门户。
5. 在 **Coupa** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-coupa-tutorial/IC791902.png "配置单一登录")
6. 在“你希望用户如何登录到 Coupa”页上，选择“Microsoft Azure AD 单一登录”，并单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-coupa-tutorial/IC791903.png "配置单一登录")
7. 在“配置应用 URL”页上，执行以下步骤：
   
   ![配置应用 URL](./media/active-directory-saas-coupa-tutorial/IC791904.png "配置应用 URL")   
   1. 在“登录 URL”文本框中，键入用户用来登录 Coupa 应用程序的 URL（例如：“*http://company.Coupa.com*”）。
   2. 打开下载的 Coupa 元数据文件，并复制 **AssertionConsumerService index/URL**。
   3. 在“Coupa 回复 URL”文本框中，粘贴 **AssertionConsumerService index/URL** 值。
   4. 单击“下一步”。
8. 在“在 Coupa 处配置单一登录”页上，要下载元数据文件，请单击“下载元数据”，然后将文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-coupa-tutorial/IC791905.png "配置单一登录")
9. 在 Coupa 公司站点上，转到“设置”\>“安全控制”。
   
   ![安全性控制](./media/active-directory-saas-coupa-tutorial/IC791900.png "安全性控制")
10. 在“使用 Coupa 凭据进行登录”部分中，执行以下步骤：  

   ![使用 Coupa 凭据登录](./media/active-directory-saas-coupa-tutorial/IC791906.png "使用 Coupa 凭据登录") 
   1. 选择“使用 SAML 进行登录”。
   2. 若要上传所下载的 Azure Active 元数据文件，请单击“浏览”。
   3. 单击“保存”。
11. 在 Azure 经典门户中，选择“单一登录配置确认”，并单击“完成”，关闭“配置单一登录”对话框。
    
   ![配置单一登录](./media/active-directory-saas-coupa-tutorial/IC791907.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Coupa，必须将其预配到 Coupa 中。  

* 对于 Coupa，需要手动执行预配。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录到 **Coupa** 公司站点。
2. 在顶部菜单中，单击“设置”，并单击“用户”。
   
   ![用户](./media/active-directory-saas-coupa-tutorial/IC791908.png "用户")
3. 单击“创建” 。
   
   ![创建用户](./media/active-directory-saas-coupa-tutorial/IC791909.png "创建用户")
4. 在“用户创建”部分中，执行以下步骤：
   
   ![用户详细信息](./media/active-directory-saas-coupa-tutorial/IC791910.png "用户详细信息")
   
   1. 将要预配的有效 Azure Active Directory 帐户的**登录名**、**名字**、**姓氏**、**单一登录 ID**、**电子邮件**属性键入到相关文本框中。
   2. 单击“创建”。   
   >[!NOTE]
   >Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。 
   > 

>[!NOTE]
>可以使用 Coupa 提供的任何其他 Coupa 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**要将用户分配到 Coupa，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“Coupa”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-coupa-tutorial/IC791911.png "分配用户")
3. 选择测试用户，单击“分配”，并单击“是”确认分配。
   
   ![是](./media/active-directory-saas-coupa-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

