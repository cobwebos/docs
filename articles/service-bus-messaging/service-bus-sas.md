---
title: "使用共享访问签名进行 Azure 服务总线访问控制 | Microsoft Docs"
description: "根据如何使用共享访问签名进行服务总线访问控制，并详细介绍如何使用 Azure 服务总线进行 SAS 授权。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: sethm;clemensv
ms.openlocfilehash: f6bb77ad6df09e36419b24b24924dac7ecd79065
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>使用共享访问签名进行服务总线访问控制

共享访问签名 (SAS) 是服务总线消息传送的主要安全机制。 本文介绍 SAS、其工作原理以及如何以平台无关的方式使用它们。

SAS 可以根据授权规则来保护对服务总线的访问。 可以在命名空间或消息传递实体（中继、队列或主题）中配置这些保护。 授权规则具有与特定权限关联的名称，并包含一个加密密钥对。 通过服务总线 SDK 或者在自己的代码中使用规则名称和密钥可以生成 SAS 令牌。 然后，客户端可将令牌传递给服务总线，以证明请求的操作获得授权。

## <a name="overview-of-sas"></a>SAS 概述

共享访问签名是使用简单令牌的基于声明的授权机制。 使用 SAS 时，永远不会通过网络传递密钥。 密钥用于以加密方式将信息签名，以后，服务可以验证这些信息。 可以像使用用户名和密码一样使用 SAS。在用户名和密码方案中，客户端直接拥有授权规则名称和匹配的密钥。 此外，还可以像在联合安全模型中一样使用 SAS。在此模型中，客户端从安全令牌服务接收限时且经过签名的访问令牌，而无需拥有签名密钥。

服务总线中的 SAS 身份验证配置了指定的[共享访问授权规则](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)，这些规则具有关联的访问权限，以及一对主要和辅助加密密钥。 密钥是采用 Base64 表示法的 256 位值。 在服务总线的[中继](service-bus-fundamentals-hybrid-solutions.md#relays)、[队列](service-bus-fundamentals-hybrid-solutions.md#queues)和[主题](service-bus-fundamentals-hybrid-solutions.md#topics)中，可以在命名空间级别配置规则。

[共享访问签名](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider)令牌包含所选授权规则的名称、应访问的资源的 URI、即时过期时间，以及使用所选授权规则的主要或辅助加密密钥基于这些字段计算的 HMAC-SHA256 加密签名。

## <a name="shared-access-authorization-policies"></a>共享访问授权策略

每个服务总线命名空间和服务总线实体都有一个由规则构成的共享访问授权策略。 命名空间级别的策略应用到该命名空间中的所有实体，不管这些实体各自的策略配置如何。

对于每个授权策略规则，需要确定三个信息片段：**名称**、**范围**和**权限**。 **名称**只是该范围内的唯一名称。 范围也很简单：它是相关资源的 URI。 对于服务总线命名空间，范围是完全限定的域名 (FQDN)，例如 `https://<yournamespace>.servicebus.windows.net/`。

策略规则授予的权限可以是以下各项的组合：

* “发送”- 授予向实体发送消息的权限
* “侦听”- 授予侦听（中继）或接收（队列、订阅）和所有相关消息处理的权限
* “管理”- 授予管理命名空间的拓扑的权限，包括创建和删除实体

“管理”权限包括“发送”和“接收”权限。

一个命名空间或实体策略最多可以包含 12 个共享访问授权规则，为三组规则提供空间。其中每个规则涵盖了基本权限，以及“发送”和“侦听”权限的组合。 根据此项限制，很明显 SAS 策略存储并不适合用作用户或服务帐户存储。 如果应用程序需要根据用户或服务标识授予服务总线的访问权限，应实现安全令牌服务，以便在执行身份验证和访问检查后颁发 SAS 令牌。

将为授权规则分配主要密钥和辅助密钥。 它们是加密形式的强密钥。 请不要遗失或透漏这些密钥 - 它们在 [Azure 门户][Azure portal]中始终可用。 可以使用其中一个生成的密钥，并且随时可以重新生成密钥。 如果重新生成或更改策略中的密钥，以前基于该密钥颁发的所有令牌会立即失效。 但是，基于此类令牌创建的现有连接将继续工作，直到该令牌过期。

创建服务总线命名空间时，系统会自动为该命名空间创建名为 **RootManageSharedAccessKey** 的策略规则。 此策略具有整个命名空间的“管理”权限。 建议将此规则视为 **root** 管理帐户，且不要在应用程序中使用它。 可以通过门户上命名空间的“配置”选项卡、Powershell 或 Azure CLI 创建更多策略规则。

## <a name="configuration-for-shared-access-signature-authentication"></a>共享访问签名身份验证的配置

可以在服务总线命名空间、队列，或主题上配置 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 规则。 当前不支持在服务总线订阅上配置 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)，但是可以使用命名空间或主题上配置的规则来确保安全访问订阅。 有关说明此过程的工作示例，请参阅 [Using Shared Access Signature (SAS) authentication with Service Bus Subscriptions](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)（将共享访问签名 (SAS) 身份验证与服务总线订阅配合使用）示例。

