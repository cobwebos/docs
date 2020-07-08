---
title: 如何将 RabbitMQ 与 Azure 服务总线集成
description: 如何将 RabbitMQ 与 Azure 服务总线集成的循序渐进指南
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: df0541802dfc331ffc94e95be112ea7e005960b5
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049756"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>如何将 RabbitMQ 与 Azure 服务总线集成

在本指南中，我们将学习如何将消息从 RabbitMQ 发送到 Azure 服务总线。

下面是一些可以利用这些功能的方案：

- **边缘设置**：我们有一种边缘设置，我们要将消息发送到 RabbitMQ，但我们希望将这些消息转发到[Azure 服务总线](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview)，以便进行进一步处理，因此，我们可以使用许多[Azure 大数据功能](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/big-data)。
- **混合云**：你的公司刚刚获取了使用 RabbitMQ 的第三方，以满足其消息传送需求。 它们在不同的云上。 转换到 Azure 时，你可以通过将 RabbitMQ 与 Azure 服务总线进行桥接来开始共享数据。
- **第**三方集成：第三方使用 RabbitMQ 作为 broker，并想要将其数据发送给我们的组织之外。 我们可以向他们提供 SAS 密钥，使其能够访问有限的一组 Azure 服务总线队列，用户可以在这些队列中将消息转发到这些队列。

此列表已打开，但我们可以通过将 RabbitMQ 桥接到 Azure 来解决其中的大多数用例。

