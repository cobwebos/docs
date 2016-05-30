<properties
   pageTitle="在 Linux 上使用 C 连接设备 | Azure"
   description="介绍如何使用在 Linux 上运行的以 C 编写的应用程序将设备连接到 Azure IoT 套件预配置远程监视解决方案。"
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.date="04/26/2016"
   wacn.date="05/17/2016"/>


# 将设备连接到远程监视预配置解决方案 (Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../includes/iot-suite-selector-connecting.md)]

## 在 Linux 上生成并运行 C 示例解决方案

1. 要克隆 “Azure IoT SDK” GitHub 存储库并在 Linux 桌面环境中安装“适用于 C 语言的 Azure IoT 设备 SDK”，请按照[设置 Linux 开发环境][lnk-setup-linux]说明进行操作。

2. 在文本编辑器中打开文件 **c/serializer/samples/remote\_monitoring/remote\_monitoring.c**。

3. 在该文件中找到以下代码：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. 将 **[Device Id]** 和 **[Device Key]** 替换为来自远程监视解决方案仪表板的设备值。

5. 使用来自仪表板的 **IoT 中心主机名**替换 **[IoTHub Name]** 和 **[IoTHub Suffix, i.e. azure-devices.net]**。例如，如果 **IoT 中心主机名**是 **contoso.azure-devices.net**，则将 **[IoTHub Name]** 替换为 **contoso**，并将 **[IoTHub Suffix, i.e. azure-devices.net]** 替换为 **azure-devices.net**，如下所示：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. 保存更改并生成示例。要生成示例，可以运行 **c/build\_all/linux** 目录中的 build.sh 脚本。生成脚本会创建 **cmake** 文件夹来存储已编译的示例程序。

7. 运行 **cmake/serializer/samples/remote\_monitoring/remote\_monitoring** 示例应用程序。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: /documentation/articles/devbox_setup/#linux


<!---HONumber=Mooncake_0523_2016-->