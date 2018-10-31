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
ms.openlocfilehash: dc5570b188bfdc0e1be78aa2bd5c5d92e884f377
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638012"
---
# <a name="connect-and-authenticate-to-apis"></a>连接 API 并对其进行身份验证

Azure 数字孪生使用 Azure Active Directory (Azure AD) 对用户进行身份验证并保护应用程序。 Azure AD 支持各种新型体系结构的身份验证，所有这些体系结构都基于行业标准协议 OAuth 2.0 或 OpenID Connect。 此外，Azure AD 支持开发人员生成单租户业务线 (LOB) 应用程序和开发多租户应用程序。

有关 Azure AD 的概述，请访问[基础知识页](https://docs.microsoft.com/azure/active-directory/fundamentals/index)的分步指南、概念和快速入门。

若要将某个应用程序或服务与 Azure AD 进行集成，开发人员必须先将该应用程序注册到 Azure AD。 如需详细说明和屏幕截图，请参阅[此处](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)的说明

下面是 Azure AD 支持的[五种主要应用程序方案](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)：

* 单页应用程序 (SPA)：用户需要登录到受 Azure AD 保护的单页应用程序。
* Web 浏览器到 Web 应用程序：用户需要登录到受 Azure AD 保护的 Web 应用程序。
* 本机应用程序到 Web API：在手机、平板电脑或电脑上运行的本机应用程序需要对用户进行身份验证以通过受 Azure AD 保护的 Web API 获取资源。
* Web 应用程序到 Web API：Web 应用程序需要通过受 Azure AD 保护的 Web API 获取资源。
* 守护程序或服务器应用程序到 Web API：没有 Web 用户界面的守护程序应用程序或服务器应用程序需要通过受 Azure AD 保护的 Web API 获取资源。

Microsoft Azure 身份验证库提供多种方法，用于获取 Active Directory 令牌。 如需深入了解库以及代码示例，请参阅[此处](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)。

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>从中间层 Web API 调用数字孪生

构建数字孪生解决方案时，开发人员通常选择创建中间层应用程序或 API，然后调用下游数字孪生 API。 用户会先对中间层应用程序进行身份验证，然后在调用下游时将使用代理令牌流。 有关如何安排代理流的详细说明，请访问[本页面](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)。 还可以在[此处](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)查看代码示例。


## <a name="test-with-the-postman-client"></a>Postman 客户端测试

为了设置并运行数字孪生 API，可以使用客户端（如 Postman）作为 API 环境。 Postman 可助你快速创建复杂的 HTTP 请求。 下面的说明将重点介绍如何获取在 Postman UI 内调用数字孪生所需的 Azure AD 令牌。


1. 导航到 https://www.getpostman.com/ 下载该应用
1. 请按照[此处](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)的步骤创建 Azure Active Directory 应用程序（也可以选择重复使用现有注册）。 
1. 在所需权限下添加“Azure 数字孪生”，然后选择“委托的权限”。 不要忘记单击“授予权限”完成授权。
1. 打开应用程序清单，并将“oauth2AllowImplicitFlow”设置为“true”
1. 将回复 URL 配置为 [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback)。
1. 选择“授权”选项卡，单击“OAuth 2.0”，然后选择“获取新访问令牌”。

    |**字段**  |**值** |
    |---------|---------|
    | 授权类型 | 隐式 |
    | 回调 URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 身份验证 URL | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | 客户端 ID | 使用步骤 1 中创建或重新使用的 Azure AD 应用的应用程序 ID |
    | 范围 | 留空 |
    | 状态 | 留空 |
    | 客户端身份验证 | 作为“基本身份验证”标头发送 |

1. 单击“请求令牌”。

    >[!NOTE]
    >如果收到错误消息“无法完成 OAuth 2”，请尝试以下操作：
    > * 关闭并重新打开 Postman，然后重试。
   
1. 向下滚动并单击“使用令牌”。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 数字孪生安全性的信息，请阅读[创建和管理角色分配](./security-create-manage-role-assignments.md)。
