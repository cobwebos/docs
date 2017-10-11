---
title: "教程：Azure Active Directory 与 Salesforce Sandbox 集成 | Microsoft 文档"
description: "了解如何使用 Salesforce Sandbox 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 32835e79188806bb2ff319eea23b1b52ab585ab1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教程：Azure Active Directory 与 Salesforce 沙盒集成

本教程的目的是说明 Azure 与 Salesforce 沙盒的集成。  

>[!TIP]
>有关反馈，请参阅 [Azure 支持页](http://go.microsoft.com/fwlink/?LinkId=521878)。 
> 

使用沙盒可以在不同的环境中针对多种用途（如开发、测试和培训）创建组织的多个副本，而不会影响 Salesforce 产品组织中的的数据和应用程序。  

有关更多详细信息，请参阅[沙盒概述](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)

在本教程中概述的方案假定已有以下各项：

* 一个有效的 Azure 订阅
* Salesforce.com 中的沙盒

如果在 Salesforce.com 中尚无有效沙盒，则需要联系 Salesforce。

在本教程中概述的方案由以下构建基块组成：

1. 为 Salesforce 沙盒启用应用程序集成
2. 配置单一登录 (SSO)
3. 启用域
4. 配置用户设置
5. 分配用户

![方案](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "方案")

## <a name="enable-the-application-integration-for-salesforce-sandbox"></a>为 Salesforce 沙盒启用应用程序集成
本部分旨在概述如何为 Salesforce 沙盒启用应用程序集成。

**若要为 Salesforce 沙盒启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "应用程序")
4. 要打开**应用程序库**，请单击“添加应用”，并单击“为组织添加要使用的应用程序”。
   
   ![想要做什么？](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "想要做什么？")
5. 在搜索框中，键入“Salesforce 沙盒”。
   
   ![应用程序库](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "应用程序库")
6. 在结果窗格中，选择“Salesforce 沙盒”，并单击“完成”以添加该应用程序。
   
   ![Salesforce 沙盒](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce 沙盒")
   
## <a name="configur-single-sign-on-sso"></a>配置单一登录 (SSO)

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Salesforce 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Salesforce 沙盒”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "配置单一登录")
2. 在“你希望用户如何登录 Salesforce 沙盒”页上，选择“Microsoft Azure AD 单一登录”，并单击“下一步”。
   
   ![Salesforce 沙盒](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce 沙盒")
3. 在“配置应用 URL”页上，在“登录 URL”文本框中，使用 `http://company.my.salesforce.com` 模式键入 URL，并单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "配置应用 URL")
4. 如果已为目录中的另一个 Salesforce 沙盒实例配置单一登录，则还必须配置**标识符**以使用与**登录 URL** 相同的值。 
 * 通过选中“配置应用 URL”对话框页上的“显示高级设置”复选框，可以找到“标识符”字段。
5. 在“配置 Salesforce 沙盒的单一登录”页上，单击“下载证书”，并将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "配置单一登录")
6. 在另一个 Web 浏览器窗口中，以管理员身份登录 Salesforce 沙盒。
7. 在顶部菜单中，单击“设置”。
   
   ![设置](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "设置")
