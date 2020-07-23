---
title: 使用 Python 将设备上的文件上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用适用于 Python 的 Azure IoT 设备 SDK 将文件上的设备上传到云端。 上传的文件存储在 Azure 存储 Blob 容器中。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, tracking-python
ms.openlocfilehash: 9a3782c0d5791f20f14aabb53d486fc012518c1f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84608496"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>使用 IoT 中心将文件从设备上传到云 (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本文演示如何使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)将文件上传到 [Azure Blob 存储](../storage/index.yml)。 本教程介绍如何：

* 安全地提供存储容器用于文件上传。

* 使用 Python 客户端通过 IoT 中心上传文件。

[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)快速入门演示了 IoT 中心基本的设备到云的消息传送功能。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

在本教程的末尾，你将运行 Python 控制台应用：

* FileUpload.py，该应用使用 Python 设备 SDK 将文件上传到存储中  。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

本部分中操作将会创建可将文件上传到 IoT 中心的设备应用。

1. 在命令提示符处，运行以下命令来安装 azure-iot-device  包。 可以使用此包与 IoT 中心协调文件上传操作。

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 在命令提示符处，运行以下命令来安装 [azure.storage.blob  ](https://pypi.org/project/azure-storage-blob/) 包。 可以使用此包执行文件上传操作。

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. 创建将上传到 blob 存储的测试文件。

1. 使用文本编辑器，在工作文件夹中创建一个 FileUpload.py 文件  。

1. 在 FileUpload.py 文件的开头添加以下 `import` 语句和变量  。

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. 在文件中，将 `[Device Connection String]` 替换为 IoT 中心设备的连接字符串。 将 `[Full path to local file]` 替换为你创建的测试文件的路径，或是设备上要上传的任何文件的路径。

1. 创建一个函数以将文件上传到 blob 存储：

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    此函数分析传递给它的 blob_info  结构，以创建用于初始化 [azure.storage.blob.BlobClient](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python) 的 URL。 然后，它使用此客户端将文件上传到 Azure Blob 存储。

1. 添加下述用来连接客户端并上传文件的代码：

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    此代码创建一个异步的 IoTHubDeviceClient  ，并使用以下 API 来管理通过 IoT 中心进行的文件上传操作：

    * get_storage_info_for_blob  从 IoT 中心获取你之前创建的链接存储帐户的相关信息。 此信息包括主机名、容器名称、blob 名称和 SAS 令牌。 存储信息传递到 store_blob  函数（在上一步中创建），因此该函数中的 BlobClient  可以通过 Azure 存储进行身份验证。 get_storage_info_for_blob  方法还会返回一个 correlation_id，后者在 notify_blob_upload_status  方法中使用。 IoT 中心使用 correlation_id 来标记你在处理的 blob。

    * notify_blob_upload_status  向 IoT 中心通知你的 blob 存储操作的状态。 你将向其传递通过 get_storage_info_for_blob  方法获取的 correlation_id。 IoT 中心使用它来通知任何可能正在侦听文件上传任务状态通知的服务。

1. 保存并关闭 UploadFile.py 文件  。

## <a name="run-the-application"></a>运行应用程序

现在可以运行应用程序了。

1. 在工作文件夹的命令提示符处，运行以下命令：

    ```cmd/sh
    python FileUpload.py
    ```

2. 以下屏幕截图显示来自 FileUpload 应用的输出  ：

    ![simulated-device 应用的输出](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. 可以使用门户查看所配置的存储容器中上传的文件：

    ![上传的文件](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>后续步骤

在本教程中，已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

使用以下链接详细了解 Azure Blob 存储：

* [Azure Blob 存储文档](https://docs.microsoft.com/azure/storage/blobs/)

* [适用于 Python API 的 Azure Blob 存储文档](https://docs.microsoft.com/python/api/overview/azure/storage-blob-readme?view=azure-python)
