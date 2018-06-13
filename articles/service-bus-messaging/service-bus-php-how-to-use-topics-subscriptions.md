---
title: 如何通过 PHP 使用服务总线主题 | Microsoft 文档
description: 了解如何通过 PHP 使用 Azure 中的服务总线主题。
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: 4763b172375668213372e6f4f8fc87431c430e53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23044582"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>如何通过 PHP 使用服务总线主题和订阅

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文说明如何使用服务总线主题和订阅。 示例采用 PHP 编写，且使用了 [Azure SDK for PHP](../php-download-sdk.md)。 涉及的应用场景包括**创建主题和订阅**、**创建订阅筛选器**、**将消息发送到主题**、**从订阅接收消息**以及**删除主题和订阅**。

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>创建 PHP 应用程序
创建访问 Azure Blob 服务的 PHP 应用程序的唯一要求是从代码中引用 [Azure SDK for PHP](../php-download-sdk.md) 中的类。 可以使用任何开发工具或记事本创建应用程序。

> [!NOTE]
> PHP 安装还必须已安装并启用 [OpenSSL 扩展](http://php.net/openssl)。
> 
> 

本文说明如何使用服务功能，这些功能可在 PHP 应用程序中本地调用，或通过在 Azure 的 Web 角色、辅助角色或网站中运行的代码调用。

## <a name="get-the-azure-client-libraries"></a>获取 Azure 客户端库
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用 Service Bus
使用服务总线 API：

1. 使用 [require_once][require-once] 语句引用 autoloader 文件。
2. 引用可使用的所有类。

以下示例演示如何包含 autoloader 文件并引用 **ServiceBusService** 类。

> [!NOTE]
> 本示例（以及本文中的其他示例）假定已通过 Composer 安装用于 Azure 的 PHP 客户端库。 如果已手动安装这些库或将其作为 PEAR 包安装，则必须引用 **WindowsAzure.php** autoloader 文件。
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

在下面的示例中，`require_once` 语句始终显示，但只引用了执行示例所必需的类。

## <a name="set-up-a-service-bus-connection"></a>设置服务总线连接
若要实例化服务总线客户端，必须先设置采用以下格式的有效连接字符串：

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

其中，`Endpoint` 的格式通常为 `https://[yourNamespace].servicebus.windows.net`。

若要创建任何 Azure 服务客户端，必须使用 `ServicesBuilder` 类。 可以：

* 将连接字符串直接传递给它。
* 使用 **CloudConfigurationManager (CCM)** 检查多个外部源以获取连接字符串：
  * 默认情况下，它附带了对一个外部源的支持 - 环境变量。
  * 可通过扩展 `ConnectionStringSource` 类来添加新源。

在此处列出的示例中，将直接传递连接字符串。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>创建主题
可通过 `ServiceBusRestProxy` 类管理服务总线主题。 `ServiceBusRestProxy` 对象是通过 `ServicesBuilder::createServiceBusService` 工厂方法与一个适当的连接字符串（该字符串封装了令牌权限以进行管理）构造的。

下列示例演示了如何实例化 `ServiceBusRestProxy` 并调用 `ServiceBusRestProxy->createTopic` 以在 `MySBNamespace` 命名空间中创建名为 `mytopic` 的主题：

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> 可使用 `ServiceBusRestProxy` 对象上的 `listTopics` 方法检查服务命名空间中是否已经存在一个具有指定名称的主题。
> 
> 

## <a name="create-a-subscription"></a>创建订阅
主题订阅也是使用 `ServiceBusRestProxy->createSubscription` 方法创建的。 订阅已命名，并且具有一个限制传递到订阅的虚拟队列的消息集的可选筛选器。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>创建具有默认 (MatchAll) 筛选器的订阅
**MatchAll** 筛选器是默认筛选器，在创建新订阅时未指定筛选器的情况下使用。 使用 **MatchAll** 筛选器时，发布到主题的所有消息都将置于订阅的虚拟队列中。 下面的示例将创建名为“mysubscription”的订阅，并使用默认的 **MatchAll** 筛选器。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>创建具有筛选器的订阅
还可以设置筛选器，以指定发送到主题的哪些消息应该在特定主题订阅中显示。 订阅支持的最灵活的一种筛选器是 [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter)，它实现了一部分 SQL92 功能。 SQL 筛选器将对发布到主题的消息的属性进行操作。 有关 SqlFilters 的详细信息，请参阅 [SqlFilter.SqlExpression 属性][sqlfilter]。

> [!NOTE]
> 有关订阅的每个规则单独处理传入消息，并将其结果消息添加到订阅。 此外，每个新订阅的筛选器具有一个默认**规则**对象，该对象包含一个将主题中的所有消息添加到订阅的筛选器。 要仅接收与筛选器相符的消息，必须删除默认规则。 可以使用 `ServiceBusRestProxy->deleteRule` 方法删除默认规则。
> 
> 

以下示例创建了一个名为 `HighMessages` 的订阅，该订阅带有只选择自定义 `MessageNumber` 属性大于 3 的消息的 **SqlFilter**。 请参阅[将消息发送到主题](#send-messages-to-a-topic)，了解有关将自定义属性添加到消息的信息。

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

请注意，此代码需要使用另一个命名空间：`WindowsAzure\ServiceBus\Models\SubscriptionInfo`。

同样，以下示例创建一个名为 `LowMessages` 的订阅，其 `SqlFilter` 只选择 `MessageNumber` 属性小于或等于 3 的消息。

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

现在，消息发送到 `mytopic` 主题后总是会传送给订阅了 `mysubscription` 订阅的接收者，并且会选择性地传送给订阅了 `HighMessages` 和 `LowMessages` 订阅的接收者（具体取决于消息内容）。

## <a name="send-messages-to-a-topic"></a>将消息发送到主题
要将消息发送到服务总线主题，应用程序应调用 `ServiceBusRestProxy->sendTopicMessage` 方法。 下面的代码演示了如何将消息发送到先前在 `MySBNamespace` 服务命名空间创建的 `mytopic` 主题。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

发送到服务总线主题的消息是 [BrokeredMessage][BrokeredMessage] 类的实例。 [BrokeredMessage][BrokeredMessage] 对象包含一组标准属性和方法以及用来保存自定义应用程序特定属性的属性。 下面的示例演示如何将 5 条测试消息发送到先前创建的 `mytopic` 主题中。 `setProperty` 方法用于将自定义属性 (`MessageNumber`) 添加到每条消息。 请注意，每条消息的 `MessageNumber` 属性值会不同（可使用该值决定哪个订阅接收它，如[创建订阅](#create-a-subscription)部分所述）：

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

服务总线主题在[标准层](service-bus-premium-messaging.md)中支持的最大消息容量为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大为 64 KB，其中包括标准和自定义应用程序属性。 一个主题中包含的消息数量不受限制，但消息的总大小受限制。 主题大小的上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额][Service Bus quotas]。

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息
从订阅接收消息的最佳方法是使用 `ServiceBusRestProxy->receiveSubscriptionMessage` 方法。 可通过两种模式接收消息：[*ReceiveAndDelete* 和 *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)。 **PeekLock** 为默认设置。

当使用 [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) 模式时，接收是一个单步操作，也就是当服务总线接收订阅中的消息读取请求时，它会将消息标记为“正在使用”并将其返回给应用程序。 [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) * 模式是最简单的模式，最适合应用程序允许出现故障时不处理消息的方案。 为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为“将使用”，因此当应用程序重启并重新开始使用消息时，它会丢失在发生崩溃前使用的消息。

在默认的 [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) 模式下，接收消息会变成一个两阶段操作，使其可为不允许消息丢失的应用程序提供支持。 当 Service Bus 收到请求时，它会查找下一条要使用的消息，锁定该消息以防其他使用者接收，然后将该消息返回到应用程序。 应用程序完成消息处理（或可靠地存储消息以供日后处理）后，它会将收到的消息传送到 `ServiceBusRestProxy->deleteMessage`，从而完成接收过程的第二阶段。 发现 `deleteMessage` 调用时，服务总线会将消息标记为“已使用”，并将消息从队列中删除。

以下示例演示了如何使用 [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode) 模式（默认模式）接收和处理消息。 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何：处理应用程序崩溃和不可读消息
服务总线提供了相关功能，帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序出于某种原因无法处理消息，它可以对收到的消息调用 `unlockMessage` 方法（而不是 `deleteMessage` 方法）。 这会导致服务总线解锁队列中的消息并使其能够重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

队列中的锁定消息还有相关超时，如果应用程序无法在锁定超时期满前处理消息（例如，当应用程序发生故障时），服务总线会自动解除锁定消息，让它再次可供接收。

如果应用程序在处理消息后、但在发出 `deleteMessage` 请求前发生故障，消息会重新传递给重启的应用程序。 此情况通常称作*至少处理一次*，即每条消息将至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案无法容忍重复处理，则应用程序开发人员应向其应用程序添加更多逻辑以处理重复消息传送。 这通常可通过使用消息的 `getMessageId` 方法来实现，该方法在多次传送尝试中保持不变。

## <a name="delete-topics-and-subscriptions"></a>删除主题和订阅
若要删除某个主题或订阅，请分别使用 `ServiceBusRestProxy->deleteTopic` 或 `ServiceBusRestProxy->deleteSubscripton` 方法。 请注意，删除某个主题也会删除向该主题注册的所有订阅。

下面的示例演示了如何删除主题 (`mytopic`) 及其注册的订阅。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {        
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

通过使用 `deleteSubscription` 方法可单独删除订阅：

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>后续步骤
了解服务总线队列的基础知识后，请参阅[队列、主题和订阅][Queues, topics, and subscriptions]以获取更多信息。

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter
[require-once]: http://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
