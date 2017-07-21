---
title: "处理 IoT 中心设备到云的消息 (Java) | Microsoft Docs"
description: "如何使用路由规则和自定义终结点将消息发送到其他后端服务，从而处理 IoT 中心的设备到云消息。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 7055e207cfbcc9de02669be9f0e97045769ef217
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017


---
# 处理 IoT 中心设备到云的消息 (Java)
<a id="process-iot-hub-device-to-cloud-messages-java" class="xliff"></a>

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT 中心是一项完全托管的服务，可在数百万个设备和一个解决方案后端之间实现安全可靠的双向通信。 其他教程（[IoT 中心入门]和[使用 IoT 中心发送“云到设备”消息][lnk-c2d]）介绍了如何使用 IoT 中心的“设备到云”和“云到设备”的基本消息传递功能。

本教程以 [IoT 中心入门]教程中所示的代码为基础，说明如何按可缩放的方式通过消息路由处理设备到云的消息。 本教程描述了如何处理需要解决方案后端立即执行操作的消息。 例如，设备可能将发送一条警报消息，触发在 CRM 系统中插入票证。 与此相反，数据点消息仅送入分析引擎。 例如，设备中存储便于日后分析的温度遥测是数据点消息。

在本教程最后，会运行 3 个 Java 控制台应用：

* **simulated-device**（在 [IoT 中心入门]教程中创建的应用的修改版本）会每秒发送一次数据点设备到云的消息，每 10 秒发送一次互动设备到云的消息。 此应用使用 AMQP 协议来与 IoT 中心通信。
* **read-d2c-messages** 显示模拟设备应用发送的遥测数据。
* **read-critical-queue** 从附加到 IoT 中心的服务总线队列中取消关键消息的排队。

> [!NOTE]
> IoT 中心对许多设备平台和语言（包括 C、Java 和 JavaScript）提供 SDK 支持。 若要了解如何将本教程中的模拟设备替换为物理设备，以及如何将设备连接到 IoT 中心，请参阅 [Azure IoT 开发人员中心]。

若要完成本教程，您需要以下各项：

* [IoT 中心入门]教程的完整工作版本。
* 最新的 [Java SE 开发工具包 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* 有效的 Azure 帐户。 （如果你没有帐户，只需花费几分钟就能创建一个 [免费帐户][lnk-free-trial]。）

应具备 [Azure 存储]和 [Azure 服务总线]的一些基础知识。

## 从模拟设备应用发送交互式消息
<a id="send-interactive-messages-from-a-simulated-device-app" class="xliff"></a>

在本部分中，会修改 [IoT 中心入门]教程中创建的模拟设备应用，不定期发送需要立即处理的消息。

1. 使用文本编辑器打开 simulated-device\src\main\java\com\mycompany\app\App.java 文件。 本文件包含你在 [IoT 中心入门]教程中创建的 **simulated-device** 应用的代码。

2. 使用以下代码替换 **MessageSender** 类：

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```

    此方法会将 `"level": "critical"` 属性随机添加到模拟设备发送的消息，该设备可模拟需要应用程序后端立即执行操作的消息。 应用程序将在消息属性中传递此信息（而非在消息正文中），因此 IoT 中心可将消息路由到适当的消息目标。

    > [!NOTE]
    > 可使用消息属性根据各种方案路由消息，包括冷路径处理和此处所示的热路径示例。

2. 保存并关闭 simulated-device\src\main\java\com\mycompany\app\App.java 文件。

    > [!NOTE]
    > 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [Transient Fault Handling]（暂时性故障处理）中建议来实施重试策略（如指数退让）。

3. 若要使用 Maven 生成 **simulated-device** 应用，请在 simulated-device 文件夹中的命令提示符下执行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## 向 IoT 中心添加一个队列并向其路由消息
<a id="add-a-queue-to-your-iot-hub-and-route-messages-to-it" class="xliff"></a>

在本部分中，将创建一个服务总线队列并将其连接到 IoT 中心，还会配置 IoT 中心，根据消息上的现有属性发送消息到队列。 若要深入了解如何处理来自服务总线队列的消息，请参阅[队列入门][lnk-sb-queues-java]教程。

1. 按[队列入门][lnk-sb-queues-java]中所述，创建服务总线队列。 记下命名空间和队列名称。

2. 在 Azure 门户中，打开 IoT 中心并单击“终结点”。

    ![IoT 中心的终结点][30]

3. 在“终结点”边栏选项卡中，单击顶部的“添加”，将队列添加到 IoT 中心。 将终结点命名为“CriticalQueue”，并使用下拉列表选择“服务总线队列”、队列所在的服务总线命名空间和队列名称。 完成后，单击底部的“**保存**”。

    ![添加终结点][31]

4. 现在单击 IoT 中心的“路由”。 单击边栏选项卡顶部的“添加”，创建将消息路由到刚添加的队列的路由规则。 选择“DeviceTelemetry”作为数据源。 输入 `level="critical"` 作为条件，然后选择刚添加为自定义终结点的队列作为路由规则终结点。 完成后，单击底部的“**保存**”。

    ![添加路由][32]

    请确保回退路由设为“开”。 此设置是 IoT 中心的默认配置。

    ![回退路由][33]

## （可选）从队列终结点读取
<a id="optional-read-from-the-queue-endpoint" class="xliff"></a>

可按照[队列入门][lnk-sb-queues-java]中的说明，选择性地从队列终结点读取消息。 将应用命名为 **read-critical-queue**。

## 运行应用程序
<a id="run-the-applications" class="xliff"></a>

现在即可运行 3 个应用程序。

1. 若要运行 **read-d2c-messages** 应用程序，请在命令提示符或外壳处导航到 read-d2c 文件夹并执行以下命令：

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![运行 read-d2c-messages][readd2c]

2. 若要运行 **read-critical-queue** 应用程序，请在命令提示符或外壳处导航到 read-critical-queue 文件夹并执行以下命令：

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![运行 read-critical-messages][readqueue]

3. 若要运行 **simulated-device** 应用，请在命令提示符或外壳处导航到 simulated-device 文件夹并执行以下命令：

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![运行 simulated-device][simulateddevice]

## 后续步骤
<a id="next-steps" class="xliff"></a>

在本教程中，介绍了如何使用 IoT 中心的消息路由功能可靠地分派设备到云的消息。

[如何使用 IoT 中心发送云到设备的消息][lnk-c2d]介绍了如何从解决方案后端向设备发送消息。

若要查看使用 IoT 中心的完整端到端解决方案的示例，请参阅 [Azure IoT 套件][lnk-suite]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

若要详细了解 IoT 中心的消息路由，请参阅[使用 IoT 中心发送和接收消息][lnk-devguide-messaging]。

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure 存储]: https://azure.microsoft.com/documentation/services/storage/
[Azure 服务总线]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT 中心开发人员指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[IoT 中心入门]: iot-hub-java-java-getstarted.md
[Azure IoT 开发人员中心]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[暂时性故障处理]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

