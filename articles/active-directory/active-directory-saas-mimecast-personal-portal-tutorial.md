---
title: "教程：Azure Active Directory 与 Mimecast Personal Portal 的集成 | Microsoft Docs"
description: "了解如何将 Mimecast Personal Portal 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: eb0fef7143e85e1e9ab8b586e1f9944340c76bf3


---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>教程：Azure Active Directory 与 Mimecast Personal Portal 的集成
本教程的目的是展示 Azure 与 Mimecast Personal Portal 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 启用了 Mimecast Personal Portal 单一登录的订阅

完成本教程后，分配到 Mimecast Personal Portal 的 Azure AD 用户将能够在你的 Mimecast Personal Portal 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 Mimecast Personal Portal 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "Scenario")

## <a name="enabling-the-application-integration-for-mimecast-personal-portal"></a>为 Mimecast Personal Portal 启用应用程序集成
本部分的目的是概述如何为 Mimecast Personal Portal 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-mimecast-personal-portal-perform-the-following-steps"></a>若要为 Mimecast Personal Portal 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户中，在左侧导航窗格上，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Mimecast Personal Portal”。
   
   ![应用程序库](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "Application Gallery")
7. 在结果窗格中，选择“Mimecast Personal Portal”，然后单击“完成”以添加该应用程序。
   
   ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Mimecast Personal Portal 证明自己的身份。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Mimecast Personal Portal** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Configure Single Sign-On")
2. 在“你希望用户如何登录到 Mimecast Personal Portal”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上，在“Mimecast Personal Portal 登录 URL”文本框中，键入用户用来登录 Mimecast Personal Portal 应用程序的 URL（例如：“https://webmail-uk.mimecast.com” 或 “https://webmail-us.mimecast.com”），然后单击“下一步”。
   
   > [!NOTE]
   > 登录 URL 特定于区域。
   > 
   > 
   
   ![配置应用 URL](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "Configure App URL")
4. 在“在 Mimecast Personal Portal 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Mimecast Personal Portal。
6. 转到“服务”\>“应用程序”。
   
   ![应用程序](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "Applications")
7. 单击“身份验证配置文件”。
   
   ![身份验证配置文件](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "Authentication Profiles")
8. 单击“新建身份验证配置文件”。
   
   ![新建身份验证配置文件](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "New Authentication Profil")
9. 在“身份验证配置文件”部分中，执行以下步骤：
   
   ![身份验证配置文件](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "Authentication Profil")
   
   1. 在“描述”文本框中，键入配置名称。
   2. 选择“对 Mimecast Personal Portal 强制实施 SAML 身份验证”。
   3. 对于“提供程序”，选择“Azure Active Directory”。
   4. 在 Azure 经典门户中，在“在 Mimecast Personal Portal 处配置单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者 URL”文本框中。
   5. 在 Azure 经典门户中，在“在 Mimecast Personal Portal 处配置单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
   6. 在 Azure 经典门户中，在“在 Mimecast Personal Portal 处配置单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“注销 URL”文本框中。  
      
      > [!NOTE]
      > 对于 Mimecast Personal Portal，“登录 URL”值和“注销 URL”值是相同的。
      > 
      > 
   7. 基于下载的证书创建一个 **base-64 编码**的文件。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。
      > 
      > 
   8. 在记事本中打开 base-64 编码的证书，删除第一行（“*--*”）和最后一行（“*--*”），将剩余的内容复制到剪贴板中，然后将其粘贴到“标识提供程序证书(元数据)”文本框中。
   9. 选择“允许单一登录”。
   10. 单击“保存”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>配置用户预配

为了使 Azure AD 用户能够登录到 Mimecast Personal Portal，必须将其预配到 Mimecast Personal Portal 中。  
对于 Mimecast Personal Portal，需要手动执行预配。

需要注册一个域，然后才能创建用户。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户预配，请执行以下步骤：
1. 以管理员身份登录到 **Mimecast Personal Portal**。
2. 转到“目录”\>“内部”。
   
   ![目录](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "Directories")
3. 单击“注册新域”。
   
   ![注册新域](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "Register New Domain")
4. 在创建新域后，单击“新建地址”。
   
   ![新建地址](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "New Address")
5. 在“新建地址”对话框中，执行以下步骤：
   
   ![保存](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "Save")
   
   1. 将要预配的有效 AAD 帐户的**电子邮件地址**、**全局名称**、**密码**和**确认密码**属性键入到相关文本框中。
   2. 单击“保存”。

> [!NOTE]
> 可以使用 Mimecast Personal Portal 提供的任何其他 Mimecast Personal Portal 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配用户的方式向要允许其使用应用程序的 Azure AD 用户授予应用程序访问权限。

### <a name="to-assign-users-to-mimecast-personal-portal-perform-the-following-steps"></a>若要将用户分配到 Mimecast Personal Portal，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在 **Mimecast Personal Portal** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


