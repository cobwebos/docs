---
title: Azure Active Directory 报告 API 的先决条件 |Microsoft Docs
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
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f25f13c60a60e1a397e4c63443ee786a9acdf6d1
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273798"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>访问 Azure Active Directory 报告 API 的先决条件

[Azure Active Directory (Azure AD) 报告 API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可以从编程语言和工具中调用这些 Api。

报告 API 使用 [OAuth](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) 授权访问 Web API。

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

即使使用脚本访问报告 API，也需要注册。 注册为你提供了一个**应用程序 ID**，该 ID 是授权调用所必需的，并使你的代码能够接收令牌。

若要配置目录以访问 Azure AD 报告 API，必须使用同为 Azure AD 租户中[全局管理员](https://portal.azure.com)目录角色成员的 Azure 管理员帐户登录到 **Azure 门户**。

> [!IMPORTANT]
> 在具有管理员权限的凭据下运行的应用程序可能具有非常强大的功能，因此请务必将应用程序的 ID 和机密凭据放置在一个安全的位置。
> 

**若要注册 Azure AD 应用程序，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)中，从左侧导航窗格中选择“Azure Active Directory”。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”页面中，选择“应用注册”。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. 从 "**应用注册**" 页中，选择 "**新建注册**"。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. "**注册应用程序**" 页：

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. 在“名称”文本框中，键入 `Reporting API application`。

    b. 对于 "**支持的帐户类型**"，请选择 "**仅限此组织中的帐户**"。

    c. 在 "**重定向 URL** **" 文本框中**，键入 `https://localhost`。

    d. 选择“注册”。 


## <a name="grant-permissions"></a>授予权限 

根据想要访问的 API，你需要向应用授予以下权限：  

| API | 权限 |
| --- | --- |
| Microsoft Azure Active Directory | 读取目录数据 |
| Microsoft Graph | 读取所有审核日志数据 |


![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/36.png)

以下部分列出适用于这两个 API 的步骤。 如果你不想访问其中一个 API，可以跳过相关步骤。

**若要授予应用程序使用 API 的权限，请执行以下操作：**


1. 依次选择 " **API 权限**" 和 "**添加权限**"。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. 在 "**请求 API 权限" 页**上，找到 "**支持旧版 Api** **Azure Active Directory 关系图**。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. 在 "**所需权限**" 页上，依次选择 "**应用程序权限**"、"**目录** **ReadAll**"。  选择“添加权限”。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. 在 "**报告 Api 应用程序-API 权限**" 页上，选择 "**授予管理员许可**"。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 注意：默认情况下，在 API 注册过程中会添加**Microsoft Graph** 。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>收集配置设置 

本部分介绍了如何从目录中获取以下设置：

- 域名
- 客户端 ID
- 客户端机密

在配置对报告 API 的调用时需要这些值。 

### <a name="get-your-domain-name"></a>获取域名

**若要获取域名，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)中，在左侧导航窗格中，选择“Azure Active Directory”。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”页上，选择“自定义域名”。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 从域列表中复制域名。


### <a name="get-your-applications-client-id"></a>获取应用程序的客户端 ID

**若要获取应用程序的客户端 ID，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 从“应用注册”页面中选择你的应用程序。

3. 从应用程序页面中，导航到“应用程序 ID”并选择“单击以复制”。

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>获取应用程序的客户端机密
 避免尝试使用 API 访问审核日志或登录时出现错误。

**若要获取应用程序的客户端机密，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”。
   
    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  从“应用注册”页面中选择你的应用程序。

3.  在**API 应用程序**页上选择 "**证书和机密**"，在 "**客户端密码**" 部分中，单击 " **+ 新建客户端密钥**"。 

    ![注册应用程序](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. 在 "**添加客户端密钥**" 页面上，添加：

    a. 在“说明”文本框中，键入 `Reporting API`。

    b. 选择“2 年内”作为“过期时间”。

    c. 单击“ **保存**”。

    d. 复制密钥值。

## <a name="troubleshoot-errors-in-the-reporting-api"></a>排查报告 API 中的错误

本部分列出了使用 Microsoft Graph API 访问活动报告时可能会遇到的常见错误消息，以及解决方法的步骤。


### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>错误：未能从 Microsoft Graph 获取用户角色

 使用图形资源管理器 UI 中的登录按钮登录到你的帐户，以避免尝试使用图形资源管理器登录时出现错误。 

![Graph 浏览器](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>错误：未能从 Microsoft Graph 进行高级许可证检查 

如果尝试使用 Graph 浏览器访问登录时收到此错误消息，请在左侧导航栏中选择帐户下方的“修改权限”，然后选择“Tasks.ReadWrite”和“Directory.Read.All”。 

![修改权限 UI](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>错误：租户不是 B2C 或租户没有高级版许可证

访问登录报告需要 Azure Active Directory Premium 1 (P1) 许可证。 如果访问登录时看到此错误消息，请确保你的租户已获得 Azure AD P1 许可证。

### <a name="error-the-allowed-roles-does-not-include-user"></a>错误：允许的角色不包括用户。 

 避免尝试使用 API 访问审核日志或登录时出现错误。 请确保你的帐户是 Azure Active Directory 租户中的**安全读者**或**报表读者**角色的一部分。

### <a name="error-application-missing-aad-read-directory-data-permission"></a>错误: 应用程序缺少 AAD“读取目录数据”权限 

### <a name="error-application-missing-microsoft-api-read-all-audit-log-data-permission"></a>错误：应用程序缺少 Microsoft API "读取所有审核日志数据" 权限

按照先决条件中的步骤[访问 Azure Active Directory 报告 API](howto-configure-prerequisites-for-reporting-api.md) ，以确保你的应用程序使用正确的权限集运行。 

## <a name="next-steps"></a>后续步骤

* [使用具有证书的 Azure Active Directory 报告 API 获取数据](tutorial-access-api-with-certificates.md)
* [审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [登录活动报告 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
