---
title: 使用 Azure Active Directory 对托管标识进行身份验证
description: 本文提供有关对使用 Azure Active Directory 访问 Azure 事件中心资源的托管标识进行身份验证的信息
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: dfc60fbc03021e72dccc0f60a7ac34d204ef6df9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025180"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>使用 Azure Active Directory 对托管标识的事件中心资源访问进行身份验证
Azure 事件中心支持使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授权对事件中心资源的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。

本文介绍如何在 Azure VM 中使用托管标识授予对事件中心的访问权限。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识
在使用 Azure 资源的托管标识对 VM 中的事件中心资源授权之前，必须首先在 VM 上启用 Azure 资源的托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向 Azure AD 中的托管标识授予权限
若要通过应用程序中的托管标识授权对事件中心服务的请求，请先为该托管标识配置基于角色的访问控制 (RBAC) 设置。 Azure 事件中心定义 RBAC 角色，这些角色涵盖了从事件中心进行发送和读取操作所需的权限。 将 RBAC 角色分配到某个托管标识后，将在适当的范围授予该托管标识访问事件中心数据的权限。

有关如何分配 RBAC 角色的详细信息，请参阅[使用 Azure Active Directory 进行身份验证，以便访问事件中心资源](authorize-access-azure-active-directory.md)。

## <a name="use-event-hubs-with-managed-identities"></a>将事件中心与托管标识结合使用
若要将事件中心与托管标识配合使用，需为标识分配角色和相应的范围。 此部分的过程使用一个简单的应用程序，该应用程序在托管标识下运行并访问事件中心资源。

在这里，我们将使用一个在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中托管的示例 Web 应用程序。 有关如何创建 Web 应用程序的分步说明，请参阅[在 Azure 中创建 ASP.NET Core Web 应用](../app-service/app-service-web-get-started-dotnet.md)

创建应用程序后，请执行以下步骤： 

1. 转到“设置”，然后选择“标识”  。  
1. 选择“状态”  ，将其切换到“启用”  。 
1. 选择“保存”  ，保存设置。 

    ![Web 应用的托管标识](./media/authenticate-managed-identity/identity-web-app.png)

启用此设置后，会在 Azure Active Directory (Azure AD) 中创建一个新的服务标识并将其配置到应用服务主机中。

现在，请将此服务标识分配给事件中心资源中所需范围中的某个角色。

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色
若要为事件中心资源分配角色，请导航到 Azure 门户中的该资源。 显示资源的“访问控制(标识和访问管理)”设置，并按以下说明管理角色分配：

> [!NOTE]
> 以下步骤为事件中心命名空间分配服务标识角色。 可以遵循相同的步骤来分配限定为事件中心资源范围的角色。 

1. 在 Azure 门户中导航到事件中心命名空间，显示该命名空间的“概览”。  
1. 选择左侧菜单上的“访问控制(标识和访问管理)”，显示事件中心的访问控制设置  。
1.  选择“角色分配”  选项卡以查看角色分配列表。
3.  选择“添加”以添加新角色。 
4.  在“添加角色分配”页上，选择要分配的事件中心角色  。 然后通过搜索找到已注册的服务标识，以便分配该角色。
    
    ![“添加角色分配”页](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  选择“保存”  。 分配有该角色的标识列出在该角色下。 例如，下图显示服务标识有事件中心数据所有者。
    
    ![分配给角色的标识](./media/authenticate-managed-identity/role-assigned.png)

分配此角色后，Web 应用程序即可访问已定义范围内的事件中心资源。 

### <a name="test-the-web-application"></a>测试 Web 应用程序
1. 创建事件中心命名空间和事件中心。 
2. 将 Web 应用部署到 Azure。 请参阅下面的选项卡式部分，获取 GitHub 上的 Web 应用程序的链接。 
3. 确保将 SendReceive.aspx 设置为 Web 应用的默认文档。 
3. 为 Web 应用启用**标识**。 
4. 将此标识分配给命名空间级别或事件中心级别的“事件中心数据所有者”  角色。 
5. 运行 Web 应用程序，输入命名空间名称和事件中心名称，输入一条消息，然后选择“发送”  。 若要接收事件，请选择“接收”。  

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs（最新）](#tab/latest)
现在可以启动 Web 应用程序并将浏览器指向示例 aspx 页面了。 可以在 [GitHub 存储库](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)中找到用于通过事件中心资源发送和接收数据的示例 Web 应用程序。

安装 [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) 中的最新包，开始使用 **EventHubProducerClient** 向事件中心发送事件，使用 **EventHubConsumerClient** 接收事件。 

> [!NOTE]
> 有关使用托管标识将事件发布到事件中心的 Java 示例，请参阅[在 GitHub 上使用 Azure 标识示例发布事件](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)。

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs（旧版）](#tab/old)
现在可以启动 Web 应用程序并将浏览器指向示例 aspx 页面了。 可以在 [GitHub 存储库](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)中找到用于通过事件中心资源发送和接收数据的示例 Web 应用程序。

从 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 安装最新包，并开始使用 EventHubClient 通过事件中心发送和接收数据，如以下代码所示： 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>用于 Kafka 的事件中心
您可以使用 Apache Kafka 应用程序使用托管标识 OAuth 向 Azure 事件中心发送消息并从 Azure 事件中心接收消息。 请参阅 GitHub 上的以下示例[：Kafka 的事件中心 - 使用托管标识 OAuth 发送和接收消息](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)。

## <a name="samples"></a>示例
- **Azure.消息传递.事件中心**示例
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [微软.Azure.事件中心示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    这些示例使用旧的**Microsoft.Azure.EventHubs**库，但可以轻松地将其更新为使用最新的**Azure.消息.事件中心**库。 要将示例从使用旧库移动到新库，请参阅[从 Microsoft.Azure.事件中心迁移到 Azure.消息集的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
    此示例已更新为使用最新的**Azure.消息.事件中心**库。
- [卡夫卡的事件中心 - 使用托管标识 OAuth 发送和接收消息](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>后续步骤
- 请参阅以下文章以了解 Azure 资源的托管标识[：Azure 资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md)
- 请参阅以下相关文章：
    - [使用 Azure Active Directory 对应用程序的 Azure 事件中心请求进行身份验证](authenticate-application.md)
    - [使用共享访问签名对 Azure 事件中心请求进行身份验证](authenticate-shared-access-signature.md)
    - [使用 Azure 活动目录授权访问事件中心资源](authorize-access-azure-active-directory.md)
    - [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)