![SAS](./media/service-bus-sas/service-bus-namespace.png)

在此图中，manageRuleNS、sendRuleNS 和 listenRuleNS 授权规则适用于队列 Q1 和主题 T1，而 listenRuleQ 和 sendRuleQ 仅适用于队列 Q1，sendRuleT 仅适用于主题 T1。

## <a name="generate-a-shared-access-signature-token"></a>生成共享访问签名令牌

有权访问授权规则的名称及其某个签名密钥的任何客户端都可以生成 SAS 令牌。 令牌是通过采用以下格式编写一个字符串而生成的：

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** - 令牌即时过期时间。 一个整数，反映自 1970 年 1 月 1 日令牌过期的时期 `00:00:00 UTC`（UNIX 时期）以来的秒数。
* **`skn`** - 授权规则的名称。
* **`sr`** - 所访问资源的 URI。
* **`sig`** - 签名。

`signature-string` 是基于资源 URI 计算的 SHA-256 哈希（上一部分中所述的**范围**），以及令牌即时过期时间的字符串表示形式，以 CRLF 分隔。

哈希计算方式如以下虚拟代码所示，返回 256 位/32 字节哈希值。

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

令牌包含非哈希值，使接收方可以使用相同的参数重新计算哈希，并验证颁发者是否拥有有效的签名密钥。 

