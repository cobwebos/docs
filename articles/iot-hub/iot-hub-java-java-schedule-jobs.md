---
title: "使用 Azure IoT 中心安排作业 (Java) | Microsoft Docs"
description: "如何安排 Azure IoT 中心作业对多台设备调用直接方法并设置必需属性。 使用适用于 Java 的 Azure IoT 设备 SDK 实现模拟设备应用，并使用适用于 Java 的 Azure IoT 服务 SDK 实现用于运行作业的服务应用。"
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
ms.date: 07/10/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 25ad960e456d20a3695d4245ddddc9772cbf47a0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/31/2017

---
# <a name="schedule-and-broadcast-jobs-java"></a>计划和广播作业 (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

使用 Azure IoT 中心来计划和跟踪可更新数百万台设备的作业。 使用作业可以：

* 更新所需属性
* 更新标记
* 调用直接方法

作业包装上述一项操作，并跟踪一组设备中的执行情况。 设备孪生查询定义作业执行的一组设备。 例如，后端应用可以使用作业在 10,000 台设备上调用直接方法来重启设备。 使用设备孪生查询指定设备集，并将作业计划为在以后运行。 每个设备接收和执行“重新启动”直接方法时，该作业会跟踪进度。

若要详细了解其中的每项功能，请参阅：

* 设备孪生和属性：[设备孪生入门](iot-hub-java-java-twin-getstarted.md)
* 直接方法：[IoT 中心开发人员指南 - 直接方法](iot-hub-devguide-direct-methods.md)和[教程：使用直接方法](iot-hub-java-java-direct-methods.md)

本教程演示如何：

* 创建设备应用，用于实现名为 lockDoor 的直接方法。 该设备应用还从后端应用接收所需的属性更改。
* 创建一个后端应用，用于创建一个作业在多个设备上调用 **lockDoor** 直接方法。 另一个作业将所需的属性更新发送到多个设备。

本教程结束时，将有一个 java 控制台设备应用，以及一个 java 控制台后端应用：

simulated-device：连接到 IoT 中心、实现 lockDoor 直接方法，并处理所需的属性更改。

schedule-jobs：使用作业来调用 lockDoor 直接方法，并在多个设备上更新设备孪生的必需属性。

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 一文介绍了可用于构建设备和后端应用的 Azure IoT SDK。

## <a name="prerequisites"></a>先决条件

若要完成本教程，你需要：

