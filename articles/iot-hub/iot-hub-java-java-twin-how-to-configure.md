---
title: "使用 Azure IoT 中心设备孪生属性 (Java) | Microsoft Docs"
description: "如何使用 Azure IoT 中心设备孪生配置设备。 使用用于 Java 的 Azure IoT 设备 SDK 实现模拟设备应用，并使用用于 Java 的 Azure IoT 服务 SDK 实现可使用设备孪生更改设备配置的服务应用。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 72b57a2495d1a03a57923fb171ee17c0f870ddc7
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>使用所需属性配置设备

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

本教程结束时，将会创建两个 Java 控制台应用：

* **simulated-device**，一个模拟设备应用，它等待所需配置更新并报告模拟配置更新过程的状态。
* **set-configuration**，一个后端应用，用于在设备上设置所需配置并查询配置更新过程。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 文章介绍了可用于构建设备和后端应用的 Azure IoT SDK。
> 
> 

要完成本教程，需要以下各项：

* 最新的 [Java SE 开发工具包 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

如果已按照[设备孪生入门][lnk-twin-tutorial]教程执行了操作，则现在已有一个 IoT 中心和一个名为 **myDeviceId** 的设备标识。 在这种情况下，可以跳到[创建模拟设备应用][lnk-how-to-configure-createapp]部分。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>创建模拟设备应用
在此部分中，会创建一个 Java 控制台应用，它作为 **myDeviceId** 连接到中心，等待所需配置更新，然后对模拟配置更新过程报告更新。

1. 创建一个名为 dt-get-started 的空文件夹。

1. 在命令提示符下使用以下命令，在 dt-get-started 文件夹中创建一个名为 **simulated-device** 的 Maven 项目。 请注意，这是一条很长的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示符下，导航到 simulated-device 文件夹。

1. 使用文本编辑器打开 simulated-device 文件夹中的 pom.xml 文件，并在 dependencies 节点中添加以下依赖项。 此依赖项使得可以使用应用中的 iot-service-client 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven search][lnk-maven-device-search] 检查是否有最新版本的 **iot-device-client**。

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

1. 使用文本编辑器打开 simulated-device\src\main\java\com\mycompany\app\App.java 源文件。

1. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 将以下类级变量添加到 **App** 类。 使用“创建设备标识”部分所述的设备连接字符串替换 {yourdeviceconnectionstring}：

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. 若要为设备孪生状态事件实现回调处理程序（用于调试），请将以下嵌套类添加到 **App** 类：

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. 将以下嵌套类添加到 **App** 类：

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > TelemetryConfig 类扩展了 [Device 类][lnk-java-device-class]来获取对所需属性回调的访问权限。

1. 修改 main 方法的签名以引发以下异常：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. 将以下代码添加到 **main** 方法以实例化 **DeviceClient** 和 **TelemetryConfig**：

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. 将以下代码添加到 **main** 方法以启动设备孪生服务：

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. 将以下代码添加到 **main** 方法以在必要时关闭设备模拟器：

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. 保存并关闭 simulated-device\src\main\java\com\mycompany\app\App.java 文件。

1. 生成 simulated-device 后端应用并更正任何错误。 在命令提示符下，导航到 simulated-device 文件夹并运行以下命令：

    `mvn clean package -DskipTests`

   > [!NOTE]
   > 本教程不模拟并发配置更新的任何行为。 某些配置更新进程在更新运行过程中可能能够适应目标配置的更改，某些配置更新进程则可能必须将它们排队，某些配置更新进程会拒绝它们并显示错误情况。 请确保考虑特定配置过程所需的行为，并在开始配置更改之前添加相应的逻辑。
   > 
   > 

## <a name="create-the-service-app"></a>创建服务应用
在本部分中，将创建一个 Java 控制台应用，该应用通过新的遥测配置对象更新与 **myDeviceId** 关联的设备孪生的所需属性。 然后，它会查询在 IoT 中心内存储的设备孪生，并显示所需的设备配置与报告的设备配置之间的差异。

1. 在命令提示符下使用以下命令，在 dt-get-started 文件夹中创建一个名为 **set-configuration** 的 Maven 项目。 请注意，这是一条很长的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示符下，导航到 set-configuration 文件夹。

1. 使用文本编辑器打开 trigger-reboot 文件夹中的 pom.xml 文件，并在 dependencies 节点中添加以下依赖项。 此依赖项使得可以使用应用中的 iot-service-client 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven search][lnk-maven-service-search] 检查是否有最新版本的 **iot-service-client**。

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

1. 使用文本编辑器打开 set-configuration\src\main\java\com\mycompany\app\App.java 源文件。

1. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. 将以下类级变量添加到 **App** 类。 使用“创建 IoT 中心”部分中记下的 IoT 中心连接字符串替换 {youriothubconnectionstring}：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. 若要查询并更新模拟设备上的设备孪生，请将以下代码添加到 **main** 方法中：

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. 保存并关闭 set-desired-configuration\src\main\java\com\mycompany\app\App.java 文件。

1. 生成 **set-configuration** 后端应用并更正任何错误。 在命令提示符下，导航到 set-configuration 文件夹并运行以下命令：

    `mvn clean package -DskipTests`
   
    此代码检索 **myDeviceId** 的设备孪生，并使用新的遥测配置对象更新其所需属性。
    然后，该代码会每隔 10 秒钟查询一次存储在 IoT 中心的设备孪生，并打印所需遥测配置和报告遥测配置。 请参阅 [IoT 中心查询语言][lnk-query]以了解如何跨所有设备生成丰富的报告。
   
   > [!IMPORTANT]
   > 此应用程序每 10 秒查询一次 IoT 中心以用于说明目的，直至设备完成更新。 使用查询跨多个设备生成面向用户的报表，而不检测更改。 如果解决方案需要设备事件的实时通知，请使用[孪生通知][lnk-twin-notifications]。
   > 

   > [!IMPORTANT]
   > 设备报告操作与查询结果之间最多存在一分钟的延迟。 这是为了使查询基础结构可以采用非常大的规模来工作。  若要检索单个设备孪生的一致视图，请使用 **DeviceTwin** 类中的 **getDeviceTwin** 方法。
   > 
   > 

## <a name="run-the-apps"></a>运行应用

现在可以运行应用了。

1. 在命令提示符下，在 simulated-device 文件夹中运行以下命令，以开始侦听来自 IoT 中心的设备孪生调用：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. 在命令提示符下，在 set-configuration 文件夹中运行以下命令，从 IoT 中心查询并更新模拟设备上的设备孪生：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. 模拟设备对重启直接方法调用做出响应：

    ![Java IoT 中心模拟设备应用对设备孪生调用做出响应][img-deviceconfigured]

## <a name="next-steps"></a>后续步骤
在本教程中，用户已从解决方案后端将所需配置设置为所需属性，此外还编写了一个设备应用来检测该更改并模拟多步骤更新过程（通过报告属性报告其状态）。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门][lnk-iothub-getstarted]教程学习如何从设备发送遥测；
* 查看[计划和广播作业][lnk-schedule-jobs]教程，学习如何对大型设备集计划或执行操作。
* 通过[使用直接方法][lnk-methods-tutorial]教程学习如何以交互方式控制设备（例如如何从用户控制的应用打开风扇）。

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
