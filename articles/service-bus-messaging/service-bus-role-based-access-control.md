---
title: Azure 服务总线基于角色的访问控制 (RBAC) 预览版 | Microsoft Docs
description: Azure 服务总线基于角色的访问控制
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 729d6db6b2fc6495ffb0f4fbe4d545d7ad953cef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783330"
---
# <a name="active-directory-role-based-access-control-preview"></a>Active Directory 基于角色的访问控制（预览版）

Microsoft Azure 基于 Azure Active Directory (Azure AD) 针对资源和应用程序提供了集成的访问控制管理功能。 使用 Azure AD，可以专门为基于 Azure 的应用程序管理用户帐户和应用程序，还可以将现有的 Active Directory 基础结构与 Azure AD 联合起来以实现公司范围内的单一登录，该单一登录还涵盖了 Azure 资源和 Azure 托管的应用程序。 然后，你可以将那些 Azure AD 用户和应用程序标识分配给全局和特定于服务的角色以授予对 Azure 资源的访问权限。

对于 Azure 服务总线，通过 Azure 门户和 Azure 资源管理 API 对命名空间和所有相关资源的管理已使用*基于角色的访问控制* (RBAC) 模型进行了保护。 针对运行时操作的 RBAC 目前为公共预览版。 

使用 Azure AD RBAC 的应用程序不需要处理 SAS 规则和密钥，也不需要处理特定于服务总线的任何其他访问令牌。 客户端应用与 Azure AD 进行交互来建立身份验证上下文，并获取服务总线的访问令牌。 使用需要交互式登录的域用户帐户，应用程序从不直接处理任何凭据。

## <a name="service-bus-roles-and-permissions"></a>服务总线角色和权限

对于初始公共预览版，只能将 Azure AD 帐户和服务主体添加到服务总线消息传送命名空间的“所有者”或“参与者”角色。 此操作向标识授予对命名空间中的所有实体的完全控制权限。 最初只能通过 Azure 资源管理来支持对命名空间拓扑进行更改的管理操作，不能通过本机服务总线 REST 管理接口来支持。 此支持还意味着 .NET Framework 客户端 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 对象无法与 Azure AD 帐户一起使用。  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>将服务总线与 Azure AD 域用户帐户一起使用

下面的部分介绍了创建和运行一个示例应用程序（该应用程序提示交互式 Azure AD 用户进行登录）所需的步骤，如何向该用户帐户授予服务总线访问权限，以及如何使用该标识来访问事件中心。 

此介绍描述了一个简单的控制台应用程序，[其代码位于 Github 上](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)。

### <a name="create-an-active-directory-user-account"></a>创建 Active Directory 用户帐户

这第一个步骤是可选的。 每个 Azure 订阅都会自动与一个 Azure Active Directory 租户进行配对，如果你具有某个 Azure 订阅的访问权限，则你的用户帐户已经注册。 这意味着你只能使用你的帐户。 

如果仍然希望为此方案创建特定帐户，请[执行这些步骤](../automation/automation-create-aduser-account.md)。 你必须有权在 Azure Active Directory 租户中创建帐户，对于较大的企业方案，这可能不现实。

### <a name="create-a-service-bus-namespace"></a>创建服务总线命名空间

接下来，在支持 RBAC 预览版的 Azure 区域（**美国东部**、**美国东部 2** 或**西欧**）之一中[创建服务总线消息传递命名空间](service-bus-create-namespace-portal.md)。 

在创建命名空间后，在门户上导航到其“访问控制(标识和访问管理)”页面，然后单击“添加”将 Azure AD 用户帐户添加到“所有者”角色。 如果你使用自己的用户帐户并且已创建了命名空间，则你已在“所有者”角色中。 若要向角色添加一个不同的帐户，请在“添加权限”面板的“选择”字段中搜索 Web 应用程序的名称，然后单击该条目。 然后单击“保存”。

![](./media/service-bus-role-based-access-control/rbac1.PNG)

用户帐户现在已具有对服务总线命名空间和对之前创建的队列的访问权限。
 
### <a name="register-the-application"></a>注册应用程序

在可以运行示例应用程序之前，将它注册到 Azure AD 中并批准允许应用程序以其身份访问 Azure 服务总线的许可提示。 

因为示例应用程序是一个控制台应用程序，因此你必须注册一个本机应用程序并将 **Microsoft.ServiceBus** 的 API 权限添加到“必需的权限”集。 本机应用程序在 Azure AD 中还需要有一个充当标识符的 **redirect-URI**，该 URI 不需要是网络目的地。 对于此示例请使用 `http://servicebus.microsoft.com`，因为示例代码已使用了该 URI。

[此教程](../active-directory/develop/active-directory-integrating-applications.md)中介绍了详细的注册步骤。 请按照那些步骤注册一个**本机**应用，然后按照更新说明将 **Microsoft.ServiceBus** API 添加到必需的权限。 执行那些步骤时，请记下 **TenantId** 和 **ApplicationId**，因为你将使用这些值来运行应用程序。

### <a name="run-the-app"></a>运行应用程序

在可以运行示例前，编辑 App.config 文件并根据你的方案设置以下值：

- `tenantId`：设置为 **TenantId** 值。
- `clientId`：设置为 **ApplicationId** 值。 
- `clientSecret`：如果希望使用客户端机密进行登录，请在 Azure AD 中创建它。 此外，请使用 Web 应用或 API 而非本机应用。 另外，请在之前创建的命名空间中将该应用添加到 **Access Control (IAM)** 下。
- `serviceBusNamespaceFQDN`：设置为新创建的服务总线命名空间的完整 DNS 名称，例如 `example.servicebus.windows.net`。
- `queueName`：设置为你创建的队列的名称。
- 在前面的步骤中你在应用中指定的重定向 URI。
 
运行该控制台应用程序时，会提示你选择一个方案，请通过键入相应的编号并按 ENTER 来选择 **Interactive User Login**。 应用程序会显示一个登录窗口，要求你同意访问服务总线，然后使用登录标识通过该服务来演练发送/接收方案。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)