---
title: "Azure 服务总线管理库| Microsoft 文档"
description: "管理 .NET 中的服务总线命名空间和实体"
services: service-bus-messaging
cloud: na
documentationcenter: na
author: jtaubensee
manager: timlt
ms.assetid: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/6/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: dfd1ae52cc56a4d4b4c7ee3f69f0c454be607401
ms.openlocfilehash: 7b04227327235f788ecf4018b6c159d33113b63d


---

# <a name="service-bus-management-libraries"></a>服务总线管理库

服务总线管理库可以动态设置服务总线命名空间和实体。 这样能够以编程方式确定要设置的实体，从而实现复杂的部署和消息传递方案。 这些库目前可用于 .NET。

## <a name="supported-functionality"></a>支持的功能

* 命名空间创建、更新、删除
* 队列创建、更新、删除
* 主题创建、更新、删除
* 订阅创建、更新、删除

## <a name="prerequisites"></a>先决条件

若要开始使用服务总线管理库，必须使用 Azure Active Directory (AAD) 进行身份验证。 AAD 要求身份验证为服务主体，且该主体提供对 Azure 资源的访问权限。 有关创建服务主体的信息，请参阅以下文章之一：  

* [使用 Azure 门户创建可访问资源的 Active Directory 应用程序和服务主体](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal)
* [使用 Azure PowerShell 创建服务主体来访问资源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [使用 Azure CLI 创建服务主体来访问资源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)

这些教程将提供 `AppId`（客户端 ID）、`TenantId` 和 `ClientSecret`（身份验证密钥），这些都将用于管理库进行的身份验证。 对于要在其上运行的资源组，必须具有“所有者”权限。

## <a name="programming-pattern"></a>编程模式

操作任何服务总线资源的模式都遵循通用协议：

1. 使用 `Microsoft.IdentityModel.Clients.ActiveDirectory` 库从 Azure Active Directory 获取令牌。
    ```csharp
    var context = new AuthenticationContext($"https://login.windows.net/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. 创建 `ServiceBusManagementClient` 对象。
    ```csharp
    var creds = new TokenCredentials(token);
    var sbClient = new ServiceBusManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. 将 CreateOrUpdate 参数设置为指定值。
    ```csharp
    var queueParams = new QueueCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"],
        EnablePartitioning = true
    };
    ```

1. 执行调用。
    ```csharp
    await sbClient.Queues.CreateOrUpdateAsync(resourceGroupName, namespaceName, QueueName, queueParams);
    ```

## <a name="next-steps"></a>后续步骤
* [.NET 管理示例](https://github.com/Azure-Samples/service-bus-dotnet-management/)
* [Microsoft.Azure.管理服务总线参考](/dotnet/api/Microsoft.Azure.Management.ServiceBus)



<!--HONumber=Jan17_HO3-->


