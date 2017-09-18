---
title: "使用 Azure IoT 中心的直接方法 (Java) | Microsoft Docs"
description: "如何使用 Azure IoT 中心直接方法。 使用适用于 Java 的 Azure IoT 设备 SDK 实现包含直接方法的模拟设备应用，并使用适用于 Java 的 Azure IoT 服务 SDK 实现调用直接方法的服务应用。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 4fb759ecd7767c126bc22165494652039ba1caa4
ms.contentlocale: zh-cn
ms.lasthandoff: 09/08/2017

---
# <a name="use-direct-methods-java"></a>使用直接方法 (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教程中，将创建两个 Java 控制台应用：

* **invoke-direct-method**：一个 Java 后端应用，它调用模拟设备应用上的方法并显示响应。
* **模拟设备**：一个 Java 应用，它模拟使用创建的设备标识连接到 IoT 中心的设备。 此应用对后端直接调用做出响应。

> [!NOTE]
> 有关 SDK 的信息（可以使用这些 SDK 构建在设备和解决方案后端上运行的应用程序），请参阅 [Azure IoT SDK][lnk-hub-sdks]。

要完成本教程，需要：

* Java SE 8。 <br/> [准备开发环境][lnk-dev-setup]介绍了如何在 Windows 或 Linux 上安装本教程所用的 Java。
* Maven 3。  <br/> [准备开发环境][lnk-dev-setup]介绍如何在 Windows 或 Linux 上安装本教程所用的 [Maven][lnk-maven]。
* [Node.js 版本 0.10.0 或更高版本](http://nodejs.org)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

在本部分中，会创建一个 Java 控制台应用，用以响应解决方案后端调用的方法。

1. 创建一个名为 iot-java-direct-method 的空文件夹。

1. 在命令提示符下使用以下命令，在 iot-java-direct-method 文件夹中创建一个名为 **simulated-device** 的 Maven 项目。 以下命令是一条很长的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示符下，导航到 simulated-device 文件夹。

1. 使用文本编辑器，打开 simulated-device 文件夹中的 pom.xml 文件，并在 **dependencies** 节点中添加以下依赖项。 此依赖项使得可以使用应用中的 iot-device-client 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索][lnk-maven-device-search]检查是否有最新版本的 **iot-device-client**。

1. 在 **dependencies** 节点后添加以下 **build** 节点。 此配置指示 Maven 使用 Java 1.8 来生成应用：

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

1. 保存并关闭 pom.xml 文件。

1. 使用文本编辑器打开 simulated-device\src\main\java\com\mycompany\app\App.java 文件。

1. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 将以下类级变量添加到 **App** 类。 将 `{youriothubname}` 替换为 IoT 中心名称，将 `{yourdevicekey}` 替换为在“创建设备标识”部分中生成的设备密钥值：

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    本示例应用在实例化 **DeviceClient** 对象时使用 **protocol** 变量。 

1. 若要向 IoT 中心返回状态代码，向 App 类添加以下嵌套类：

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. 若要处理从解决方案后端进行的直接方法调用，向 App 类添加以下嵌套类：

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
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

1. 若要创建 DeviceClient 并侦听直接方法调用，将一个 main 方法添加到 App 类中：

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. 保存并关闭 simulated-device\src\main\java\com\mycompany\app\App.java 文件

1. 生成 **simulated-device** 应用并更正任何错误。 在命令提示符下，导航到 simulated-device 文件夹并运行以下命令：

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>在设备上调用直接方法

在本部分中，创建一个 Java 控制台应用，用以调用一个直接方法并显示响应。 此控制台应用连接到 IoT 中心来调用该直接方法。

1. 在命令提示符下使用以下命令，在 iot-java-direct-method 文件夹中创建一个名为 **invoke-direct-method** 的 Maven 项目。 以下命令是一条很长的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示符下，导航到 invoke-direct-method 文件夹。

1. 使用文本编辑器，打开 invoke-direct-method 文件夹中的 pom.xml 文件，并在 **dependencies** 节点中添加以下依赖项。 此依赖项使得可以使用应用中的 iot-service-client 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索][lnk-maven-service-search]检查是否有最新版本的 **iot-service-client**。

1. 在 **dependencies** 节点后添加以下 **build** 节点。 此配置指示 Maven 使用 Java 1.8 来生成应用：

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

1. 保存并关闭 pom.xml 文件。

1. 使用文本编辑器打开 invoke-direct-method\src\main\java\com\mycompany\app\App.java 文件。

1. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. 将以下类级变量添加到 **App** 类。 将 `{youriothubconnectionstring}` 替换为在“创建 IoT 中心”部分记下的 IoT 中心连接字符串：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. 若要在模拟设备上调用该方法，将以下代码在添加到 main 方法中：

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. 保存并关闭 invoke-direct-method\src\main\java\com\mycompany\app\App.java 文件

1. 生成 invoke-direct-method 应用并更正任何错误。 在命令提示符下，导航到 invoke-direct-method 文件夹并运行以下命令：

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>运行应用

现在可以运行控制台应用了。

1. 在命令提示符下，在 simulated-device 文件夹中运行以下命令，开始侦听从 IoT 中心发出的方法调用：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![侦听直接方法调用的 Java IoT 中心模拟设备应用][8]

1. 在命令提示符下，在 invoke-direct-method 文件夹中运行以下命令，从 IoT 中心调用模拟设备上的方法：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![调用直接方法的 Java IoT 中心服务应用][7]

1. 模拟设备对直接方法调用做出响应：

    ![对直接方法调用进行响应的 Java IoT 中心模拟设备应用][9]

## <a name="next-steps"></a>后续步骤

在本教程中，已在 Azure 门户中配置了新的 IoT 中心，然后在 IoT 中心的标识注册表中创建了设备标识。 已通过此设备标识启用模拟设备应用的相关功能，使之能够响应通过云调用的方法。 还创建了一个应用，用于调用设备上的方法并显示来自设备的响应。

若要探索其他 IoT 方案，请参阅[在多个设备上计划作业][lnk-devguide-jobs]。

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22

