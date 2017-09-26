---
title: "使用 Java 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs"
description: "如何使用用于 Java 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 37a45bb0dd3927a131fc216fe9452962a0fdacfa
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>使用 IoT 中心将文件从设备上传到云

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程的内容基于[使用 IoT 中心发送云到设备的消息](iot-hub-java-java-c2d.md)教程中所述的代码，介绍如何使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)将文件上传到 [Azure Blob 存储](../storage/index.yml)。 本教程介绍如何：

- 安全提供具有 Azure blob URI 的设备，用于上传文件。
- 使用 IoT 中心文件上传通知触发处理应用后端中的文件。

[IoT 中心入门](iot-hub-java-java-getstarted.md)和[使用 IoT 中心发送云到设备的消息](iot-hub-java-java-c2d.md)教程介绍了 IoT 中心提供的基本的设备到云和云到设备的消息传送功能。 [处理设备到云的消息](iot-hub-java-java-process-d2c.md)教程介绍了一种在 Azure Blob 存储中可靠存储设备到云消息的方法。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件
* 视频
* 以高频率采样的振动数据
* 某种形式的预处理数据。

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.md) 堆栈等工具在云中批处理这些文件。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

在本教程的最后，将运行两个 Java 控制台应用：

* **simulated-device**，这是 [使用 IoT 中心发送云到设备的消息] 教程中创建的应用的修改版本。 该应用使用 IoT 中心提供的 SAS URI 将文件上传到存储。
* **read-file-upload-notification**，它可以接收来自 IoT 中心的文件上传通知。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、.NET 和 Javascript）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心]。

要完成本教程，需要以下各项：

* 最新的 [Java SE 开发工具包 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](http://azure.microsoft.com/pricing/free-trial/)。）

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

本部分中的操作将会修改在[使用 IoT 中心发送云到设备的消息](iot-hub-java-java-c2d.md)中创建的设备应用，以便将文件上传到 IoT 中心。

1. 将一个图像文件复制到 `simulated-device` 文件夹并将其重命名为 `myimage.png`。

1. 使用文本编辑器打开 `simulated-device\src\main\java\com\mycompany\app\App.java` 文件。

1. 将变量声明添加到 **App** 类：

    ```java
    private static String fileName = "myimage.png";
    ```

1. 若要处理文件上传状态回调消息，请将以下嵌套类添加到 **App** 类：

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

1. 若要将图像上传到 IoT 中心，请将以下方法添加 **App** 类，以便将图像上传到 IoT 中心：

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

1. 修改 **main** 方法以调用 **uploadFile** 方法，如以下代码片段中所示：

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

1. 使用以下命令生成 **simulated-device** 应用并检查错误：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>接收文件上传通知

本部分中的操作将创建一个 Java 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

需要使用 IoT 中心的 **iothubowner** 的连接字符串才能完成本部分。 可以在 [Azure 门户](https://portal.azure.com/)上的“共享访问策略”边栏选项卡中找到该连接字符串。

1. 在命令提示符下使用以下命令，创建名为 **read-file-upload-notification** 的 Maven 项目。 请注意，此命令是一条很长的命令：

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

1. 在命令提示符下，导航到新的 `read-file-upload-notification` 文件夹。

1. 使用文本编辑器打开 `read-file-upload-notification` 文件夹中的 `pom.xml` 文件，在 **dependencies** 节点中添加以下依赖项。 这样即可使用应用程序中的 **iothub-java-service-client** 包来与 IoT 中心服务通信：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-service-client**。

1. 保存并关闭 `pom.xml` 文件。

1. 使用文本编辑器打开 `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` 文件。

1. 在该文件中添加以下 **import** 语句：

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

1. 将以下类级变量添加到 **App** 类：

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

1. 若要在控制台中列显有关文件上传的信息，请将以下嵌套类添加到 **App** 类：

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Recieve file upload notifications...");
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

1. 若要启动侦听文件上传通知的线程，请将以下代码添加到 **main** 方法：

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

1. 保存并关闭 `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` 文件。

1. 使用以下命令生成 **read-file-upload-notification** 应用并检查错误：

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

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

* [以编程方式创建 IoT 中心][lnk-create-hub]
* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 IoT Edge 模拟设备][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png
[3]: ./media/iot-hub-csharp-csharp-file-upload/enable-file-notifications.png

<!-- Links -->



[Azure IoT 开发人员中心]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]:../storage/common/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-windows-iot-edge-simulated-device.md