首先，你需要通过[在此处](https://azure.microsoft.com/free/)注册来创建一个免费的 Azure 帐户

登录到你的帐户后，请使用[Azure 门户](https://portal.azure.com/)，并创建新的 Azure 服务总线[命名空间](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)。 命名空间是消息传递组件所在的作用域容器，如队列和主题。

## <a name="adding-a-new-azure-service-bus-namespace"></a>添加新的 Azure 服务总线命名空间

在 Azure 门户中，单击大加号按钮添加新资源

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="创建资源":::

然后选择 "集成"，并单击 "Azure 服务总线" 创建消息命名空间：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="选择 Azure 服务总线":::

系统将提示你输入命名空间信息。 选择要使用的 Azure 订阅。 如果没有资源组，则可以创建一个新的[资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="创建命名空间":::

`rabbitmq`适用于 `Namespace name` ，但它可以是你想要的任何内容。 然后 `East US` 为该位置设置。 选择 `Basic` 作为价格层。

如果一切正常，应会看到以下确认屏幕：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="创建命名空间确认":::

然后，返回到 Azure 门户你会看到其中列出了新的 `rabbitmq` 命名空间。 单击该资源以访问资源，以便可以向其添加队列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="具有新命名空间的资源列表":::

## <a name="creating-our-azure-service-bus-queue"></a>创建 Azure 服务总线队列

现在，你已有 Azure 服务总线命名空间，单击 `Queues` 左侧的按钮，在下， `Entities` 你可以添加新队列：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="创建队列":::

队列的名称将 `from-rabbitmq` 只是提醒消息来自何处。 你可以将所有其他选项保留为默认值，但你可以更改它们以满足你的应用程序的需求。

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>启用 RabbitMQ Shovel 插件

若要将消息从 RabbitMQ 发送到 Azure 服务总线，我们将使用与 RabbitMQ 一起打包的[Shovel 插件](https://www.rabbitmq.com/shovel.html)。 可以使用以下命令启用插件及其可视界面：

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>可能需要以根身份运行该命令。

现在可以获取将 RabbitMQ 连接到 Azure 所需的凭据。

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>将 RabbitMQ 连接到 Azure 服务总线

需要为队列创建[共享访问策略](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)（SAS），以便 RabbitMQ 可以将消息发布到其中。 SAS 策略使你可以指定允许哪个外部方对资源执行操作。 其思路是，RabbitMQ 能够发送消息，但不能侦听或管理队列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="添加 SAS 策略":::

勾选 `Send` 复选框，然后单击 `Create` 以设置 SAS 策略。

创建策略后，单击该策略以查看**主连接字符串**。 我们将使用它来让 RabbitMQ 与 Azure 服务总线通信：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="获取 SAS 策略":::

在可以使用该连接字符串之前，需要将其转换为 RabbitMQ 的 AMQP 连接格式。 接下来，请参阅[连接字符串转换器工具](https://red-mushroom-0f7446a0f.azurestaticapps.net/)并将连接字符串粘贴到窗体中，单击 "转换"。 你将获得一个 RabbitMQ 的连接字符串。 （该网站在浏览器中运行所有内容，因此不会通过网络发送数据）。 你可以访问[GitHub](https://github.com/videlalvaro/connstring_to_amqp)上的源代码。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="转换连接字符串":::

现在，在浏览器中打开 RabbitMQ 管理插件 [http://localhost:15672/#/dynamic-shovels](http://localhost:15672/#/dynamic-shovels) ，然后转到 `Admin -> Shovel Management` ，可在其中添加新的 shovel，以便将消息从 RabbitMQ 队列发送到 Azure 服务总线队列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="添加 RabbitMQ Shovel":::

此处调用 Shovel `azure` ，并选择 `AMQP 0.9.1` 作为源协议。 在屏幕截图中，我们有 `amqp://` ，这是将我们连接到本地 RabbitMQ 服务器的默认 URI。 请确保适应当前的部署。

在项的队列一方，可以使用 `azure` 作为队列的名称。 如果该队列不存在，RabbitMQ 将为你创建它。 你还可以选择已存在的队列的名称。 可以将其他选项保留为默认值。

然后，在 `destination` 项目的一侧选择 `AMQP 1.0` 作为协议。 在 "类型" `URI` 字段中，输入你在上一步中获得的连接字符串，你是否已将 Azure 连接字符串转换为 RabbitMQ 格式。 应如下所示：

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

在字段中， `Address` 我们将输入**Azure 服务总线队列**的名称，在此示例中，它是名为的 `from-rabbitmq` 。 单击 `Add Shovel` "设置"，您的设置应该已准备好开始接收消息。

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>将消息从 RabbitMQ 发布到 Azure 服务总线

在 RabbitMQ 管理界面中 `Queues` ，可以选择 "队列"， `azure` 然后搜索 `Publish message` 面板。 将显示一个窗体，可让你直接将消息发布到队列。 对于我们的示例，我们只是将添加 `fist message` 为 `Payload` 并按 `Publish Message` ：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="发布第一条消息":::

返回到 Azure 并检查队列。 `Service Bus Explorer`在左面板中单击。 如果一切顺利，您将看到您的队列现在包含一条消息。 Yay、恭喜！

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure 服务总线队列":::

但请确保该消息是从 RabbitMQ 发送的消息。 选择该 `Peek` 选项卡，然后单击 "" `Peek` 按钮以检索队列中的最后一条消息。 单击该消息以检查其内容。 你应看到如下图所示的内容 `first message` 。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="队列速览":::

## <a name="lets-recap"></a>让我们回顾一下

恭喜！ 您已经实现了很多事情！ 为了使消息从 RabbitMQ 转到 Azure 服务总线，我们来总结一下这些步骤：

1. 创建 Azure 服务总线命名空间
2. 向命名空间添加队列
3. 向队列添加 SAS 策略
4. 获取队列连接字符串
5. 在管理界面 & 启用 RabbitMQ shovel 插件
6. 将 Azure 服务总线连接字符串转换为 RabbitMQ 的 AMQP 格式
7. 将新的 Shovel 添加到 RabbitMQ & 将其连接到 Azure 服务总线
8. 发布消息

按照前面的步骤，你的组织的集成区域在 Azure 之外。 Shovel 插件允许将消息从 RabbitMQ 发送到 Azure 服务总线。 这种方式具有巨大的优势，因为现在可以允许受信任的第三方通过 Azure 部署来连接其应用。

最后，消息传送是关于启用连接的，使用这种方法，我们刚刚打开了一个新的。