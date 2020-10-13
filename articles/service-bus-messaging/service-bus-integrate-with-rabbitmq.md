---
title: 如何将 RabbitMQ 与 Azure 服务总线集成
description: 有关如何将 RabbitMQ 与 Azure 服务总线集成的分步指南
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91301064"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>如何将 RabbitMQ 与 Azure 服务总线集成

在本指南中，我们将学习如何将消息从 RabbitMQ 发送到 Azure 服务总线。

下面是一些可以利用这些功能的方案：

- **边缘设置**：我们有一种边缘设置，可以在其中将消息发送到 RabbitMQ，但我们希望将这些消息转发到 [Azure 服务总线](./service-bus-messaging-overview.md)进行进一步的处理，以便使用许多 [Azure 大数据功能](/azure/architecture/guide/architecture-styles/big-data)。
- **混合云**：你的公司刚收购了一家使用 RabbitMQ 来满足其消息传递需求的第三方公司。 它们在不同的云上。 当它们过渡到 Azure 时，你就已经可以通过使用 Azure 服务总线桥接 RabbitMQ 来开始共享数据。
- **第三方集成**：第三方使用 RabbitMQ 作为中转站，并想要将其数据发送给我们，但它们在我们的组织外部。 我们可以向其提供 SAS 密钥，使其能够访问用来转发消息的一组有限的 Azure 服务总线队列。

列表还在继续扩充，但是我们可以通过将 RabbitMQ 桥接到 Azure 来解决大多数此类用例的问题。

