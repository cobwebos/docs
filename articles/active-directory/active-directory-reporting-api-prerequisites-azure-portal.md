---
title: "访问 Azure AD 报告 API 的先决条件 | Microsoft Docs"
description: "了解有关访问 Azure AD 报告 API 的先决条件"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5fafd83c337e3c73260d89cdad7409a01ce5855b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>访问 Azure AD 报告 API 的先决条件

[Azure AD 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API，让你以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。

报告 API 使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授权访问 Web API。 

若要通过 API 访问报告数据，需分配以下角色之一：

- 安全读取者
- 安全管理员
- 全局管理员


要准备访问报告 API，必须执行以下操作：

1. 注册应用程序 
2. 授予权限 
3. 收集配置设置 

有关疑问、问题或反馈，请[提交支持票证](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)。

## <a name="register-an-azure-active-directory-application"></a>注册 Azure Active Directory 应用程序

即使是在使用脚本来访问报告 API，也需要注册一个应用。 该操作会提供一个**应用程序 ID**，该 ID 是进行授权调用所必需的，代码也通过该 ID 来接收令牌。

若要配置目录以访问 Azure AD 报告 API，必须使用同为 Azure AD 租户中“全局管理员”目录角色成员的 Azure 管理员帐户登录到 Azure 门户。

> [!IMPORTANT]
> 在具有此类“管理”特权的凭据下运行的应用程序可以具有非常强大的功能，因此请务必保证应用程序 ID/机密凭据的安全。
> 


**若要注册 Azure Active Directory 应用程序，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“Azure Active Directory”边栏选项卡中，单击“应用注册”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. 在“应用注册”边栏选项卡的顶部工具栏中，单击“新建应用程序注册”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. 在“创建”边栏选项卡中执行以下步骤：

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. 在“名称”文本框中，键入 `Reporting API application`。

    b. 选择“Web 应用/API”作为应用程序类型。

    c. 在“登录 URL”文本框中，键入 `https://localhost`。

    d. 单击“创建” 。 


## <a name="grant-permissions"></a>授予权限 

此步骤的目的是授予应用程序对 **Windows Azure Active Directory** API 的“读取目录数据”权限。

![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/16.png)
 

**若要授予应用程序使用 API 的权限，请执行以下操作：**

1. 在“应用注册”边栏选项卡的应用列表中，单击“Reporting API 应用程序”。

2. 在“Reporting API 应用程序”边栏选项卡的顶部工具栏中，单击“设置”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. 在“设置”边栏选项卡中，单击“所需的权限”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. 在“所需的权限”边栏选项卡的“API”列表中，单击“Windows Azure Active Directory”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. 在“启用访问权限”边栏选项卡中，选择“读取目录数据”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. 在顶部工具栏中，单击“保存”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

## <a name="gather-configuration-settings"></a>收集配置设置 
本部分介绍了如何从目录中获取以下设置：

* 域名
* 客户端 ID
* 客户端机密

在配置对报告 API 的调用时需要这些值。 

### <a name="get-your-domain-name"></a>获取域名

**若要获取域名，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“Azure Active Directory”边栏选项卡中，单击“域名”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. 从域列表中复制域名。


### <a name="get-your-applications-client-id"></a>获取应用程序的客户端 ID

**若要获取应用程序的客户端 ID，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“应用注册”边栏选项卡的应用列表中，单击“Reporting API 应用程序”。

3. 在“Reporting API 应用程序”边栏选项卡中，指向“应用程序 ID”，并单击“单击复制”。

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>获取应用程序的客户端机密
若要获取应用程序的客户端密码，需要创建新密钥并在保存新密钥时保存其值，因为它在以后无法再检索此值。

**若要获取应用程序的客户端机密，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Active Directory”。
   
    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. 在“应用注册”边栏选项卡的应用列表中，单击“Reporting API 应用程序”。


3. 在“Reporting API 应用程序”边栏选项卡的顶部工具栏中，单击“设置”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. 在“设置”边栏选项卡的“APIR 访问”部分，单击“密钥”。 

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. 在“密钥”边栏选项卡中执行以下步骤：

    ![注册应用程序](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. 在“说明”文本框中，键入 `Reporting API`。

    b. 选择“2 年内”作为“过期时间”。

    c. 单击“保存” 。

    d. 复制密钥值。


## <a name="next-steps"></a>后续步骤
* 是否要以编程方式从 Azure AD 报告 API 中访问数据？ 请查看 [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)。
* 如果要查找有关 Azure Active Directory 报告的详细信息，请参阅 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)。  

