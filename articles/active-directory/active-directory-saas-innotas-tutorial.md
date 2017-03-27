---
title: "教程：Azure Active Directory 与 Innotas 集成 | Microsoft Docs"
description: "了解如何使用 Innotas 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: eb9e9c2c-4b09-4177-bbab-423fd657448e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf68d6ebbc353988e203bcfa773b2e3fe1539b8c
ms.openlocfilehash: ddd7d6dc48dcc1e61ebe348db51248330e24cdd7
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-innotas"></a>教程：Azure Active Directory 与 Innotas 集成
本教程的目的是说明 Azure 与 Innotas 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Innotas 租户

完成本教程后，已向 Innotas 分配的 Azure AD 用户将能够在 Innotas 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Innotas 启用应用程序集成
* 配置单一登录
* 配置用户设置
*  分配用户

![方案](./media/active-directory-saas-innotas-tutorial/IC777331.png "方案")

## <a name="enable-the-application-integration-for-innotas"></a>为 Innotas 启用应用程序集成
本部分的目的是概述如何为 Innotas 启用应用程序集成。

**若要为 Innotas 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-innotas-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-innotas-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-innotas-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“Innotas”。
   
   ![应用程序库](./media/active-directory-saas-innotas-tutorial/IC777332.png "应用程序库")
7. 在结果窗格中，选择“Innotas”，然后单击“完成”以添加该应用程序。
   
   ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Innotas 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Innotas”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-innotas-tutorial/IC777334.png "配置单一登录")
2. 在“你希望用户如何登录 Innotas”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-innotas-tutorial/IC777335.png "配置单一登录")
3. 在“配置应用 URL”页上的“Innotas 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.Innotas.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-innotas-tutorial/IC777336.png "配置应用 URL")
4. 在“配置 Innotas 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将数据文件本地保存为 **c:\\InnotasMetaData.xml**。
   
   ![配置单一登录](./media/active-directory-saas-innotas-tutorial/IC777337.png "配置单一登录")
5. 将该元数据文件转发给 Innotas 支持团队。 支持团队需要为你配置单一登录。
6. 选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-innotas-tutorial/IC777338.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

没有操作项可用于配置预配到 Innotas 的用户。  
当分配的用户尝试使用访问面板登录到 Innotas 时，Innotas 会检查该用户是否存在。  

>[!NOTE]
>如果尚无用户帐户可用，Innotas 将自动创建该用户。
>

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Innotas，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Innotas”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-innotas-tutorial/IC777339.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-innotas-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


