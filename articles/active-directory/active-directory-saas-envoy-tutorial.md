---
title: "教程：Azure Active Directory 与 Envoy 的集成 | Microsoft 文档"
description: "了解如何将 Envoy 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 913f0688d757829771a8456b3e3be13f69c5b1da
ms.openlocfilehash: a72feeb919f2bbc1d96736e33c8c99e340ff6914
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>教程：Azure Active Directory 与 Envoy 的集成
本教程的目标是展示 Azure 与 Envoy 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Envoy 租户

完成本教程后，分配到 Envoy 的 Azure AD 用户将能够在你的 Envoy 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Envoy 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-envoy-tutorial/IC776759.png "方案")

## <a name="enable-the-application-integration-for-envoy"></a>为 Envoy 启用应用程序集成
本部分的目的是概述如何为 Envoy 启用应用程序集成。

**若要为 Envoy 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-envoy-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-envoy-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-envoy-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Envoy”。
   
   ![应用程序库](./media/active-directory-saas-envoy-tutorial/IC776760.png "应用程序库")
7. 在结果窗格中，选择“Envoy”，然后单击“完成”以添加该应用程序。
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Envoy 证明自己的身份。  

配置 Envoy 的 SSO 需要检索证书的指纹值。 如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Envoy”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![启用单一登录](./media/active-directory-saas-envoy-tutorial/IC776778.png "启用单一登录")
2. 在“你希望用户如何登录到 Envoy”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-envoy-tutorial/IC776779.png "配置单一登录")
3. 在“配置应用 URL”页上，在“Envoy 登录 URL”文本框中，使用“*https://\<tenant-name\>.Envoy.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-envoy-tutorial/IC776780.png "配置应用 URL")
4. 在“在 Envoy 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后在本地将证书文件保存为 **c:\\Envoy.cer**。
   
   ![配置单一登录](./media/active-directory-saas-envoy-tutorial/IC776781.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Envoy 公司站点。
6. 在顶部工具栏中，单击“设置”。
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")
7. 单击“公司”。
   
   ![公司](./media/active-directory-saas-envoy-tutorial/IC776783.png "公司")
8. 单击“SAML”。
   
   ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")
9. 在“SAML 身份验证”配置部分中，执行以下步骤：
   
   ![SAML 身份验证](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML 身份验证")   
   >[!NOTE]
   >HQ 位置 ID 的值是应用程序自动生成的。 
   > 
   
   1. 从导出的证书中复制“指纹”值，然后将其粘贴到“指纹”文本框中。  
   
      >[!TIP]
      >有关更多详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。 
      > 
   2. 在 Azure 经典门户中，在“在 Envoy 处配置单一登录”对话框页上，复制“SAML SSO URL”值，然后将其粘贴到“标识提供者 HTTP SAML URL”文本框中。
   3. 单击“保存更改”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-envoy-tutorial/IC776786.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

没有操作项可供你用来配置 Envoy 的用户预配。  
当已分配的用户尝试使用访问面板登录到 Envoy 时，Envoy 会检查该用户是否存在。  

* 如果尚无用户帐户可用，Envoy 将自动创建该帐户。

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-envoy-perform-the-following-steps"></a>若要将用户分配到 Envoy，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Envoy”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-envoy-tutorial/IC776787.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-envoy-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


