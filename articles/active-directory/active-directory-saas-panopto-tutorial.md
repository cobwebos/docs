---
title: "教程：Azure Active Directory 与 Panopto 集成 | Microsoft 文档"
description: "了解如何使用 Panopto 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 89c88e23-93ce-4970-9baa-1104c4e8fe4a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 078a2ea0db006cf976f89a55c65a536a7b9f04aa
ms.lasthandoff: 04/03/2017


---

# <a name="tutorial-azure-active-directory-integration-with-panopto"></a>教程：Azure Active Directory 与 Panopto 的集成
本教程的目的是介绍 Azure 与 Panopto 的集成。 

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Panopto 租户

完成本教程以后，分配到 Panopto 的 Azure AD 用户就可以通过单一登录登录到 Panopto 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 Panopto 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-panopto-tutorial/IC777665.png "方案")

## <a name="enable-the-application-integration-for-panopto"></a>为 Panopto 启用应用程序集成
本部分的目的是概述如何为 Panopto 启用应用程序集成。

**若要为 Panopto 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-panopto-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-panopto-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-panopto-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Panopto”。
   
   ![应用程序库](./media/active-directory-saas-panopto-tutorial/IC777666.png "应用程序库")
7. 在结果窗格中，选择“Panopto”，然后单击“完成”添加该应用程序。
   
   ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
   
## <a name="configure-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Panopto 进行身份验证。  

在此过程中，需要创建 base-64 编码的证书文件。 

如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置 SSO，请执行以下步骤：**

1. 在 Azure 经典门户中的“Panopto”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-panopto-tutorial/IC777667.png "配置单一登录")
2. 在“你希望用户如何登录 Panopto”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-panopto-tutorial/IC777668.png "配置单一登录")
3. 在“配置应用 URL”页上，在“Panopto 登录 URL”文本框中，使用以下模式键入 URL：*https://\<tenant-name\>.Panopto.com*，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-panopto-tutorial/IC777528.png "配置应用 URL")
4. 在“配置 Panopto 的单一登录”页上，若要下载证书，请单击“下载证书”，然后在计算机上保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-panopto-tutorial/IC777669.png "配置单一登录")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 Panopto 公司站点。
6. 在左侧工具栏中，单击“系统”，然后单击“标识提供者”。
   
   ![系统](./media/active-directory-saas-panopto-tutorial/IC777670.png "系统")
7. 单击“添加提供者”。
   
   ![标识提供者](./media/active-directory-saas-panopto-tutorial/IC777671.png "标识提供者")
8. 在“SAML 提供者”部分执行以下步骤：
   
   ![SaaS 配置](./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS 配置")
   
   1. 在“提供者类型”列表中，选择“SAML20”。
   2. 在“实例名称”文本框中，键入实例的名称。
   3. 在“易懂描述”文本框中，键入通俗易懂的描述。
   4. 在 Azure 经典门户的“配置 Panopto 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中。
   5. 在 Azure 经典门户的“配置 Panopto 的单一登录”对话框页上，复制“SAML SSO URL”值，然后将其粘贴到“Bounce Page URL”文本框中。
   6. 基于下载的证书创建一个 **base-64 编码**的文件。    
   
      >[!TIP]
      >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。
      >
      
   7. 在记事本中打开 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“公钥”文本框中
   8. 单击“保存” 。

 ![保存](./media/active-directory-saas-panopto-tutorial/IC777673.png "保存")
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
  ![配置单一登录](./media/active-directory-saas-panopto-tutorial/IC777674.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

没有相关操作项，因此无法通过配置将用户预配到 Panopto。  
当已分配的用户尝试使用访问面板登录到 Panopto 时，Panopto 会检查该用户是否存在。  

如果尚无用户帐户可用，Panopto 将自动创建该帐户。

>[!NOTE]
>可以使用任何其他 Panopto 用户帐户创建工具或 Panopto 提供的 API 来预配 AAD 用户帐户。
>
>

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Panopto，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Panopto”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-panopto-tutorial/IC777675.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-panopto-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


