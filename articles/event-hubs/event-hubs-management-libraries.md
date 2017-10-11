---
title: "Azure 事件中心管理库 | Microsoft Docs"
description: "管理 .NET 中的事件中心命名空间和实体"
services: event-hubs
cloud: na
documentationcenter: na
author: sethmanheim
manager: timlt
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 0d659cb860a6c98342b548212820efe046decfcc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="event-hubs-management-libraries"></a>事件中心管理库

事件中心管理库可以动态设置事件中心命名空间和实体。 这样可以实现复杂的部署和消息传递方案，以便能够以编程方式确定要设置的实体。 这些库目前可用于 .NET。

## <a name="supported-functionality"></a>支持的功能

* 命名空间创建、更新、删除
* 事件中心创建、更新、删除
* 使用者组创建、更新、删除

## <a name="prerequisites"></a>先决条件

若要开始使用事件中心管理库，必须使用 Azure Active Directory (AAD) 进行身份验证。 AAD 要求身份验证为服务主体，且该主体提供对 Azure 资源的访问权限。 有关创建服务主体的信息，请参阅以下文章之一：  

* [使用 Azure 门户创建可访问资源的 Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [使用 Azure PowerShell 创建服务主体来访问资源](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [使用 Azure CLI 创建服务主体来访问资源](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

这些教程提供 `AppId`（客户端 ID）、`TenantId` 和 `ClientSecret`（身份验证密钥），这些都用于管理库进行的身份验证。 若要对资源组运行命令，必须拥有“所有者”权限。

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
