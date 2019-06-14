---
title: 访问 Azure Active Directory 报告 API 的先决条件 | Microsoft 文档
description: 了解有关访问 Azure AD 报告 API 的先决条件
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab30dfeccc4ae1c7b09a8f48846ffb9e71cfc23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438943"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>访问 Azure Active Directory 报告 API 的先决条件

[Azure Active Directory (Azure AD) 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可从各种编程语言和工具中调用这些 API。

报告 API 使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授权访问 Web API。

若要准备访问报告 API，需要执行以下操作：

1. [分配角色](#assign-roles)
2. [注册应用程序](#register-an-application)
3. [授予权限](#grant-permissions)
4. [收集配置设置](#gather-configuration-settings)

## <a name="assign-roles"></a>分配角色

若要通过 API 访问报告数据，需分配以下角色之一：

- 安全读取者

- 安全管理员

- 全局管理员角色


## <a name="register-an-application"></a>注册应用程序

即使是在使用脚本来访问报告 API，也需要注册一个应用程序。 该操作会提供一个**应用程序 ID**，该 ID 是进行授权调用所必需的，代码也通过该 ID 来接收令牌。

若要配置目录以访问 Azure AD 报告 API，必须使用同为 Azure AD 租户中**全局管理员**目录角色成员的 Azure 管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

> [!IMPORTANT]
> 在具有管理员权限的凭据下运行的应用程序可能具有非常强大的功能，因此请务必将应用程序的 ID 和机密凭据放置在一个安全的位置。
> 

**若要注册 Azure AD 应用程序，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)中，从左侧导航窗格中选择“Azure Active Directory”。 
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”  页面中，选择“应用注册”  。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. 在“应用注册”页面中，选择“新建应用程序注册”   。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. 在“创建”  页面中，执行以下步骤：

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. 在“名称”  文本框中，键入 `Reporting API application`。

    b. 选择“Web 应用/API”作为应用程序类型   。

    c. 在“登录 URL”  文本框中，键入 `https://localhost`。

    d. 选择“创建”  。 


## <a name="grant-permissions"></a>授予权限 

根据想要访问的 API，你需要向应用授予以下权限：  

| API | 权限 |
| --- | --- |
| Microsoft Azure Active Directory | 读取目录数据 |
| Microsoft Graph | 读取所有审核日志数据 |


![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/36.png)

以下部分列出适用于这两个 API 的步骤。 如果你不想访问其中一个 API，可以跳过相关步骤。

**若要授予应用程序使用 API 的权限，请执行以下操作：**

1. 从“应用注册”  页面中选择你的应用程序，然后选择“设置”。  

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. 在“设置”  页面中，选择“所需的权限”  。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. 在“所需的权限”页的“API”列表中，单击“Windows Azure Active Directory”。    

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. 在  “启用访问”页上，选择“读取目录数据”  并取消选择“登录并读取用户个人资料”  。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 在顶部工具栏中，单击“保存”。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/15.png)

6. 在“所需权限”页顶部的工具栏中，单击“添加”   。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/32.png)

7. 在“添加 API 访问权限”页中，单击“选择 API”   。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/31.png)

8. 在“选择 API”页上，单击“Microsoft Graph”，然后单击“选择”    。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/33.png)

9. 在  “启用访问”页上，选择“读取所有审核日志数据”  ，然后单击“选择”  。  

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/34.png)

10. 在“添加 API 访问权限”页中，单击“完成”   。  

11. 在“所需权限”页顶部的工具栏中  ， 单击“授予权限”，然后单击“是”   。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/17.png)


## <a name="gather-configuration-settings"></a>收集配置设置 

本部分介绍了如何从目录中获取以下设置：

- 域名
- 客户端 ID
- 客户端机密

在配置对报告 API 的调用时需要这些值。 

### <a name="get-your-domain-name"></a>获取域名

**若要获取域名，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)中，在左侧导航窗格中，选择“Azure Active Directory”  。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”  页上，选择“自定义域名”  。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 从域列表中复制域名。


### <a name="get-your-applications-client-id"></a>获取应用程序的客户端 ID

**若要获取应用程序的客户端 ID，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”  。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 从“应用注册”  页面中选择你的应用程序。

3. 从应用程序页面中，导航到“应用程序 ID”  并选择“单击以复制”。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>获取应用程序的客户端机密
若要获取应用程序的客户端密码，需要创建新密钥并在保存新密钥时保存其值，因为它在以后无法再检索此值。

**若要获取应用程序的客户端机密，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”  。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  从“应用注册”  页面中选择你的应用程序。

3. 在应用程序页面上，在顶部的工具栏中，选择“设置”。  

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/05.png)

4. 在“设置”  页面上，在“API 访问”部分中，单击“密钥”。   

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. 在“密钥”页中执行以下步骤： 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/14.png)

    a. 在“说明”  文本框中，键入 `Reporting API`。

    b. 选择“2 年内”作为“过期时间”。  

    c. 单击“ **保存**”。

    d. 复制密钥值。

## <a name="troubleshoot-errors-in-the-reporting-api"></a>排查报告 API 中的错误

本部分列出了使用 MS Graph API 访问活动报告时可能遇到的常见错误消息及其解决步骤。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>访问 Microsoft Graph V2 终结点时出现 500 HTTP 内部服务器错误

目前，我们不支持 Microsoft Graph v2 终结点，请务必使用 Microsoft Graph v1 终结点访问活动日志。

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>错误：无法从 AD Graph 获取用户角色

尝试使用 Graph 浏览器访问登录时，可能会收到此错误消息。 确保使用 Graph 浏览器 UI 中的两个登录按钮登录帐户，如下图所示。 

![Graph 浏览器](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>错误：无法从 AD Graph 执行 Premium 许可证检查 

如果尝试使用 Graph 浏览器访问登录时收到此错误消息，请在左侧导航栏中选择帐户下方的“修改权限”，然后选择“Tasks.ReadWrite”和“Directory.Read.All”    。 

![修改权限 UI](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>错误：租户既不是 B2C，也没有 Premium 许可证

访问登录报告需要 Azure Active Directory Premium 1 (P1) 许可证。 如果访问登录时看到此错误消息，请确保你的租户已获得 Azure AD P1 许可证。

### <a name="error-user-is-not-in-the-allowed-roles"></a>错误：用户不是允许的角色 

如果在尝试使用 API 访问审核日志或登录时看到此错误消息，请确保帐户属于 Azure Active Directory 租户中的“安全读取者”或“报表读取者”角色   。 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>错误：应用程序缺少 AAD“读取目录数据”权限 

请按照[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>错误：应用程序缺少 MSGraph API“读取所有审核日志数据”权限

请按照[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

## <a name="next-steps"></a>后续步骤

* [使用具有证书的 Azure Active Directory 报告 API 获取数据](tutorial-access-api-with-certificates.md)
* [审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
