---
title: 使用 Python 将设备上的文件上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用适用于 Python 的 Azure IoT 设备 SDK 将文件上的设备上传到云端。 上传的文件存储在 Azure 存储 Blob 容器中。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 532ae26dfd7add5c5ecc61db259903239e449f40
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299138"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>使用 IoT 中心将文件从设备上传到云

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

此教程介绍了如何使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)将文件上传到 [Azure Blob 存储](../storage/index.yml)。 本教程介绍如何：

- 安全地提供存储容器用于文件上传。
- 使用 Python 客户端通过 IoT 中心上传文件。

[IoT 中心入门](iot-hub-node-node-getstarted.md)教程展示了 IoT 中心基本的设备到云消息功能。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

> [!NOTE]
> IoT 中心 Python SDK 目前仅支持上传基于字符的文件，如 .txt 文件。

在本教程最后，会运行下述 Python 控制台应用：

* FileUpload.py，该应用使用 Python 设备 SDK 将文件上传到存储中。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、.NET、Javascript、Python 和 Java）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心]。

要完成本教程，需要以下各项：

* [Python 2.x 或 3.x][lnk-python-download]。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 如果使用 Python 2.x，则可能需要[安装或升级 pip - Python 包管理系统][lnk-install-pip]。
* 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包][lnk-visual-c-redist]，以便使用 Python 中的本机 DLL。
* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](http://azure.microsoft.com/pricing/free-trial/)。）


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

本部分中操作将会创建可将文件上传到 IoT 中心的设备应用。

1. 在命令提示符处，运行以下命令来安装 **azure-iothub-device-client** 包：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. 使用文本编辑器，在工作文件夹中创建一个 FileUpload.py 文件。

1. 在 FileUpload.py 文件的开头添加以下 `import` 语句和变量。 将 `deviceConnectionString` 替换为 IoT 中心设备的连接字符串：

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name on storage after upload]"
    ```

1. 针对 upload_blob 函数创建一个回调：

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. 添加以下代码用于连接客户端和上传文件。 此外还包含 `main` 例程：

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

1. 保存并关闭 UploadFile.py 文件。

1. 将示例文本文件复制到工作文件夹，并将其重命名为 `sample.txt`。

    > [!NOTE]
    > IoT 中心 Python SDK 目前仅支持上传基于字符的文件，如 .txt 文件。


## <a name="run-the-application"></a>运行应用程序

现即可运行应用程序。

1. 在工作文件夹的命令提示符处，运行以下命令：

    ```cmd/sh
    python FileUpload.py
    ```

1. 以下屏幕截图显示来自 FileUpload 应用的输出：

    ![simulated-device 应用的输出](./media/iot-hub-python-python-file-upload/1.png)

1. 可以使用门户查看所配置的存储容器中上传的文件：

    ![上传的文件](./media/iot-hub-python-python-file-upload/2.png)


## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心][lnk-create-hub]
* [C SDK 简介][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

<!-- Links -->
[Azure IoT 开发人员中心]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