* 最新的 [Java SE 开发工具包 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](http://azure.microsoft.com/pricing/free-trial/)。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

若要以编程方式创建设备标识，请参阅[使用 Java 将设备连接到 IoT 中心](iot-hub-java-java-getstarted.md#create-a-device-identity)一文中的相应部分。 还可使用 [iothub-explorer](https://github.com/Azure/iothub-explorer) 工具向 IoT 中心添加设备。

## <a name="create-the-service-app"></a>创建服务应用

本部分中将创建一个使用作业进行如下操作的 Java 控制台应用：

* 在多台设备上调用 lockDoor 直接方法。
* 向多台设备发送必需属性。

创建应用：

1. 在开发计算机上，创建名为 `iot-java-schedule-jobs` 的空文件夹。

1. 在 `iot-java-schedule-jobs` 文件夹中，通过命令提示符使用以下命令创建名为 schedule-jobs 的 Maven 项目。 请注意，这是一条很长的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示符下，导航到 `schedule-jobs` 文件夹。

1. 使用文本编辑器打开 `schedule-jobs` 文件夹中的 `pom.xml` 文件，在 **dependencies** 节点中添加以下依赖项。 通过此依赖项可以使用应用中的 **iot-service-client** 包来与 IoT 中心通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.6.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-service-client**。

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

1. 保存并关闭 `pom.xml` 文件。

1. 使用文本编辑器打开 `schedule-jobs\src\main\java\com\mycompany\app\App.java` 文件。

1. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

1. 将以下类级变量添加到 **App** 类。 将 `{youriothubconnectionstring}` 替换为在“创建 IoT 中心”部分记下的 IoT 中心连接字符串：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. 向 App 类添加以下方法，以安排作业更新设备孪生中的 Building 和 Floor 必需属性：

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

1. 若要安排作业调用 lockDoor 方法，请向 App 类添加以下方法：

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

1. 若要监视作业，请向 App 类添加以下方法：

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

1. 若要查询已运行作业的详细信息，请添加以下方法：

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

1. 更新 **main** 方法签名，以包含以下 `throws` 子句：

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. 若要依次运行和监视两个作业，请向 main 方法添加以下代码：

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

1. 保存并关闭 `schedule-jobs\src\main\java\com\mycompany\app\App.java` 文件

1. 生成 schedule-jobs 应用并更正任何错误。 在命令提示符下，导航到 `schedule-jobs` 文件夹并运行以下命令：

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>创建设备应用

本部分中将创建一个 Java 控制台应用，处理从 IoT 中心发送的必需属性并实现直接方法调用。

1. 在命令提示符下使用以下命令，在 `iot-java-schedule-jobs` 文件夹中创建名为 **simulated-device** 的 Maven 项目。 请注意，这是一条很长的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示符下，导航到 `simulated-device` 文件夹。

1. 使用文本编辑器打开 `simulated-device` 文件夹中的 `pom.xml` 文件，在 **dependencies** 节点中添加以下依赖项。 通过此依赖项可以使用应用中的 **iot-device-client** 包来与 IoT 中心进行通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-device-client**。

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

1. 保存并关闭 `pom.xml` 文件。

1. 使用文本编辑器打开 `simulated-device\src\main\java\com\mycompany\app\App.java` 文件。

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
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    本示例应用在实例化 **DeviceClient** 对象时使用 **protocol** 变量。 目前，若要使用设备孪生功能，必须使用 MQTT 协议。

1. 若要在控制台中列显设备孪生通知，请向 App 类添加以下嵌套类：

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. 若要在控制台中列显直接方法通知，请向 App 类添加以下嵌套类：

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. 若要处理 IoT 中心的直接方法调用，请向 App 类添加以下嵌套类：

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

1. 更新 **main** 方法签名，以包含以下 `throws` 子句：

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. 将以下代码添加到 **main** 方法，以便：
    * 创建用来与 IoT 中心通信的设备客户端。
    * 创建一个 **Device** 对象用于存储设备孪生属性。

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

1. 若要启动设备客户端服务，请向 main 方法添加以下代码：

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

1. 若要在关闭前等待用户按 Enter 键，请向 main 方法末尾添加以下代码：

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. 保存并关闭 `simulated-device\src\main\java\com\mycompany\app\App.java` 文件。

1. 生成 **simulated-device** 应用并更正任何错误。 在命令提示符下，导航到 `simulated-device` 文件夹并运行以下命令：

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>运行应用

现在可以运行控制台应用了。

1. 在 `simulated-device` 文件夹中的命令提示符处，运行以下命令启动设备应用用于侦听所需属性更改和直接方法调用：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![设备客户端启动](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. 在 `schedule-jobs` 文件夹中的命令提示符处，运行以下命令以运行 schedule-jobs 服务应用，从而运行两个作业。 第一个作业设置所需的属性值，第二个作业调用直接方法：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT 中心服务应用创建两个作业](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. 设备应用处理所需的属性更改和直接方法调用：

    ![设备客户端对更改作出响应](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，然后在 IoT 中心的标识注册表中创建了设备标识。 创建了运行两个作业的后端应用。 第一个作业设置了所需的属性值，第二个作业调用了直接方法。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门](iot-hub-java-java-getstarted.md)教程学习如何从设备发送遥测数据。
* 通过[使用直接方法](iot-hub-java-java-direct-methods.md)教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。

