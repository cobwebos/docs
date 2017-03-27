---
title: "教程：Azure Active Directory 与 Lynda.com 的集成 | Microsoft Docs"
description: "了解如何将 Lynda.com 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07b9df5ab5c7df0089f001a26200bb9c3fb1c508
ms.openlocfilehash: 7f4f1c22e0b3382a9de770800ccb733c61d4f5b0
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>教程：Azure Active Directory 与 Lynda.com 的集成
本教程的目的是展示 Azure 与 Lynda.com 的集成。  

在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 一个 Lynda.com 租户

完成本教程后，分配到 Lynda.com 的 Azure AD 用户将能够在你的 Lynda.com 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 Lynda.com 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-lynda-tutorial/IC781046.png "方案")

## <a name="enabling-the-application-integration-for-lyndacom"></a>为 Lynda.com 启用应用程序集成
本部分的目的是概述如何为 Lynda.com 启用应用程序集成。

**若要为 Lynda.com 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-lynda-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-lynda-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-lynda-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Lynda.com”。
   
   ![应用程序库](./media/active-directory-saas-lynda-tutorial/IC777524.png "应用程序库")
7. 在结果窗格中，选择“Lynda.com”，然后单击“完成”以添加该应用程序。
   
   ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Lynda.com 证明自己的身份。

>[!IMPORTANT]
>为了能够在你的 Lynda.com 租户上配置单一登录，请首先联系 Lynda.com 技术支持以启用此功能。 
> 

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Lynda.com** 应用程序集成页上，单击“配置单一登录”****，以打开“配置单一登录”对话框。****

   
   ![配置单一登录](./media/active-directory-saas-lynda-tutorial/IC777526.png "配置单一登录")
2. 在“你希望用户如何登录到 Lynda.com”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-lynda-tutorial/IC777527.png "配置单一登录")
3. 在“配置应用 URL”页上，在“Lynda.com 登录 URL”文本框中，键入你的 Lynda.com 租户 URL（例如：*https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-lynda-tutorial/IC781047.png "配置应用 URL")
4. 在“在 Lynda.com 处配置单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将证书文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-lynda-tutorial/IC777529.png "配置单一登录")
5. 将下载的元数据文件发送给 Lynda.com 支持团队。 Lynda.com 支持团队将为你执行单一登录配置。
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-lynda-tutorial/IC777530.png "配置单一登录")
   
## <a name="configuring-user-provisioning"></a>配置用户设置

没有操作项可供你用来配置 Lynda.com 的用户预配。  
当已分配的用户尝试使用访问面板登录到 Lynda.com 时，Lynda.com 会检查该用户是否存在。  

如果尚无用户帐户可用，Lynda.com 将自动创建该帐户。

>[!NOTE]
>可以使用 Lynda.com 提供的任何其他 Lynda.com 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Lynda.com，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Lynda.com** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-lynda-tutorial/IC777531.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-lynda-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


