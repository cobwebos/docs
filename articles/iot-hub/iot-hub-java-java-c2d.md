---
title: 使用 Azure IoT 中心发送云到设备消息 (Java) | Microsoft Docs
description: 如何使用 Azure IoT SDK for Java 将云到设备的消息从 Azure IoT 中心发送到设备。 修改模拟设备应用以接收云到设备消息，并修改后端应用以发送云到设备消息。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: f4e5880a39d6ad299fd6e7f29bd0e3aefadc3bcd
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446891"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>使用 IoT 中心发送云到设备的消息 (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 [将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-java.md)快速入门介绍了如何创建 IoT 中心、在其中预配设备标识，以及编写模拟设备应用来发送设备到云的消息。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程建立在[将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-java.md)的基础之上。 它展示了如何执行以下操作：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。

* 在设备上接收云到设备的消息。

* 通过解决方案后端，请求确认收到从 IoT 中心发送到设备的消息（反馈）。

可以在 [IoT 中心开发人员指南](iot-hub-devguide-messaging.md)中找到有关云到设备消息的详细信息。

在本教程的最后，将运行两个 Java 控制台应用：

* simulated-device（[将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-java.md)中创建的应用的修改版本），它连接到 IoT 中心并接收云到设备的消息。

* send-c2d-messages，它将云到设备消息通过 IoT 中心发送到模拟设备应用，然后接收 IoT 中心的送达确认。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 对许多设备平台和语言（包括 C、Java、Python 和 Javascript）提供 SDK 支持。 有关如何将设备连接到本教程中的代码（通常是连接到 Azure IoT 中心）的逐步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。

## <a name="prerequisites"></a>先决条件

* [将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-java.md)快速入门或[使用 IoT 中心配置消息路由](tutorial-routing.md)教程的完整工作版本。

* [Java SE 开发工具包 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。 请确保在“长期支持”下选择“Java 8”以获取 JDK 8 的下载。

* [Maven 3](https://maven.apache.org/download.cgi)

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="receive-messages-in-the-simulated-device-app"></a>在模拟设备应用中接收消息

在本部分中，修改在[将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-java.md)中创建的模拟设备应用，接收来自 IoT 中心的云到设备消息。

1. 使用文本编辑器打开 simulated-device\src\main\java\com\mycompany\app\App.java 文件。

2. 在 **App** 类中添加以下 **MessageCallback** 类作为嵌套类。 设备从 IoT 中心接收消息时，将调用 **execute** 方法。 在本示例中，设备始终通知 IoT 中心它已完成消息：

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. 修改 **main** 方法，以创建 **AppMessageCallback** 实例，并在打开客户端之前调用 **setMessageCallback** 方法，如下所示：

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. 若要使用 Maven 生成 **simulated-device** 应用，请在 simulated-device 文件夹中的命令提示符下执行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

`execute`类中的方法 `AppMessageCallback` 返回 `IotHubMessageResult.COMPLETE` 。 这会通知 IoT 中心消息已成功处理，并且可以安全地从设备队列中删除该消息。 当设备的处理成功完成，而不考虑它使用的协议时，设备应返回该值。

通过 AMQP 和 HTTPS，但不 MQTT，设备还可以：

* 放弃消息，这会导致 IoT 中心将消息保留在设备队列中以供将来使用。
* 拒绝消息，这会永久地从设备队列中删除消息。

如果发生了阻止设备完成、放弃或拒绝该消息的情况，IoT 中心会在固定的超时期限过后，将该消息列队以便重新传递。 出于此原因，设备应用中的消息处理逻辑必须是 *幂*等的，以便多次接收同一消息会生成相同的结果。

有关 IoT 中心如何处理云到设备的消息的详细信息，包括云到设备消息生命周期的详细信息，请参阅 [从 IoT 中心发送云到设备的消息](iot-hub-devguide-messages-c2d.md)。

> [!NOTE]
> 如果使用 HTTPS （而不是 MQTT 或 AMQP）作为传输，则 **DeviceClient** 实例将不经常每隔25分钟) 检查来自 IoT 中心的消息 (。 有关 MQTT、AMQP 和 HTTPS 支持之间的差异的详细信息，请参阅 [云到设备通信指南](iot-hub-devguide-c2d-guidance.md) 和 [选择通信协议](iot-hub-devguide-protocols.md)。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你会创建一个后端服务，以通过在[将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-java.md)中创建的 IoT 中心，发送云到设备消息。 若要发送云到设备消息，服务需要服务连接权限。 默认情况下，每个 IoT 中心都使用名为“服务”的共享访问策略创建，该策略会授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息

在本部分中，会创建 Java 控制台应用，用于向模拟设备应用发送“云到设备”消息。 需要在[将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-java.md)快速入门中添加的设备的设备 ID。 还需要以前在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。

1. 在命令提示符处使用以下命令，创建名为 **send-c2d-messages** 的 Maven 项目。 请注意，此命令是一条很长的命令：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示符下，导航到新的 send-c2d-messages 文件夹。

3. 使用文本编辑器，打开 send-c2d-messages 文件夹中的 pom.xml 文件，并在 **dependencies** 节点中添加以下依赖项。 这样即可使用应用程序中的 **iothub-java-service-client** 包来与 IoT 中心服务通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-service-client**。

4. 保存并关闭 pom.xml 文件。

5. 使用文本编辑器打开 send-c2d-messages\src\main\java\com\mycompany\app\App.java 文件。

6. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 将以下类级变量添加到 App 类，并将 {yourhubconnectionstring} 和 {yourdeviceid} 替换为前面记下的值  ：

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. 将 **main**方法替换为以下代码。 此代码用于连接到 IoT 中心，将消息发送到设备，然后等待设备已接收并处理消息的通知：

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按文章 [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。

9. 若要使用 Maven 生成 **simulated-device** 应用，请在 simulated-device 文件夹中的命令提示符下执行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 在 simulated-device 文件夹的命令提示符处，运行以下命令以发送遥测数据至 IoT 中心并侦听中心发出的云到设备消息：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![运行模拟设备应用](./media/iot-hub-java-java-c2d/receivec2d.png)

2. 在 send-c2d-messages 文件夹的命令提示符处，运行以下命令以发送云到设备的消息并等待反馈确认：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![运行命令以发送“云到设备”消息](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何发送和接收云到设备的消息。

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 解决方案加速器](https://azure.microsoft.com/documentation/suites/iot-suite/)。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南](iot-hub-devguide.md)。
