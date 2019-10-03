---
title: 管理库 – Azure 事件中心 | Microsoft Docs
description: 本文提供有关可用于从 .NET 管理 Azure 事件中心命名空间和实体的库的信息。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746652"
---
# <a name="event-hubs-management-libraries"></a>事件中心管理库

可以使用 Azure 事件中心管理库动态设置事件中心命名空间和实体。 这种动态性可以实现复杂的部署和消息传送方案，以便能够以编程方式确定要预配的实体。 这些库目前可用于 .NET。

## <a name="supported-functionality"></a>支持的功能

* 创建、更新、删除命名空间
* 事件中心创建、更新、删除
* 使用者组创建、更新、删除

## <a name="prerequisites"></a>必备组件

若要开始使用事件中心管理库，必须使用 Azure Active Directory (AAD) 进行身份验证。 AAD 要求以提供 Azure 资源访问权限的服务主体身份进行身份验证。 有关创建服务主体的信息，请参阅以下文章之一：  

* [使用 Azure 门户创建可访问资源的 Active Directory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)
* [使用 Azure PowerShell 创建服务主体来访问资源](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [使用 Azure CLI 创建服务主体来访问资源](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

这些教程提供 `AppId`（客户端 ID）、`TenantId` 和 `ClientSecret`（身份验证密钥），这些都用于管理库进行的身份验证。 若要对资源组运行命令，必须拥有“所有者”  权限。

## <a name="programming-pattern"></a>编程模式

操作任何事件中心资源的模式都遵循通用协议：

1. 使用 `Microsoft.IdentityModel.Clients.ActiveDirectory` 库从 AAD 获取令牌。
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. 创建 `EventHubManagementClient` 对象。
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. 将 `CreateOrUpdate` 参数设置为指定值。
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. 执行调用。
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>后续步骤
* [.NET 管理示例](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub 引用](/dotnet/api/Microsoft.Azure.Management.EventHub) 
