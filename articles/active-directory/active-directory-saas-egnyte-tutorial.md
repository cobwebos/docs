---
title: "教程：Azure Active Directory 与 Egnyte 的集成 | Microsoft 文档"
description: "了解如何将 Egnyte 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 4e8857244038435430f3cabdf5f3eccad8a922bf
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>教程：Azure Active Directory 与 Egnyte 的集成
本教程的目标是展示 Azure 与 Egnyte 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Egnyte 单一登录 (SSO) 的订阅

完成本教程后，分配到 Egnyte 的 Azure AD 用户将能够在你的 Egnyte 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Egnyte 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-egnyte-tutorial/IC787812.png "方案")

## <a name="enable-the-application-integration-for-egnyte"></a>为 Egnyte 启用应用程序集成
本部分的目的是概述如何为 Egnyte 启用应用程序集成。

**若要为 Egnyte 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-egnyte-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-egnyte-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-egnyte-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“egnyte”。
   
   ![应用程序库](./media/active-directory-saas-egnyte-tutorial/IC787813.png "应用程序库")
7. 在结果窗格中，选择“Egnyte”，然后单击“完成”以添加该应用程序。
   
   ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Egnyte 证明自己的身份。  

在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Egnyte** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-egnyte-tutorial/IC787815.png "配置单一登录")
2. 在“你希望用户如何登录到 Egnyte”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-egnyte-tutorial/IC787816.png "配置单一登录")
3. 在“配置应用 URL”页上，在“Egnyte 登录 URL”文本框中，使用“*https://company.egnyte.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-egnyte-tutorial/IC787817.png "配置应用 URL")
4. 在“在 Egnyte 处配置单一登录”页上，单击“下载证书”，然后将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-egnyte-tutorial/IC787818.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Egnyte 公司站点。
6. 单击“设置”。
   
   ![设置](./media/active-directory-saas-egnyte-tutorial/IC787819.png "设置")
7. 在菜单中，单击“设置”。
   
   ![设置](./media/active-directory-saas-egnyte-tutorial/IC787820.png "设置")
8. 单击“配置”选项卡，然后单击“安全性”。
   
   ![安全](./media/active-directory-saas-egnyte-tutorial/IC787821.png "安全")
9. 在“单一登录身份验证”部分中，执行以下步骤：
   
   ![单一登录身份验证](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Single Sign On Authentication")   
   1. 对于“单一登录身份验证”，选择“SAML 2.0”。
   2. 对于“标识提供者”，选择“AzureAD”。
   3. 在 Azure 经典门户中，在“在 Egnyte 处配置单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“标识提供者登录 URL”文本框中。
   4. 在 Azure 经典门户中，在“在 Egnyte 处配置单一登录”对话框页上，复制“实体 ID”值，然后将其粘贴到“标识提供者实体 ID”文本框中。
   5. 基于下载的证书创建一个 **base-64 编码**的文件。  
     >[!TIP]
     >有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      > 
   6. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“标识提供者证书”文本框中。
   7. 对于“默认用户映射”，选择“电子邮件地址”。
   8. 对于“使用特定于域的颁发者值”，选择“禁用”。
   9. 单击“保存”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
   ![配置单一登录](./media/active-directory-saas-egnyte-tutorial/IC787823.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Egnyte，必须将其预配到 Egnyte 中。 对于 Egnyte，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到你的 **Egnyte** 公司站点。
2. 转到“设置”\>“用户和组”。
3. 单击“添加新用户”，然后选择要添加的用户的类型。
   
   ![用户](./media/active-directory-saas-egnyte-tutorial/IC787824.png "用户")
4. 在“新建标准用户”部分中，执行以下步骤：
   
   ![新建标准用户](./media/active-directory-saas-egnyte-tutorial/IC787825.png "New Standard User")   
   1. 键入要预配的有效 Azure Active Directory 帐户的“电子邮件”、“用户名”和其他详细信息。
   2. 单击“保存”。
    >[!NOTE]
    >Azure Active Directory 帐户持有者将收到一封通知电子邮件。
    >

>[!NOTE]
>可以使用 Egnyte 提供的任何其他 Egnyte 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Egnyte，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Egnyte** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-egnyte-tutorial/IC787826.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-egnyte-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


