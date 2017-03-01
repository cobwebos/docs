---
title: "教程：Azure Active Directory 与 Druva 的集成 | Microsoft 文档"
description: "了解如何将 Druva 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 0c24b01924d5fcec0d4600e044e679cfc295815f
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>教程：Azure Active Directory 与 Druva 的集成
本教程的目标是展示 Azure 与 Druva 的集成。  

在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Druva 单一登录 (SSO) 的订阅

完成本教程后，分配到 Druva 的 Azure AD 用户将能够在你的 Druva 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Druva 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-druva-tutorial/IC795084.png "方案")

## <a name="enable-the-application-integration-for-druva"></a>为 Druva 启用应用程序集成
本部分的目的是概述如何为 Druva 启用应用程序集成。

**若要为 Druva 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-druva-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-druva-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-druva-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Druva”。
   
   ![应用程序库](./media/active-directory-saas-druva-tutorial/IC795085.png "应用程序库")
7. 在结果窗格中，选择“Druva”，然后单击“完成”以添加该应用程序。
   
   ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Druva 证明自己的身份。  

在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

Druva 应用程序需要特定格式的 SAML 断言，这要求向 **SAML 令牌属性**配置添加自定义属性映射。 

以下屏幕截图显示一个示例。

![SAML 令牌属性](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML 令牌属性")

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Druva** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-druva-tutorial/IC795027.png "配置单一登录")
2. 在“你希望用户如何登录到 Druva”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-druva-tutorial/IC795088.png "配置单一登录")
3. 在“配置应用 URL”页上，在“Druva 登录 URL”文本框中，键入用户用来登录 Druva 应用程序的 URL（例如：“*https://cloud.druva.com/home/*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-druva-tutorial/IC795089.png "配置应用 URL")
4. 在“在 Druva 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-druva-tutorial/IC795090.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Druva 公司站点。
6. 转到“管理”\>“设置”。
   
   ![设置](./media/active-directory-saas-druva-tutorial/IC795091.png "设置")
7. 在“单一登录设置”对话框上，执行以下步骤：
   
   ![单一登录设置](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl Sign-On Settings")   
   1. 在 Azure 经典门户中，在“在 Druva 处配置单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“ID 提供者登录 URL”文本框中。
   2. 在 Azure 经典门户中，在“在 Druva 处配置单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“ID 提供者注销 URL”文本框中。
   3. 基于下载的证书创建一个 **base-64 编码**的文件。  
     >[!TIP]
     >有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
     > 
   4. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“ID 提供者证书”文本框中。
   5. 若要打开“设置”页，请单击“保存”。
8. 在“设置”页上，单击“生成 SSO 令牌”。
   
   ![设置](./media/active-directory-saas-druva-tutorial/IC795093.png "设置")
9. 在“单一登录身份验证令牌”对话框中，执行以下步骤：
   
   ![SSO 令牌](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO Token")
   1. 单击“复制”。
   2. 单击“关闭”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
   ![配置单一登录](./media/active-directory-saas-druva-tutorial/IC795095.png "配置单一登录")
11. 在顶部菜单中，单击“属性”，打开“SAML 令牌属性”对话框。
    
    ![属性](./media/active-directory-saas-druva-tutorial/IC795096.png "属性")
12. 若要添加所需的属性映射，请执行以下步骤：
    
   | 属性名称 | 属性值 |
   | --- | --- |
   | insync\_auth\_token |<*剪贴板值*> |
    
   1. 对于上表中的每个数据行，单击“添加用户属性”。
   2. 在“属性名称”文本框中，键入为该行显示的属性名称。
   3. 在“属性值”文本框中，键入为该行显示的属性值。
   4. 单击“完成”。
13. 单击“应用更改”。
    
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Druva，必须将其预配到 Druva 中。 

* 对于 Druva，需要手动执行预配。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到你的 **Druva** 公司站点。
2. 转到“管理”\>“用户”。
   
   ![管理用户](./media/active-directory-saas-druva-tutorial/IC795097.png "管理用户")
3. 单击“新建”。
   
   ![管理用户](./media/active-directory-saas-druva-tutorial/IC795098.png "管理用户")
4. 在“新建用户”对话框中，执行以下步骤：
   
   ![新建用户](./media/active-directory-saas-druva-tutorial/IC795099.png "Create NewUser")
   
   1. 在相关文本框中键入要预配的有效 Azure Active Directory 用户帐户的电子邮件地址和姓名。
   2. 单击“创建用户”。

>[!NOTE]
>可以使用 Druva 提供的任何其他 Druva 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Druva，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Druva** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-druva-tutorial/IC795100.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-druva-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


