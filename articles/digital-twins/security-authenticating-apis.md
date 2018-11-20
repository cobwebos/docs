---
title: 了解 Azure 数字孪生 API 身份验证 | Microsoft Docs
description: 使用 Azure 数字孪生连接 API 并对其进行身份验证
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016210"
---
# <a name="connect-and-authenticate-to-apis"></a>连接 API 并对其进行身份验证

Azure 数字孪生使用 Azure Active Directory (Azure AD) 对用户进行身份验证并保护应用程序。 Azure AD 支持各种新式体系结构的身份验证。 所有这些体系结构都以行业标准协议 OAuth 2.0 或 OpenID Connect 为基础。 此外，开发人员可以使用 Azure AD 生成单租户和业务线 (LOB) 应用程序。 开发人员还可以使用 Azure AD 开发多租户应用程序。

有关 Azure AD 的概述，请访问[基础知识页](https://docs.microsoft.com/azure/active-directory/fundamentals/index)的分步指南、概念和快速入门。

若要将某个应用程序或服务与 Azure AD 进行集成，开发人员必须先将该应用程序注册到 Azure AD。 有关详细说明和屏幕截图，请参阅[此快速入门](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)。

Azure AD 支持[五种主要应用程序方案](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)：

* 单页应用程序 (SPA)：用户需要登录到受 Azure AD 保护的单页应用程序。
* Web 浏览器到 Web 应用程序：用户需要登录到受 Azure AD 保护的 Web 应用程序。
* 本机应用程序到 Web API：在手机、平板电脑或电脑上运行的本机应用程序需要对用户进行身份验证以通过受 Azure AD 保护的 Web API 获取资源。
* Web 应用程序到 Web API：Web 应用程序需要通过受 Azure AD 保护的 Web API 获取资源。
* 守护程序或服务器应用程序到 Web API：没有 Web UI 的守护程序应用程序或服务器应用程序需要通过受 Azure AD 保护的 Web API 获取资源。

Microsoft Azure 身份验证库提供多种方法，用于获取 Active Directory 令牌。 有关库和代码示例的详细信息，请参阅[此文](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)。

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>从中间层 Web API 调用数字孪生

当开发人员构建数字孪生解决方案时，通常会创建中间层应用程序或 API。 然后，该应用或 API 将调用下游数字孪生 API。 用户先对中间层应用程序进行身份验证，然后使用代理令牌流来调用下游。 有关如何协调代理流的说明，请参阅[此页](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 还可以在[此页](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)上查看代码示例。


## <a name="test-with-the-postman-client"></a>Postman 客户端测试

若要从数字孪生 API 着手，可以使用 Postman 等客户端作为 API 环境。 Postman 可助你快速创建复杂的 HTTP 请求。 以下步骤说明如何获取在 Postman UI 中调用数字孪生时所需的 Azure AD 令牌。


1. 转到 https://www.getpostman.com/ 下载该应用。
1. 遵循[此快速入门](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)中的步骤创建一个 Azure AD 应用程序。 或者，可以重复使用现有注册。 
1. 在“所需权限”下输入“Azure 数字孪生”，并选择“委托的权限”。 然后选择“授予权限”。
1. 打开应用程序清单，并将 **oauth2AllowImplicitFlow** 设置为 true。
1. 将回复 URL 配置为 [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback)。
1. 依次选择“授权”选项卡、“OAuth 2.0”、“获取新访问令牌”。

    |**字段**  |**值** |
    |---------|---------|
    | 授权类型 | 隐式 |
    | 回调 URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 身份验证 URL | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | 客户端 ID | 使用步骤 2 中创建或重复使用的 Azure AD 应用的应用程序 ID。 |
    | 范围 | 留空。 |
    | 状态 | 留空。 |
    | 客户端身份验证 | 作为“基本身份验证”标头发送。 |

1. 选择“请求令牌”。

    >[!NOTE]
    >如果收到错误消息“无法完成 OAuth 2”，请尝试以下操作：
    > * 关闭并重新打开 Postman，然后重试。
   
1. 向下滚动并选择“使用令牌”。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 数字孪生安全性的信息，请阅读[创建和管理角色分配](./security-create-manage-role-assignments.md)。
