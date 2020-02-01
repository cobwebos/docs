---
title: 如何通过 PHP 使用 Azure 服务总线队列
description: 本教程介绍如何创建 PHP 应用程序，用于向/从服务总线队列发送/接收消息。
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760685"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>快速入门：如何通过 PHP 使用服务总线队列
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本教程介绍如何创建 PHP 应用程序，用于向/从服务总线队列发送/接收消息。 

## <a name="prerequisites"></a>必备条件
1. Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
2. 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。
    1. 阅读服务总线**队列**的快速**概述**。 
    2. 创建服务总线**命名空间**。 
    3. 获取**连接字符串**。 

        > [!NOTE]
        > 在本教程中，你将使用 PHP 在服务总线命名空间中创建一个**队列**。 
3. [用于 PHP 的 Azure SDK](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>创建 PHP 应用程序
创建访问 Azure Blob 服务的 PHP 应用程序的唯一要求是从代码中引用[ Azure SDK for PHP](https://github.com/Azure/azure-sdk-for-php) 中的类。 可以使用任何开发工具或记事本创建应用程序。

> [!NOTE]
> PHP 安装还必须已安装并启用 [OpenSSL 扩展](https://php.net/openssl)。

在本指南中，你将使用服务功能，这些功能可在 PHP 应用程序中本地调用，或通过在 Azure 的 Web 角色、辅助角色或网站中运行的代码调用。

## <a name="get-the-azure-client-libraries"></a>获取 Azure 客户端库
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>配置应用程序以使用服务总线
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
要将消息发送到服务总线队列，应用程序应调用 `ServiceBusRestProxy->sendQueueMessage` 方法。 下面的代码演示了如何将消息发送到在 `MySBNamespace` 服务命名空间先前创建的 `myqueue` 队列。

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

在服务总线队列中发送和接收的消息是 [BrokeredMessage][BrokeredMessage] 类的实例。 [BrokeredMessage][BrokeredMessage] 对象包含一组标准方法和属性，用于保存特定于自定义应用程序的属性，以及大量随机应用程序数据。

服务总线队列在[标准层](service-bus-premium-messaging.md)中支持的最大消息大小为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大大小为 64 KB，其中包括标准和自定义应用程序属性。 一个队列可包含的消息数不受限制，但消息的总大小受限。 队列大小的上限为 5 GB。

## <a name="receive-messages-from-a-queue"></a>从队列接收消息

从队列接收消息的最佳方法是使用 `ServiceBusRestProxy->receiveQueueMessage` 方法。 可在两种不同的模式下接收消息：[*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) 和 [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock)。 **PeekLock** 为默认设置。

使用 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 模式时，接收是一项单次操作，即当服务总线接收到队列中某条消息的读取请求时，它会将该消息标记为“已使用”并将其返回给应用程序。 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 模式是最简单的模式，最适合应用程序允许出现故障时不处理消息的方案。 为了理解这一点，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为“将使用”，因此当应用程序重启并重新开始使用消息时，它会丢失在发生崩溃前使用的消息。

在默认的 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) 模式下，接收消息会变成一个两阶段操作，使其可为不允许消息丢失的应用程序提供支持。 当服务总线收到请求时，它会找到要使用的下一个消息，将其锁定以防其他使用方接收它，然后将该消息返回给应用程序。 应用程序完成消息处理（或可靠地存储消息以供日后处理）后，它会将收到的消息传送到 `ServiceBusRestProxy->deleteMessage`，从而完成接收过程的第二阶段。 服务总线发现 `deleteMessage` 调用时，会将消息标记为“已使用”并将其从队列中删除。

以下示例演示了如何使用 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) 模式（默认模式）接收和处理消息。

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

还存在与队列中已锁定消息关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），服务总线会自动解锁该消息并使它可再次被接收。

如果应用程序在处理消息之后（但在发出 `deleteMessage` 请求前）出现崩溃，则应用程序重启时会将该消息重新传送给它。 此情况通常称作*至少处理一次*，即每条消息将至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案不允许重复处理，则建议向应用程序添加其他逻辑来处理重复消息传送。 这通常可通过使用消息的 `getMessageId` 方法来实现，该方法在多次传送尝试中保持不变。

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤
现在，已了解服务总线队列的基础知识，请参阅[队列、主题和订阅][Queues, topics, and subscriptions]以获取更多信息。

有关详细信息，请访问 [PHP 开发人员中心](https://azure.microsoft.com/develop/php/)。

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