首先，你需要通过[在此处](https://azure.microsoft.com/free/)注册来创建一个免费的 Azure 帐户

登录到你的帐户后，请转到 [Azure 门户](https://portal.azure.com/)并创建一个新的 Azure 服务总线[命名空间](./service-bus-create-namespace-portal.md)。 命名空间是消息传递组件（例如队列和主题）所在的作用域容器。

## <a name="adding-a-new-azure-service-bus-namespace"></a>添加新的 Azure 服务总线命名空间

在 Azure 门户中，单击大加号按钮来添加新资源

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="创建资源":::

然后选择“集成”并单击“Azure 服务总线”来创建消息命名空间：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="创建资源":::

系统会提示你输入命名空间信息。 选择要使用的 Azure 订阅。 如果你没有[资源组](../azure-resource-manager/management/manage-resource-groups-portal.md)，可新建一个。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="创建资源":::

对于“`Namespace name`”，请使用 `rabbitmq`，但它可以是你想要的任何名称。 然后，设置“`East US`”作为位置。 选择“`Basic`”作为定价层。

如果一切正常，你应该会看到以下确认屏幕：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="创建资源":::

然后返回到 Azure 门户，此时会看到其中列出了新的 `rabbitmq` 命名空间。 单击该命名空间以访问资源，以便向其添加队列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="创建资源":::

## <a name="creating-our-azure-service-bus-queue"></a>创建我们的 Azure 服务总线队列

现在，你已有 Azure 服务总线命名空间，请单击左侧“`Entities`”下的“`Queues`”按钮，以便添加新队列：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="创建资源":::

队列的名称将为 `from-rabbitmq`，只是为了提醒消息来自何处。 你可以将所有其他选项保留为默认值，但也可以更改它们来满足应用的需求。

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>启用 RabbitMQ Shovel 插件

为了将消息从 RabbitMQ 发送到 Azure 服务总线，我们打算使用随 RabbitMQ 打包的 [Shovel 插件](https://www.rabbitmq.com/shovel.html)。 可以使用以下命令启用此插件及其可视化界面：

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>你可能需要以 root 身份运行该命令。

现在可以获取将 RabbitMQ 连接到 Azure 所需的凭据。

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>将 RabbitMQ 连接到 Azure 服务总线

你需要为队列创建[共享访问策略](../storage/common/storage-sas-overview.md) (SAS)，以便 RabbitMQ 将消息发布到队列中。 使用 SAS 策略，你可以指定允许哪个外部方对你的资源执行操作。 这里的构思是允许 RabbitMQ 发送消息，但不侦听或管理队列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="创建资源":::

勾选“`Send`”框，然后单击“`Create`”以创建我们的 SAS 策略。

创建策略后，单击该策略以查看**主连接字符串**。 我们将使用它来让 RabbitMQ 与 Azure 服务总线通信：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="创建资源":::

在使用该连接字符串之前，需要将其转换为 RabbitMQ 的 AMQP 连接格式。 为此，请转到[连接字符串转换器工具](https://red-mushroom-0f7446a0f.azurestaticapps.net/)并将连接字符串粘贴到窗体中，然后单击“转换”。 你将获得一个可用于 RabbitMQ 的连接字符串。 （该网站在浏览器中以本地方式运行所有内容，因此数据不会通过网络发送）。 可以在 [GitHub](https://github.com/videlalvaro/connstring_to_amqp) 上访问其源代码。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="创建资源":::

现在，在浏览器中打开 RabbitMQ 管理插件 `http://localhost:15672/#/dynamic-shovels` 并转到 `Admin -> Shovel Management`，你可以在其中添加新的 shovel，以便将消息从 RabbitMQ 队列发送到 Azure 服务总线队列。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="创建资源":::

在此处调用你的 Shovel `azure` 并选择 `AMQP 0.9.1` 作为源协议。 在屏幕截图中，我们有 `amqp://`，这是将我们连接到本地 RabbitMQ 服务器的默认 URI。 请确保使它适应你的当前部署。

对于“队列”，可以使用 `azure` 作为队列的名称。 如果该队列不存在，RabbitMQ 会为你创建它。 你还可以选择已存在的队列的名称。 可以让其他选项保留默认值。

然后，对于“`destination`”，选择 `AMQP 1.0` 作为协议。 在 `URI` 字段中，输入你在上一步获得的连接字符串，而在上一步中，你将 Azure 连接字符串转换成了 RabbitMQ 格式。 它应如下所示：

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

在“`Address`”字段中，我们将输入你的 **Azure 服务总线队列**的名称。在本例中，它名为 `from-rabbitmq`。 单击“`Add Shovel`”，你的设置项就可以开始接收消息。

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>将消息从 RabbitMQ 发布到 Azure 服务总线

在 RabbitMQ 管理界面中，我们可以转到“`Queues`”，选择 `azure` 队列，然后搜索“`Publish message`”面板。 其中会显示一个窗体，可让你直接将消息发布到队列。 对于我们的示例，我们只是将 `fist message` 添加为“`Payload`”并点击“`Publish Message`”：

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="创建资源" 按钮。 如果一切顺利，你会看到队列现在包含一条消息。 好极了，恭喜你！

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="创建资源":::

但请确保消息是你从 RabbitMQ 发送的消息。 选择“`Peek`”选项卡，然后单击“`Peek`”按钮检索队列中的最后消息。 单击该消息以检查其内容。 你应该会看到如下图所示的内容，其中列出了你的 `first message`。

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="创建资源":::

## <a name="lets-recap"></a>让我们回顾一下

恭喜！ 你已取得了很多成果！ 你已设法将消息从 RabbitMQ 传送到 Azure 服务总线。让我们回顾一下相关步骤：

1. 创建 Azure 服务总线命名空间
2. 向命名空间添加队列
3. 向队列添加 SAS 策略
4. 获取队列连接字符串
5. 启用 RabbitMQ shovel 插件和管理界面
6. 将 Azure 服务总线连接字符串转换为 RabbitMQ 的 AMQP 格式
7. 将一个新的 Shovel 添加到 RabbitMQ 并将其连接到 Azure 服务总线
8. 发布消息

通过前面的步骤，你集成了组织在 Azure 外部的区域。 Shovel 插件允许将消息从 RabbitMQ 发送到 Azure 服务总线。 这具有巨大的优势，因为你现在可以允许受信任的第三方通过你的 Azure 部署来连接其应用。

归根到底，消息传递就是启用连接，而我们刚刚使用此技术打开了一个新连接。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 服务总线](./service-bus-messaging-overview.md)
- 详细了解 [服务总线中的 AMQP 1.0 支持](./service-bus-amqp-overview.md)
