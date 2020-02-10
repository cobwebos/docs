---
title: 使用 Java 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用用于 Java 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: fcc2013f67c6e91182979a9bcab683894088a1d5
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110376"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>通过 IoT 中心将文件从设备上传到云（Java）

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程基于使用[Iot 中心发送云到设备的消息](iot-hub-java-java-c2d.md)教程中的代码，演示如何使用[iot 中心的文件上传功能](iot-hub-devguide-file-upload.md)将文件上传到[Azure blob 存储](../storage/index.yml)。 本教程介绍如何：

* 安全提供具有 Azure blob URI 的设备，用于上传文件。

* 使用 IoT 中心文件上传通知触发处理应用后端中的文件。

使用 IoT 中心将[遥测数据从设备发送到 iot 中心](quickstart-send-telemetry-java.md)快速入门教程和[使用 iot 中心发送云到设备的消息](iot-hub-java-java-c2d.md)教程显示 iot 中心的基本设备到云和云到设备的消息传送功能。 [使用 IoT 中心配置消息路由](tutorial-routing.md)教程介绍了一种在 Azure Blob 存储中可靠存储设备到云消息的方法。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件
* 视频
* 以高频率采样的振动数据
* 某种形式的预处理数据。

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.yml) 堆栈等工具在云中批处理这些文件。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

在本教程的最后，将运行两个 Java 控制台应用：

* **模拟设备**，在 [使用 IoT 中心发送云到设备的消息] 教程中创建的应用的修改版本。 该应用使用 IoT 中心提供的 SAS URI 将文件上传到存储。

* **read-file-upload-notification**，它可以接收来自 IoT 中心的文件上传通知。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、.NET 和 Javascript）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。

## <a name="prerequisites"></a>先决条件

* [Java SE 开发工具包 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。 请确保在“长期支持”下选择“Java 8”以获取 JDK 8 的下载。

* [Maven 3](https://maven.apache.org/download.cgi)

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* 请确保已在防火墙中打开端口8883。 本文中的设备示例使用了 MQTT 协议，该协议通过端口8883进行通信。 此端口可能在某些企业和教育网络环境中被阻止。 有关此问题的详细信息和解决方法，请参阅[连接到 IoT 中心（MQTT）](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

在本部分中，会修改在[使用 Iot 中心发送云到设备消息](iot-hub-java-java-c2d.md)中创建的设备应用，以便将文件上传到 iot 中心。

1. 将一个图像文件复制到 `simulated-device` 文件夹并将其重命名为 `myimage.png`。

2. 使用文本编辑器打开 `simulated-device\src\main\java\com\mycompany\app\App.java` 文件。

3. 将变量声明添加到 **App** 类：

    ```java
    private static String fileName = "myimage.png";
    ```

4. 若要处理文件上传状态回调消息，请将以下嵌套类添加到 **App** 类：

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. 若要将图像上传到 IoT 中心，请将以下方法添加 **App** 类，以便将图像上传到 IoT 中心：

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. 修改 **main** 方法以调用 **uploadFile** 方法，如以下代码片段中所示：

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. 使用以下命令生成 **simulated-device** 应用并检查错误：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中，你将创建一个后端服务，以接收从[设备发送遥测到 iot 中心](quickstart-send-telemetry-java.md)中创建的 IoT 中心的文件上传通知消息。 若要接收文件上传通知消息，您的服务需要**服务连接**权限。 默认情况下，每个 IoT 中心都创建有一个名为 "**服务**" 的共享访问策略，该策略将授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>接收文件上传通知

本部分中的操作将创建一个 Java 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

1. 在命令提示符下使用以下命令，创建名为 **read-file-upload-notification** 的 Maven 项目。 请注意，此命令是一条很长的命令：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. 在命令提示符下，导航到新的 `read-file-upload-notification` 文件夹。

3. 使用文本编辑器打开 `pom.xml` 文件夹中的 `read-file-upload-notification` 文件，在 **dependencies** 节点中添加以下依赖项。 这样即可使用应用程序中的 **iothub-java-service-client** 包来与 IoT 中心服务通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 **Maven 搜索**检查是否有最新版本的 [iot-service-client](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)。

4. 保存并关闭 `pom.xml` 文件。

5. 使用文本编辑器打开 `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` 文件。

6. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. 将以下类级变量添加到 **App** 类。 将 `{Your IoT Hub connection string}` 占位符值替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串：

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. 若要在控制台中列显有关文件上传的信息，请将以下嵌套类添加到 **App** 类：

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. 若要启动侦听文件上传通知的线程，请将以下代码添加到 **main** 方法：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. 保存并关闭 `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` 文件。

11. 使用以下命令生成 **read-file-upload-notification** 应用并检查错误：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>运行应用程序

现在，你已准备就绪，可以运行应用程序了。

在 `read-file-upload-notification` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

在 `simulated-device` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

以下屏幕截图显示 **simulated-device** 应用的输出：

![simulated-device 应用的输出](media/iot-hub-java-java-upload/simulated-device.png)

以下屏幕截图显示 **read-file-upload-notification** 应用的输出：

![read-file-upload-notification 应用的输出](media/iot-hub-java-java-upload/read-file-upload-notification.png)

可以使用门户查看所配置的存储容器中上传的文件：

![上传的文件](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 IoT Edge 模拟设备](../iot-edge/tutorial-simulate-device-linux.md)
