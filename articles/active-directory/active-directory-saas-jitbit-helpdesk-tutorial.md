---
title: "教程：Azure Active Directory 与 Jitbit Helpdesk 集成 | Microsoft Docs"
description: "了解如何使用 Jitbit Helpdesk 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e8dfd2e943143c88406c88d85fea6916be61a7ec
ms.openlocfilehash: 0fbadc98022f82782a39c61f8ce574cf30b1fbaa
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>教程：Azure Active Directory 与 Jitbit Helpdesk 集成
本教程的目的是说明 Azure 与 Jitbit Helpdesk 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Jitbit Helpdesk 租户

完成本教程后，已向 Jitbit Helpdesk 分配的 Azure AD 用户将能够在 Jitbit Helpdesk 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Jitbit Helpdesk 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "方案")

## <a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>为 Jitbit Helpdesk 启用应用程序集成
本部分的目的是概述如何为 Jitbit Helpdesk 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>若要为 Jitbit Helpdesk 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Jitbit Helpdesk”。
   
   ![应用程序库](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "应用程序库")
7. 在结果窗格中，选择“Jitbit Helpdesk”，然后单击“完成”以添加该应用程序。
   
   ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Jitbit Helpdesk 进行身份验证。 。  

在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

>[!IMPORTANT]
>为了能够在 Jitbit Helpdesk 租户上配置单一登录，需要首先联系 Jitbit Helpdesk 技术支持以启用此功能。 
> 

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Jitbit Helpdesk”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "配置单一登录")
2. 在“你希望用户如何登录 Jitbit Helpdesk”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "配置单一登录")
3. 在“配置应用 URL”页上的“Jitbit Helpdesk 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.Jitbit.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "配置应用 URL")
4. 在“配置 Jitbit Helpdesk 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件本地保存为“c:\\Jitbit Helpdesk.cer”。
   
   ![配置单一登录](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Jitbit Helpdesk 公司站点。
6. 在顶部工具栏中，单击“管理”。
   
   ![管理](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
7. 单击“常规设置”。
   
   ![用户、公司和权限](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "用户、公司和权限")
8. 在“身份验证设置”配置部分中，执行以下步骤：
   
   ![身份验证设置](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "身份验证设置")
   
   1. 选择“启用 SAML 2.0 单一登录”，在 **OneLogin** 中使用单一登录 (SSO) 进行登录。
   2. 在 Azure 经典门户中的“配置 Jitbit Helpdesk 的单一登录”对话框页上，复制**服务提供商(SP)启动的终结点**值，然后将其粘贴到“终结点 URL”文本框中。
   3. 基于下载的证书创建一个 **base-64 编码**的文件。 
      >[!TIP]
      >有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o) 
      > 
   4. 打开 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“X.509 证书”文本框中
   5. 单击“保存更改”。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "配置单一登录")
   
## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 Jitbit Helpdesk，必须将这些用户预配到 Jitbit Helpdesk 中。  对于 Jitbit Helpdesk，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Jitbit Helpdesk** 租户。
2. 在顶部菜单中，单击“管理”。
   
   ![管理](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")
3. 单击“用户、公司和权限”。
   
   ![用户、公司和权限](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "用户、公司和权限")
4. 单击“添加用户”。
   
   ![添加用户](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "添加用户")
5. 在“创建”部分的以下文本框中，键入要预配的 Azure AD 帐户的数据：**用户名**、**电子邮件**、**名称**、**姓氏**
   
   ![创建](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "创建")
6. 单击“创建” 。

>[!NOTE]
>可以使用 Jitbit Helpdesk 提供的任何其他 Jitbit Helpdesk 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Jitbit Helpdesk，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Jitbit Helpdesk”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


