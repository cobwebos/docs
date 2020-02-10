---
title: Azure IoT 中心设备管理入门 (Java) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备管理启动远程设备重启。 使用适用于 Java 的 Azure IoT 设备 SDK 实现包含直接方法的模拟设备应用，并使用适用于 Java 的 Azure IoT 服务 SDK 实现调用直接方法的服务应用。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f68e25a618f5c6499ccc9d76c510eab8f1650330
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110881"
---
# <a name="get-started-with-device-management-java"></a>设备管理入门 (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

本教程演示如何：

* 使用 Azure 门户创建 IoT 中心，以及如何在 IoT 中心创建设备标识。

* 创建实现重启设备的直接方法的模拟设备应用。 直接方法是从云中调用的。

* 创建通过 IoT 中心在模拟设备应用上调用重启直接方法的应用。 之后此应用会监视设备的报告属性，查看重启操作何时完成。

本教程结束时，将会创建两个 Java 控制台应用：

simulated-device。 此应用：

* 使用之前创建的设备标识连接到 IoT 中心。

* 接收重启直接方法调用。

* 模拟物理重启。

* 通过报告属性报告上次重启的时间。

trigger-reboot。 此应用：

* 在模拟设备应用中调用直接方法。

* 显示模拟设备发送的直接方法调用的响应。

* 显示更新的报告属性。

> [!NOTE]
> 有关 SDK 的信息（可以使用这些 SDK 构建在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK](iot-hub-devguide-sdks.md)。

## <a name="prerequisites"></a>先决条件

* [Java SE 开发工具包 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。 请确保在“长期支持”下选择“Java 8”以获取 JDK 8 的下载。

* [Maven 3](https://maven.apache.org/download.cgi)

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* 请确保已在防火墙中打开端口8883。 本文中的设备示例使用了 MQTT 协议，该协议通过端口8883进行通信。 此端口可能在某些企业和教育网络环境中被阻止。 有关此问题的详细信息和解决方法，请参阅[连接到 IoT 中心（MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>使用直接方法在设备上触发远程重新启动

本部分将创建一个进行如下操作的 Java 控制台应用：

1. 在模拟设备应用中调用重启直接方法。

2. 显示响应。

3. 轮询设备发送的报告属性，以确定重启的完成时间。

此控制台应用连接到 IoT 中心，以便调用该直接方法并读取报告属性。

1. 创建名为 " **dm-入门**" 的空文件夹。

2. 在**dm 入门**文件夹中，在命令提示符处使用以下命令创建一个名为**触发器-reboot**的 Maven 项目：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. 在命令提示符下，导航到**触发器重新启动**文件夹。

4. 使用文本编辑器，在**触发器重启**文件夹中打开**pom**文件，并在 "**依赖项**" 节点中添加以下依赖项。 此依赖项使得可以使用应用中的 iot-service-client 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 **Maven 搜索**检查是否有最新版本的 [iot-service-client](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)。

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

7. 使用文本编辑器打开**trigger-reboot\src\main\java\com\mycompany\app\App.java**源文件。

8. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. 将以下类级变量添加到 **App** 类。 将 `{youriothubconnectionstring}` 替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. 若要实现每隔 10 秒读取一次设备孪生提供的报告属性的线程，请将以下嵌套类添加到 App 类：

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. 将 main 方法签名修改为抛出以下异常：

    ```java
    public static void main(String[] args) throws IOException
    ```

12. 若要调用模拟设备上的重新启动直接方法，请将**main**方法中的代码替换为以下代码：

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. 若要启动轮询模拟设备提供的报告属性的线程，请将以下代码添加到 main 方法：

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. 若要能够停止应用，请将以下代码添加到 main 方法：

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. 保存并关闭**trigger-reboot\src\main\java\com\mycompany\app\App.java**文件。

16. 生成 trigger-reboot 后端应用并更正任何错误。 在命令提示符下，导航到**触发器重启**文件夹，并运行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分中将创建一个模拟设备的 Java 控制台应用。 该应用侦听 IoT 中心发出的重启直接方法调用并快速响应该调用。 然后该应用会休眠一段时间，以模拟重启过程，之后该应用会使用报告属性通知 trigger-reboot 后端应用重启已完成。

1. 在**dm 入门**文件夹中，在命令提示符处使用以下命令创建名为**模拟设备**的 Maven 项目：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示符下，导航到**模拟设备**文件夹。

3. 使用文本编辑器打开**模拟设备**文件夹中的**pom**文件，并在 "**依赖项**" 节点中添加以下依赖项。 此依赖项使得可以使用应用中的 iot-service-client 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 **Maven 搜索**检查是否有最新版本的 [iot-device-client](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)。

4. 将以下依赖项添加到 "**依赖项**" 节点。 此依赖项为 Apache [SLF4J](https://www.slf4j.org/)日志记录外观配置 NOP，设备客户端 SDK 使用该外观实现日志记录。 此配置是可选的，但是，如果您省略此配置，则在运行该应用程序时，您可能会在控制台中看到一条警告。 有关设备客户端 SDK 中的[日志记录的](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)详细信息，请参阅*适用于 Java 的 AZURE IoT 设备 SDK*自述文件的示例。

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

7. 使用文本编辑器打开**simulated-device\src\main\java\com\mycompany\app\App.java**源文件。

8. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. 将以下类级变量添加到 **App** 类。 将 `{yourdeviceconnectionstring}` 替换为在[IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)部分中记下的设备连接字符串：

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. 若要为直接方法状态事件实现回调处理程序，请将以下嵌套类添加到 App 类：

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. 若要为设备孪生状态事件实现回调处理程序，请将以下嵌套类添加到 App 类：

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. 若要为属性事件实现回调处理程序，请将以下嵌套类添加到 App 类：

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. 若要实现一个模拟设备重启的线程，请将以下嵌套类添加到 App 类。 线程会休眠五秒，然后设置 lastReboot 报告属性：

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. 若要在设备上实现直接方法，请将以下嵌套类添加到 App 类。 当模拟应用收到对**reboot**直接方法的调用时，它会向调用方返回确认，然后启动一个线程来处理重启：

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. 修改 main 方法的签名以引发以下异常：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. 若要实例化**DeviceClient**，请将**main**方法中的代码替换为以下代码：

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. 若要开始侦听直接方法调用，请将以下代码添加到 main 方法：

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. 若要关闭设备模拟器，请将以下代码添加到 main 方法：

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. 保存并关闭 simulated-device\src\main\java\com\mycompany\app\App.java 文件。

20. 生成 **simulated-device** 应用并更正任何错误。 在命令提示符下，导航到**模拟设备**文件夹，并运行以下命令：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>运行应用

你现在已准备好运行应用。

1. 在**模拟设备**文件夹的命令提示符处运行以下命令，开始侦听来自 IoT 中心的重新启动方法调用：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![侦听重启直接方法调用的 Java IoT 中心模拟设备应用](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. 在**触发器重启**文件夹中的命令提示符下，运行以下命令，从 IoT 中心调用模拟设备上的重新启动方法：

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![调用重启直接方法的 Java IoT 中心服务应用](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. 模拟设备对重启直接方法调用做出响应：

    ![对直接方法调用进行响应的 Java IoT 中心模拟设备应用](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
