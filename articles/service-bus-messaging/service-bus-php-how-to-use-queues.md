---
title: "如何通过 PHP 使用服务总线队列 | Microsoft 文档"
description: "了解如何在 Azure 中使用服务总线队列。 采用 PHP 编写的代码示例。"
services: service-bus-messaging
documentationcenter: php
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 3514812f7f087582035dad5d9a4d620652aa4da9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-use-service-bus-queues-with-php"></a>如何通过 PHP 使用服务总线队列
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本指南说明如何使用服务总线队列。 示例采用 PHP 编写，且使用了 [Azure SDK for PHP](../php-download-sdk.md)。 涉及的任务包括**创建队列**、**发送和接收消息**以及**删除队列**。

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>创建 PHP 应用程序
创建访问 Azure Blob 服务的 PHP 应用程序的唯一要求是从代码中引用[ Azure SDK for PHP](../php-download-sdk.md) 中的类。 可以使用任何开发工具或记事本创建应用程序。

> [!NOTE]
> PHP 安装还必须已安装并启用 [OpenSSL 扩展](http://php.net/openssl)。
> 
> 

本指南会使用服务功能，这些功能可在 PHP 应用程序中本地调用，或通过在 Azure 的 Web 角色、辅助角色或网站中运行的代码调用。

## <a name="get-the-azure-client-libraries"></a>获取 Azure 客户端库
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用应用程序
若要使用服务总线队列 API，请执行以下操作：

1. 使用 [require_once][require_once] 语句引用 autoloader 文件。
2. 引用可使用的所有类。

下列示例演示了如何包括 autoloader 文件并引用 `ServicesBuilder` 类。

> [!NOTE]
> 本示例（以及本文中的其他示例）假定已通过 Composer 安装用于 Azure 的 PHP 客户端库。 如果已手动安装这些库或将其作为 PEAR 包安装，则必须引用 **WindowsAzure.php** autoloader 文件。
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

在以下示例中，`require_once` 语句将始终显示，但只会引用执行该示例所需的类。

## <a name="set-up-a-service-bus-connection"></a>设置服务总线连接
若要实例化服务总线客户端，必须先设置采用以下格式的有效连接字符串：

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

其中，`Endpoint` 的格式通常为 `[yourNamespace].servicebus.windows.net`。

若要创建任何 Azure 服务客户端，必须使用 `ServicesBuilder` 类。 可以：

* 将连接字符串直接传递给它。
* 使用 **CloudConfigurationManager (CCM)** 检查多个外部源以获取连接字符串：
  * 默认情况下，它附带了对一个外部源的支持 - 环境变量
  * 可通过扩展 `ConnectionStringSource` 类来添加新源

在此处列出的示例中，将直接传递连接字符串。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>创建队列
可通过 `ServiceBusRestProxy` 类管理服务总线队列。 `ServiceBusRestProxy` 对象是通过 `ServicesBuilder::createServiceBusService` 工厂方法与一个适当的连接字符串（该字符串封装了令牌权限以进行管理）构造的。

下列示例演示了如何实例化 `ServiceBusRestProxy` 并调用 `ServiceBusRestProxy->createQueue` 以在 `MySBNamespace` 服务命名空间中创建名为 `myqueue` 的队列：

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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
> 可以对 `ServiceBusRestProxy` 对象使用 `listQueues` 方法，以检查具有指定名称的队列是否已位于命名空间中。
> 
> 

## <a name="send-messages-to-a-queue"></a>向队列发送消息
要将消息发送到服务总线队列，应用程序应调用 `ServiceBusRestProxy->sendQueueMessage` 方法。 下面的代码演示了如何将消息发送到先前在 `MySBNamespace` 服务命名空间内创建的 `myqueue` 队列。

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
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
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

发送到服务总线（以及从服务总线接收）的消息是 [BrokeredMessage][BrokeredMessage] 类的实例。 [BrokeredMessage][BrokeredMessage] 对象包含一组标准方法和属性，用于保存特定于自定义应用程序的属性，以及大量随机应用程序数据。

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大为 64 KB，其中包括标准和自定义应用程序属性。 一个队列可包含的消息数不受限制，但消息的总大小受限。 队列大小的上限为 5 GB。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息

从队列接收消息的最佳方法是使用 `ServiceBusRestProxy->receiveQueueMessage` 方法。 可以两种不同模式接收消息：[*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) 和 [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock)。 **PeekLock** 为默认设置。

使用 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) 模式时，接收是一项单步操作，即当服务总线接收到队列中某条消息的读取请求时，它会将该消息标记为“已使用”并将其返回给应用程序。 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode.receiveanddelete) 模式是最简单的模式，最适合应用程序允许出现故障时不处理消息的方案。 为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为“将使用”，因此当应用程序重启并重新开始使用消息时，它会丢失在发生崩溃前使用的消息。

在默认的 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) 模式下，接收消息会变成一个两阶段操作，使其可为不允许消息丢失的应用程序提供支持。 当服务总线收到请求时，它会找到要使用的下一个消息，将其锁定以防其他使用方接收它，然后将该消息返回给应用程序。 应用程序完成消息处理（或可靠地存储消息以供日后处理）后，它会将收到的消息传递到 `ServiceBusRestProxy->deleteMessage`，从而完成接收过程的第二阶段。 服务总线发现 `deleteMessage` 调用时，会将消息标记为“已使用”并将其从队列中删除。

以下示例演示了如何使用 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode.peeklock) 模式（默认模式）接收和处理消息。

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何处理应用程序崩溃和不可读消息

服务总线提供了相关功能，帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序出于某种原因无法处理消息，它可以对收到的消息调用 `unlockMessage` 方法（而不是 `deleteMessage` 方法）。 这会导致 Service Bus 解锁队列中的消息并使其能够重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

还存在与队列中已锁定的消息相关联的超时，并且如果应用程序未能在锁定超时到期之前处理消息（例如，如果应用程序崩溃），服务总线则将自动解锁该消息，使它可以再次被接收。

如果应用程序在处理消息之后（但在发出 `deleteMessage` 请求前）出现崩溃，则应用程序重启时会将该消息重新传送给它。 此情况通常称作*至少处理一次*，即每条消息将至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案不允许重复处理，则建议向应用程序添加其他逻辑来处理重复消息传送。 这通常可通过使用消息的 `getMessageId` 方法来实现，该方法在多次传送尝试中保持不变。

## <a name="next-steps"></a>后续步骤
了解服务总线队列的基础知识后，请参阅[队列、主题和订阅][Queues, topics, and subscriptions]以获取更多信息。

有关详细信息，请访问 [PHP 开发人员中心](https://azure.microsoft.com/develop/php/)。

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once


