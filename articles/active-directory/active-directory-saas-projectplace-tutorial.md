---
title: "教程：Azure Active Directory 与 Projectplace 集成 | Microsoft 文档"
description: "了解如何使用 Projectplace 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fcf3332a6674524ea5354b96826586c79a4ba3e1
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>教程：Azure Active Directory 与 Projectplace 的集成
本教程的牡丹是介绍 Azure 与 Projectplace 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Projectplace 单一登录 (SSO) 的订阅

完成本教材后，分配到 Projectplace 的 Azure AD 用户将能够通过单一登录方式登录到 Projectplace 公司站点的应用程序（由服务提供商启动登录），也可以按照 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）中的步骤登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 Projectplace 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-projectplace-tutorial/IC790217.png "方案")

## <a name="enabling-the-application-integration-for-projectplace"></a>为 Projectplace 启用应用程序集成
本部分的目的的是概述如何为 Projectplace 启用应用程序集成。

**若要为 Projectplace 启用应用程序集成，请执行以下步骤：**
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-projectplace-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-projectplace-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-projectplace-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-projectplace-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“Projectplace”。
   
   ![应用程序库](./media/active-directory-saas-projectplace-tutorial/IC790218.png "应用程序库")
7. 在结果窗格中，选择“Projectplace”，然后单击“完成”添加该应用程序。
   
   ![ProjectPlace](./media/active-directory-saas-projectplace-tutorial/IC790219.png "ProjectPlace")
   
## <a name="configure-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Projectplace 进行身份验证。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Projectplace”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-projectplace-tutorial/IC790220.png "配置单一登录")
2. 在“你希望用户如何登录 Projectplace”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-projectplace-tutorial/IC790221.png "配置单一登录")
3. 在“配置应用 URL”页的“Projectplace 登录 URL”文本框中，键入 ProjectPlace 租户 URL（例如“*http://company.projectplace.com*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-projectplace-tutorial/IC790222.png "配置应用 URL")
4. 在“配置 Projectplace 的单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-projectplace-tutorial/IC790223.png "配置单一登录")
5. 将元数据文件发送给 Projectplace 支持团队。
   
   >[!NOTE]
   >单一登录配置必须由 Projectplace 支持团队执行。 配置完成后会立即收到通知。
   > 
   > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-projectplace-tutorial/IC790227.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

若要让 Azure AD 用户登录到 Projectplace，必须在 Projectplace 中对其进行预配。 使用 Projectplace 时，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **Projectplace** 公司站点。
2. 转到“人员”，然后单击“成员”。
   
   ![人员](./media/active-directory-saas-projectplace-tutorial/IC790228.png "人员")
3. 单击“添加成员”。
   
   ![添加成员](./media/active-directory-saas-projectplace-tutorial/IC790232.png "添加成员")
4. 在“添加成员”部分执行以下步骤：
   
   ![新建成员](./media/active-directory-saas-projectplace-tutorial/IC790233.png "新建成员")
   
   1. 在“新建成员”文本框中，键入要在相关文本框中预配的有效 AAD 帐户的电子邮件地址。
   2. 单击“发送”。

将向 Azure Active Directory 帐户持有者发送一封电子邮件，该邮件包含在激活帐户前确认帐户的链接。


>[!NOTE]
>可以使用任何其他 Projectplace 用户帐户创建工具或 Projectplace 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Projectplace，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Projectplace”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-projectplace-tutorial/IC790234.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-projectplace-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


