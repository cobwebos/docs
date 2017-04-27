---
title: "教程：Azure Active Directory 与 NetDocuments 集成 | Microsoft 文档"
description: "了解如何使用 NetDocuments 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a6ca819329de130257ec67e63a53f87cbca9f39b
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>教程：Azure Active Directory 与 NetDocuments 的集成
本教程的目的是介绍 Azure 与 NetDocuments 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* NetDocuments 租户

完成本教程以后，分配到 NetDocuments 的 Azure AD 用户就可以通过单一登录登录到 NetDocuments 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 NetDocuments 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-netdocuments-tutorial/IC795040.png "方案")

## <a name="enabling-the-application-integration-for-netdocuments"></a>为 NetDocuments 启用应用程序集成
本部分的目的是概述如何为 NetDocuments 启用应用程序集成。

**若要为 NetDocuments 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-netdocuments-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-netdocuments-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-netdocuments-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“NetDocuments”。
   
   ![应用程序库](./media/active-directory-saas-netdocuments-tutorial/IC795041.png "应用程序库")
7. 在结果窗格中，选择“NetDocuments”，然后单击“完成”添加该应用程序。
   
   ![NetDocuments](./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过其在 Azure AD 中的帐户向 NetDocuments 进行身份验证。  

配置 NetDocuments 的 SSO 需要检索证书的指纹值。 如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

**若要配置 SSO，请执行以下步骤：**

1. 在 Azure 经典门户中的“NetDocuments”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-netdocuments-tutorial/IC795043.png "配置单一登录")
2. 在“你希望用户如何登录 NetDocuments”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-netdocuments-tutorial/IC795044.png "配置单一登录")
3. 在“配置应用 URL”页上，执行以下步骤：
   
   ![配置应用 URL](./media/active-directory-saas-netdocuments-tutorial/IC795045.png "配置应用 URL")
   
   1. 在“登录 URL”文本框中，键入用户登录到 NetDocuments 应用程序时使用的 URL（例如“*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*”）。
   2. 在“NetDocuments 答复 URL”文本框中键入的值与在“登录 URL”文本框中键入的值相同。  
      
      >[!NOTE]
      >可以在“联合身份”对话框的末尾找到正确的值（参见步骤 9 的屏幕截图）。
      >
      >
     
   3. 单击“资源组名称” 的 Azure 数据工厂。
4. 在“配置 NetDocuments 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-netdocuments-tutorial/IC795046.png "配置单一登录")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 NetDocuments 公司站点。
6. 转到“管理员”。
7. 单击“添加和删除用户和组”。
   
   ![存储库](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "存储库")
8. 单击“配置高级身份验证选项”。
   
   ![配置高级身份验证选项](./media/active-directory-saas-netdocuments-tutorial/IC795048.png "配置高级身份验证选项")
9. 在“联合身份”对话框中，执行以下步骤：
   
   ![联合身份](./media/active-directory-saas-netdocuments-tutorial/IC795049.png "联合身份")
   
   1. 选择“Active Directory 联合身份验证服务”作为“联合身份服务器类型”。
   2. 若要上载已下载的元数据文件，请单击“选择文件”。
   3. 单击 **“确定”**。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-netdocuments-tutorial/IC795050.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

若要让 Azure AD 用户登录 NetDocuments，必须将其预配到 NetDocuments 中。 使用 NetDocuments 时，预配属手动任务。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录 **NetDocuments** 公司站点。
2. 在顶部菜单中，单击“管理员”。
   
   ![管理员](./media/active-directory-saas-netdocuments-tutorial/IC795051.png "管理员")
3. 单击“添加和删除用户和组”。
   
   ![存储库](./media/active-directory-saas-netdocuments-tutorial/IC795047.png "存储库")
4. 在“电子邮件地址”文本框中，键入要预配的有效 Azure Active Directory 帐户的电子邮件地址，然后单击“添加用户”。
   
   ![电子邮件地址](./media/active-directory-saas-netdocuments-tutorial/IC795053.png "电子邮件地址")
   
   >[!NOTE]
   >Azure Active Directory 帐户持有者将获得一封电子邮件，该邮件包含在激活帐户前确认帐户的链接。
   > 
   > 

>[!NOTE]
>可以使用任何其他 NetDocuments 用户帐户创建工具或 NetDocuments 提供的 API 来预配 AAD 用户帐户。
>
>

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 NetDocuments，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在 **NetDocuments** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-netdocuments-tutorial/IC795054.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-netdocuments-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
