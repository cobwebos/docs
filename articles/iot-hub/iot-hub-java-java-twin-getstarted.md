---
title: Azure IoT 中心设备孪生入门 (Java) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备孪生添加标记，并使用 IoT 中心查询。 使用适用于 Java 的 Azure IoT 设备 SDK 实现设备应用，并使用适用于 Java 的 Azure IoT 服务 SDK 实现可添加标记并运行 IoT 中心查询的服务应用。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt
ms.openlocfilehash: 3ea2f0eec12d756a898f1761f6b22fd034c1bc3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732460"
---
# <a name="get-started-with-device-twins-java"></a>设备孪生入门 (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程中，将创建两个 Java 控制台应用：

* **add-tags-query**：一个 Java 后端应用，用于添加标记并查询设备孪生。
* **simulated-device**：Java 设备应用，它连接到 IoT 中心，并使用报告的属性报告其连接状态。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文介绍了可用于构建设备和后端应用的 Azure IoT SDK。

## <a name="prerequisites"></a>先决条件

* [Java SE 开发工具包 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。 请确保在“长期支持”下选择“Java 8”以获取 JDK 8 的下载。

* [Maven 3](https://maven.apache.org/download.cgi)

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>创建服务应用

本部分将创建一个 Java 应用，用于将位置元数据作为标记添加到 IoT 中心内与 **myDeviceId** 关联的设备孪生。 该应用首先在 IoT 中心查询位于美国的设备，然后查询报告已建立移动电话网络连接的设备。

1. 在开发计算机上，创建名为 iot-java-twin-getstarted 的空文件夹。

2. 在 iot-java-twin-getstarted 文件夹中，通过命令提示符使用以下命令创建名为 add-tags-query 的 Maven 项目：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. 在命令提示符下，导航到 add-tags-query 文件夹。

4. 使用文本编辑器打开 add-tags-query 文件夹中的 pom.xml 文件，并在 dependencies 节点中添加以下依赖项  。 通过此依赖项可以使用应用中的 **iot-service-client** 包来与 IoT 中心通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-service-client**。

5. 在 **dependencies** 节点后添加以下 **build** 节点。 此配置指示 Maven 使用 Java 1.8 来生成应用。

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. 保存并关闭 pom.xml 文件。

7. 使用文本编辑器打开 add-tags-query\src\main\java\com\mycompany\app\App.java 文件。

8. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. 将以下类级变量添加到 **App** 类。 将 `{youriothubconnectionstring}` 替换为在[获取 IoT 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 IoT 中心连接字符串。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. 更新 **main** 方法签名，以包含以下 `throws` 子句：

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. 将 main 方法中的代码替换为以下代码，以创建 DeviceTwin 和 DeviceTwinDevice 对象。 **DeviceTwin** 对象处理与 IoT 中心之间的通信。 **DeviceTwinDevice** 对象使用设备孪生的属性和标记来表示设备孪生：

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. 将以下 `try/catch` 块添加到 **main** 方法：

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. 若要更新设备孪生中的 **region** 和 **plant** 设备孪生标记，请在 `try` 块中添加以下代码：

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. 若要在 IoT 中心查询设备孪生，请将以下代码添加到 `try` 块（添加到上一步骤中添加的代码后面）。 该代码运行两个查询。 每个查询最多返回 100 个设备。

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. 保存并关闭 add-tags-query\src\main\java\com\mycompany\app\App.java 文件

16. 生成 **add-tags-query** 应用并更正任何错误。 在命令提示符下，导航到 add-tags-query 文件夹并运行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>创建设备应用

本部分创建一个 Java 控制台应用，用于设置要发送到 IoT 中心的报告属性值。

1. 在 iot-java-twin-getstarted 文件夹中，通过命令提示符使用以下命令创建名为 simulated-device 的 Maven 项目：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示符下，导航到 simulated-device 文件夹。

3. 使用文本编辑器，打开 simulated-device 文件夹中的 pom.xml 文件，并在 dependencies 节点中添加以下依赖项  。 通过此依赖项可以使用应用中的 iot-device-client 包来与 IoT 中心进行通信。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-device-client**。

4. 将以下依赖项添加到 dependencies 节点。 此依赖项为 Apache [SLF4J](https://www.slf4j.org/) 日志记录外观配置 NOP，设备客户端 SDK 使用它实现日志记录。 此配置是可选的，但是如果省略此配置，则在运行该应用时，可能会在控制台中看到警告。 有关设备客户端 SDK 中的日志记录的详细信息，请参阅适用于 Java 的 Azure IoT 设备 SDK 示例自述文件中的[日志记录](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)。

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. 在 **dependencies** 节点后添加以下 **build** 节点。 此配置指示 Maven 使用 Java 1.8 来生成应用：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. 保存并关闭 pom.xml 文件。

7. 使用文本编辑器打开 simulated-device\src\main\java\com\mycompany\app\App.java 文件。

8. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. 将以下类级变量添加到 **App** 类。 将 `{yourdeviceconnectionstring}` 替换为在[在 IoT 中心内注册新设备](#register-a-new-device-in-the-iot-hub)中复制的设备连接字符串。

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    本示例应用在实例化 **DeviceClient** 对象时使用 **protocol** 变量。

10. 将以下方法添加到 **App** 类以输出有关孪生更新的信息：

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. 将 main 方法中的代码替换为以下代码，以便可：

    * 创建用来与 IoT 中心通信的设备客户端。

    * 创建一个 **Device** 对象用于存储设备孪生属性。

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. 将以下代码添加到 **main** 方法，创建 **connectivityType** 报告属性并将其发送到 IoT 中心：

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. 将以下代码添加到 **main** 方法的末尾。 按 Enter 键，等待一段时间让 IoT 中心报告设备孪生操作的状态。

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. 修改 **main** 方法的签名，包含如下所示的异常：

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. 保存并关闭 simulated-device\src\main\java\com\mycompany\app\App.java 文件。

16. 生成 **simulated-device** 应用并更正任何错误。 在命令提示符下，导航到 simulated-device 文件夹并运行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>运行应用

现在可以运行控制台应用了。

1. 在 add-tags-query 文件夹中的命令提示符下，运行以下命令以运行 add-tags-query 服务应用：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT 中心服务应用会更新标记值并运行设备查询](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    可以看到，**plant** 和 **region** 标记已添加到设备孪生。 第一个查询返回设备，但第二个查询则不会。

2. 在 simulated-device 文件夹中的命令提示符下，运行以下命令将 connectivityType 报告属性添加到设备孪生：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![设备客户端会添加 **connectivityType** 报告属性](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. 在 add-tags-query 文件夹中的命令提示符下，再次运行以下命令以运行 add-tags-query 服务应用：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Java IoT 中心服务应用会更新标记值并运行设备查询](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    现在，设备已将 connectivityType 属性发送到 IoT 中心，第二个查询返回了设备。

## <a name="next-steps"></a>后续步骤

在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已从后端应用以标记形式添加了设备元数据，并编写了一个设备应用用于报告设备孪生中的设备连接信息。 此外，还学习了如何使用类似于 SQL 的 IoT 中心查询语言查询设备孪生信息。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门](quickstart-send-telemetry-java.md)教程学习如何从设备发送遥测数据。

* 通过[使用直接方法](quickstart-control-device-java.md)教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。
