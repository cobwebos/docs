---
title: 使用 Azure IoT 中心发送云到设备消息 (Java) | Microsoft Docs
description: 如何使用 Azure IoT SDK for Java 将云到设备的消息从 Azure IoT 中心发送到设备。 修改模拟设备应用以接收云到设备消息，并修改后端应用以发送云到设备消息。
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.openlocfilehash: 83fe0e2e39d414120bb09ad58d17b5081caef745
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>使用 IoT 中心发送云到设备的消息 (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT 中心是一项完全托管的服务，有助于在数百万台设备和单个解决方案后端之间实现安全可靠的双向通信。 [IoT 中心入门]教程演示了如何创建 IoT 中心、在其中预配设备标识，以及编写用来发送“设备到云”消息的模拟设备应用。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程是在 [IoT 中心入门]的基础上制作的。 其中了说明了如何：

* 通过 IoT 中心，将云到设备的消息从解决方案后端发送到单个设备。
* 在设备上接收云到设备的消息。
* 通过解决方案后端，请求确认收到从 IoT 中心发送到设备的消息（反馈）。

可以在 [IoT 中心开发人员指南][IoT Hub developer guide - C2D]中找到有关云到设备消息的详细信息。

在本教程的最后，将运行两个 Java 控制台应用：

* **simulated-device**，这是在 [IoT 中心入门]中创建的应用的修改版本，可连接到 IoT 中心并接收云到设备的消息。
* **send-c2d-messages**，它将“云到设备”消息通过 IoT 中心发送到模拟设备应用，然后接收 IoT 中心的送达确认。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 对许多设备平台和语言（包括 C、Java 和 Javascript）提供 SDK 支持。 有关如何将设备连接到本教程中的代码（通常是连接到 Azure IoT 中心）的逐步说明，请参阅 [Azure IoT 开发人员中心]。

要完成本教程，需要以下各项：

* [IoT 中心入门](iot-hub-java-java-getstarted.md)或[处理 IoT 中心设备到云的消息](iot-hub-java-java-process-d2c.md)教程的完整有效版。
* 最新的 [Java SE 开发工具包 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

## <a name="receive-messages-in-the-simulated-device-app"></a>在模拟设备应用中接收消息

在本部分中，会修改在 [IoT 中心入门]中创建的模拟设备应用，以接收来自 IoT 中心的“云到设备”消息。

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

    > [!NOTE]
    > 如果使用 HTTPS（而不使用 MQTT 或 AMQP）作为传输，则 DeviceClient 实例将不会频繁（频率低于每 25 分钟一次）检查 IoT 中心发来的消息。 有关 MQTT、AMQP 和 HTTPS 支持之间的差异，以及 IoT 中心限制的详细信息，请参阅 [IoT 中心开发人员指南][IoT Hub developer guide - C2D]。

4. 若要使用 Maven 生成 **simulated-device** 应用，请在 simulated-device 文件夹中的命令提示符下执行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>发送云到设备的消息

在本部分中，会创建 Java 控制台应用，用于向模拟设备应用发送“云到设备”消息。 需要使用 [IoT 中心入门]教程中添加的设备的设备 ID。 还需要中心的 IoT 中心连接字符串（位于 [Azure 门户]）。

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
    > 可以使用 [Maven 搜索][lnk-maven-service-search]检查是否有最新版本的 **iot-service-client**。

4. 保存并关闭 pom.xml 文件。

5. 使用文本编辑器打开 send-c2d-messages\src\main\java\com\mycompany\app\App.java 文件。

6. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 将以下类级变量添加到 **App** 类，并将 **{yourhubconnectionstring}** 和 **{yourdeviceid}** 替换为前面记下的值：

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
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
    > 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [Transient Fault Handling]（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。


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

    ![运行模拟设备应用][img-simulated-device]

2. 在 send-c2d-messages 文件夹的命令提示符处，运行以下命令以发送云到设备的消息并等待反馈确认：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![运行命令以发送“云到设备”消息][img-send-command]

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何发送和接收云到设备的消息。 

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 套件]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[IoT 中心入门]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT 中心开发人员指南]: iot-hub-devguide.md
[Azure IoT 开发人员中心]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure 门户]: https://portal.azure.com
[Azure IoT 套件]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22