8. 在左侧导航窗格中，单击“安全控制”，并单击“单一登录设置”。
   
   ![单一登录设置](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Single Sign-On Settings")
9. 在“单一登录设置”部分中，执行以下步骤：
   
   ![单一登录设置](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Single Sign-On Settings")  
 1.  选择“已启用 SAML”。 
 2.  单击“新建” 。
10. 在“SAML 单一登录设置”部分中，执行以下步骤：
    
    ![SAML 单一登录设置](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML 单一登录设置")  
 1. 在“名称”文本框中，键入配置的名称（例如：*SPSSOWAAD\_Test*）。 
 2. 在 Azure 经典门户的“配置 Salesforce 沙盒的单一登录”对话框页上，复制“颁发者 URL”值，并将其粘贴到“颁发者”文本框中。
 3. 在“实体 ID”文本框中，键入 **https://test.salesforce.com**（如果这是要添加到目录的第一个 Salesforce 沙盒实例）。 如果已添加 Salesforce 沙盒实例，则对于**实体 ID** 键入**登录 URL**，其格式应为：`http://company.my.salesforce.com`   
 4. 单击“浏览”以上传已下载的证书。  
 5. 对于“SAML 标识类型”，选择“断言包含用户对象的联合 ID”。 
 6. 对于“SAML 标识位置”，请选择“标识位于 Subject 语句的 NameIdentifier 元素中”。
 7. 在 Azure 经典门户的“配置 Salesforce 沙盒的单一登录”对话框页上，复制“远程登录 URL”值，并将其粘贴到“标识提供者登录 URL”文本框中。 
 8. SFDC 不支持 SAML 注销。  一种解决方法是，将“https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0”粘贴到“标识提供者注销 URL”文本框中。
 9. 对于“服务提供程序发起的请求绑定”，请选择“HTTP POST”。 
 10. 单击“保存” 。
11. 在 Azure 经典门户中，选择“单一登录配置确认”，并单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "配置单一登录")

## <a name="enable-your-domain"></a>启用域
本部分假定已创建域。  有关更多详细信息，请参阅[定义域名](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。

**若要启用域，请执行以下步骤：**

1. 在左侧导航窗格中，单击“域管理”，并单击“我的域”。
   
   ![我的域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "我的域")
   
   >[!NOTE]
   >请确保域配置正确。 
   > 
2. 在“登录页设置”部分中，单击“编辑”，并对于“身份验证服务”，选择上一部分中的 SAML 单一登录设置名称，最后单击“保存”。
   
   ![我的域](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "我的域")

配置域后，用户应使用域 URL 登录到 Salesforce 沙盒。  

若要获取 URL 的值，请单击上一部分中创建的 SSO 配置文件。

## <a name="configure-user-provisioning"></a>配置用户设置
本部分的目的是概述如何对 Salesforce 沙盒启用 Active Directory 用户帐户的用户预配。

**若要配置用户预配，请执行以下步骤：**

1. 在 Salesforce 门户的顶部导航栏中，选择名称以展开用户菜单：
   
   ![我的设置](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "我的设置")
2. 在用户菜单中，选择“我的设置”以打开“我的设置”页。
3. 在左侧窗格中，单击“个人”展开“个人”部分，并单击“重置我的安全令牌”：
   
   ![我的设置](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "我的设置")
4. 在“重置我的安全令牌”页上，单击“重置安全令牌”以请求包含 Salesforce.com 安全令牌的电子邮件。
   
   ![新建令牌](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "新建令牌")
5. 在电子邮件收件箱中检查来自 Salesforce.com、标题为“salesforce.com.com 安全确认”的电子邮件。
6. 查看此电子邮件并复制安全令牌值。
7. 在 Azure 经典门户中的“salesforce 沙盒”应用程序集成页上，单击“配置用户预配”，打开“配置用户预配”对话框。
   
   ![配置用户设置](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configure user provisioning")
8. 在“输入用于启用自动用户预配的 Salesforce 沙盒凭据”页上，提供以下配置设置：
   
   ![Salesforce 沙盒](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce 沙盒")   
 1. 在“Salesforce 沙盒管理员用户名”文本框中，键入在 Salesforce.com 中已分配**系统管理员**配置文件的 Salesforce 沙盒帐户名称。
 2. 在“Salesforce 沙盒管理员密码”文本框中，键入此帐户的密码。
 3. 在“用户安全令牌”文本框中，粘贴安全令牌值。
 4. 单击“验证”以验证配置。
 5. 单击“下一步”按钮以打开“确认”页。
9. 在“确认”页上，单击“完成”以保存配置。
   
## <a name="assigning-users"></a>分配用户

若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

**要将用户分配到 Salesforce 沙盒，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“Salesforce 沙盒”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "分配用户")
3. 选择测试用户，单击“分配”，并单击“是”确认分配。
   
   ![是](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "是")

现在应等待 10 分钟，并验证该帐户是否已同步到 Salesforce 沙盒。

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://msdn.microsoft.com/library/dn308586)（访问面板简介）。