资源 URI 是向其声明访问权限的服务总线资源的完整 URI。 例如，`http://<namespace>.servicebus.windows.net/<entityPath>` 或 `sb://<namespace>.servicebus.windows.net/<entityPath>`；即，`http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`。 URI 必须采用[百分比编码](https://msdn.microsoft.com/library/4fkewx0t.aspx)。

用于签名的共享访问授权规则必须在此 URI 指定的实体上，或由其分层父级之一进行配置。 例如，前面的示例中的 `http://contoso.servicebus.windows.net/contosoTopics/T1` 或 `http://contoso.servicebus.windows.net`。

SAS 令牌对于以 `signature-string` 中使用的 `<resourceURI>` 为前缀的所有资源有效。

## <a name="regenerating-keys"></a>重新生成密钥

建议定期重新生成 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 对象中使用的密钥。 提供了主要和辅助密钥槽，以便可以逐步轮换密钥。 如果应用程序通常使用主要密钥，则可将主要密钥复制到辅助密钥槽，并只在完成此过程后才重新生成主要密钥。 然后，可以在客户端应用程序（可以使用辅助槽中的旧主要密钥继续访问）中配置新的主要密钥值。 更新所有客户端后，可以重新生成辅助密钥，以便最终停用旧主要密钥。

如果你知道或者怀疑密钥已泄漏，因此必须吊销这些密钥，则可以同时生成 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 的 [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) 和 [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey)，并用新的密钥替换它们。 此过程将使得由旧密钥签名的所有令牌失效。

## <a name="shared-access-signature-authentication-with-service-bus"></a>服务总线的共享访问签名身份验证

下面所述的方案包括配置授权规则、生成 SAS 令牌和客户端授权。

有关演示配置和使用 SAS 授权的服务总线应用程序的完整工作示例，请参阅 [Shared Access Signature authentication with Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8)（服务总线的共享访问签名身份验证）。 演示如何使用为保护服务总线订阅，在命名空间或主题上配置的 SAS 授权规则的相关示例位于此处：[Using Shared Access Signature (SAS) authentication with Service Bus Subscriptions](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)（将共享访问签名 (SAS) 身份验证与服务总线订阅配合使用）。

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>访问实体上的共享访问授权规则

使用服务总线 .NET Framework 库，可通过相应 [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) 或 [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) 中的 [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) 集合，访问在服务总线队列或主题上配置的 [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 对象。

下面的代码演示了如何向队列添加授权规则。

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>使用共享访问签名授权

使用具有服务总线 .NET 库的 Azure.NET SDK 的应用程序可以通过 [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) 类使用 SAS 授权。 下面的代码演示了使用令牌提供程序向服务总线队列发送消息。 如果不使用此处所示的代码，还可以向令牌提供程序工厂方法传递以前颁发的令牌。

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

此外，可以直接使用令牌提供程序来颁发要传递给其他客户端的令牌。 

连接字符串可以包含规则名称 (*SharedAccessKeyName*) 和规则密钥 (*SharedAccessKey*) 或以前颁发的令牌 (*SharedAccessSignature*)。 如果传递给接受连接字符串的任何构造函数或工厂方法中包含这些参数，则系统会自动创建并填充 SAS 令牌提供程序。

请注意，要使用服务总线中继的 SAS 授权，可以使用服务总线命名空间上配置的 SAS 密钥。 如果在命名空间上显式创建中继（[NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 与 [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)）对象，可以只为该中继设置 SAS 规则。 若要使用服务总线订阅的 SAS 授权，可以使用服务总线命名空间或主题上配置的 SAS 密钥。

## <a name="use-the-shared-access-signature-at-http-level"></a>使用共享访问签名（在 HTTP 级别）

在了解如何为服务总线中的任何实体创建共享访问签名后，便可以执行 HTTP POST 了：

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

请记住，这适用于所有情况。 可以为队列、主题或订阅创建 SAS。

如果为发送方或客户端提供 SAS 令牌，它们不会直接获取密钥，并且他们无法逆向改编哈希来获取它。 因此，可以控制它们有权访问的项，以及可访问的时间长短。 要记住的一个重点是，如果更改策略中的主密钥，基于该密钥创建的所有共享访问签名都将失效。

## <a name="use-the-shared-access-signature-at-amqp-level"></a>使用共享访问签名（在 AMQP 级别）

在前一部分中，已了解如何使用 SAS 令牌配合 HTTP POST 请求将数据发送到服务总线。 如你所了解，可以使用高级消息队列协议 (AMQP) 来访问服务总线。在许多方案中，都会出于性能原因而将该协议用作首选协议。 文档[基于 AMQP 声明的安全性版本 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc)（自 2013 年以来以有效草案版推出，不过 Azure 现在能够很好地支持它）中介绍了如何配合 AMQP 使用 SAS 令牌。

开始将数据发送到服务总线之前，发布者必须将 AMQP 消息中的 SAS 令牌发送到正确定义的名为 $cbs 的 AMQP 节点（可以将它视为一个由服务使用的“特殊”队列，用于获取和验证所有 SAS 令牌）。 发布者必须在 AMQP 消息中指定 **ReplyTo** 字段；这是服务向发布者回复令牌验证结果（发布者与服务之间的简单请求/回复模式）时所在的节点。 根据 AMQP 1.0 规范中有关“动态创建远程节点”的论述，此回复节点是“在运行中”创建的。 在检查 SAS 令牌有效之后，发布者可以继续将数据发送到服务。

下面的步骤演示如何使用 [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) 库通过 AMQP 协议发送 SAS 令牌。 如果不能使用官方的服务总线 SDK（例如，在 WinRT、Net Compact Framework、.Net Micro Framework 和 Mono 中）进行 C\# 开发，则这很有用。 当然，此库对于帮助了解基于声明的安全性如何在 AMQP 级别工作非常有用，就如同可以了解它如何在 HTTP 级别工作一样（使用 HTTP POST 请求并在“Authorization”标头内部发送 SAS 令牌）。 如果不需要此类有关 AMQP 的深入知识，可以将官方的服务总线 SDK 用于 .Net Framework 应用程序，该 SDK 会执行此操作。

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

`PutCbsToken()` 方法接收代表服务的 TCP 连接的 connection（[AMQP .NET Lite 库](https://github.com/Azure/amqpnetlite)提供的 AMQP Connection 类实例），以及表示要发送的 SAS 令牌的 sasToken 参数。 

> [!NOTE]
> 请务必在 **SASL 身份验证机制设置为 ANONYMOUS** 的情况下创建连接（而不是在不需要发送 SAS 令牌时使用的包含用户名与密码的默认 PLAIN）。
> 
> 

接下来，发布者将创建两个 AMQP 链接来发送 SAS 令牌和接收来自服务的回复（此令牌验证结果）。

AMQP 消息包含一组属性，比简单消息包含更多信息。 SAS 令牌是消息的正文（使用其构造函数）。 “ReplyTo”属性设置为用于在接收方链接上接收验证结果的节点名称（可以根据需要更改其名称，该节点将由服务动态创建）。 服务使用最后三个应用程序/自定义属性来指示它需要执行哪种类型的操作。 如 CBS 草案规范中所述，这些属性必须是**操作名称** ("put-token")、**令牌类型**（在本例中为 `servicebus.windows.net:sastoken`），以及要应用令牌的**受众的“名称”**（整个实体）。

在发送方链接上发送 SAS 令牌后，发布者必须在接收方链接上读取回复。 回复是一个简单的 AMQP 消息，其中包含一个名为 **"status-code"** 的应用程序属性，该属性可以包含与 HTTP 状态代码相同的值。

## <a name="rights-required-for-service-bus-operations"></a>服务总线操作所需的权限

下表显示对服务总线资源进行各种操作所需的访问权限。

| Operation | 所需声明 | 声明范围 |
| --- | --- | --- |
| **命名空间** | | |
| 在命名空间上配置授权规则 |管理 |任何命名空间地址 |
| **服务注册表** | | |
| 枚举私有策略 |管理 |任何命名空间地址 |
| 开始在命名空间上侦听 |侦听 |任何命名空间地址 |
| 将消息发送到命名空间中的侦听器 |发送 |任何命名空间地址 |
| **队列** | | |
| 创建队列 |管理 |任何命名空间地址 |
| 删除队列 |管理 |任何有效队列地址 |
| 枚举队列 |管理 |/$Resources/Queues |
| 获取队列说明 |管理 |任何有效队列地址 |
| 在队列上配置授权规则 |管理 |任何有效队列地址 |
| 发送到队列 |发送 |任何有效队列地址 |
| 从队列接收消息 |侦听 |任何有效队列地址 |
| 在查看锁定模式下接收消息后放弃或完成消息 |侦听 |任何有效队列地址 |
| 推迟消息以供将来检索 |侦听 |任何有效队列地址 |
| 将消息放入死信队列 |侦听 |任何有效队列地址 |
| 获取与消息队列会话关联的状态 |侦听 |任何有效队列地址 |
| 设置与消息队列会话关联的状态 |侦听 |任何有效队列地址 |
| **主题** | | |
| 创建主题 |管理 |任何命名空间地址 |
| 删除主题 |管理 |任何有效主题地址 |
| 枚举主题 |管理 |/$Resources/Topics |
| 获取主题说明 |管理 |任何有效主题地址 |
| 在主题上配置授权规则 |管理 |任何有效主题地址 |
| 发送到主题 |发送 |任何有效主题地址 |
| **订阅** | | |
| 创建订阅 |管理 |任何命名空间地址 |
| 删除订阅 |管理 |../myTopic/Subscriptions/mySubscription |
| 枚举订阅 |管理 |../myTopic/Subscriptions |
| 获取订阅说明 |管理 |../myTopic/Subscriptions/mySubscription |
| 在查看锁定模式下接收消息后放弃或完成消息 |侦听 |../myTopic/Subscriptions/mySubscription |
| 推迟消息以供将来检索 |侦听 |../myTopic/Subscriptions/mySubscription |
| 将消息放入死信队列 |侦听 |../myTopic/Subscriptions/mySubscription |
| 获取与主题会话关联的状态 |侦听 |../myTopic/Subscriptions/mySubscription |
| 设置与主题会话关联的状态 |侦听 |../myTopic/Subscriptions/mySubscription |
| **规则** | | |
| 创建规则 |管理 |../myTopic/Subscriptions/mySubscription |
| 删除规则 |管理 |../myTopic/Subscriptions/mySubscription |
| 枚举规则 |管理或侦听 |../myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题。

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [如何使用 Service Bus 队列](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com