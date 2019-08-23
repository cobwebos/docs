---
title: 使用 Azure IoT 中心安排作业 (Python) | Microsoft Docs
description: 逼 ﹚ Azure IoT 中心作业调用多个设备上的直接方法，请按照。 使用 Azure IoT SDK for Python 实现模拟设备应用以及用于运行作业的服务应用。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: bc93dcea74f0660a4f56aec237eaae5a4e3eeb9a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904521"
---
# <a name="schedule-and-broadcast-jobs-python"></a>计划和广播作业 (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT 中心是一项完全托管的服务，允许后端应用创建和跟踪用于计划和更新数百万台设备的作业。  作业可以用于以下操作：

* 更新所需属性
* 更新标记
* 调用直接方法

从概念上讲，一个作业包装上述一项作业，并跟踪一组设备中的执行进度（由设备孪生查询定义）。  例如，后端应用可使用作业重启 10,000 台设备（由设备孪生查询指定并计划在将来执行）。  该应用程序随后可以在其中每个设备接收和执行重新启动方法时跟踪进度。

可在以下文章中了解有关所有这些功能的详细信息：

* 设备孪生和属性：[设备孪生入门](iot-hub-python-twin-getstarted.md)和[教程：如何使用设备孪生属性](tutorial-device-twins.md)

* 直接方法：[IoT 中心开发人员指南-直接方法](iot-hub-devguide-direct-methods.md)和[教程: 直接方法](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本教程演示如何：

* 创建一个 Python 模拟设备应用，该应用具有一种直接方法，使 lockDoor 能够由解决方案后端调用。

* 创建 Python 控制台应用，它使用作业在模拟设备应用中调用 lockDoor 直接方法，并使用设备作业更新所需属性。

在本教程结束时，会创建两个 Python 应用：

simDevice.py，它使用设备标识连接到 IoT 中心并接收 lockDoor 直接方法。

scheduleJobService.py，它调用模拟设备应用中的直接方法，并通过作业更新设备孪生的所需属性。

> [!NOTE]
> Azure IoT SDK for Python 不直接支持作业功能。 本教程中转而提供一种利用异步现成和计时器的备选解决方案。 有关进一步的更新，请参阅 [Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) 页面上的**服务客户端 SDK**功能列表。
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

下面是先决条件的安装说明。

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序

本部分将创建一个 Python 控制台应用，用于响应通过云调用的方法，这会触发模拟 lockDoor 方法。

1. 在命令提示符处，运行以下命令以安装 azure-iot-device-client 包：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > 用于 azure iothub 和 azure iothub 设备客户端的 pip 包目前仅适用于 Windows 操作系统。 对于 Linux/Mac OS, 请参阅为[Python 发布准备开发环境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)中的 linux 和 Mac OS 特定部分。
   >

2. 使用文本编辑器，在工作目录中创建一个 simDevice.py 文件。

3. 在 simDevice.py 文件的开头添加以下 `import` 语句和变量。 将 `deviceConnectionString` 替换为上述创建的设备的连接字符串：

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. 添加以下功能回调以处理 lockDoor 方法：

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. 再添加一个功能回调来处理设备孪生更新：

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. 添加以下代码以注册 **lockDoor** 方法的处理程序。 此外还包含 `main` 例程：

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. 保存并关闭 simDevice.py 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应该按文章 [Transient Fault Handling](/azure/architecture/best-practices/transient-faults)（暂时性故障处理）中所述实施重试策略（例如指数性的回退）。
>

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中, 将创建一个后端服务, 用于调用设备上的直接方法并更新设备克隆。 服务需要 "**服务连接**" 权限才能在设备上调用直接方法。 服务还需要**注册表读取**和**注册表写入**权限来读取和写入标识注册表。 没有默认的共享访问策略仅包含这些权限, 因此需要创建一个。

若要创建授予**服务连接**、**注册表读取**和**注册表写入**权限的共享访问策略, 以及获取此策略的连接字符串, 请执行以下步骤:

1. 在[Azure 门户](https://portal.azure.com)中打开 IoT 中心。 若要访问 IoT 中心, 最简单的方法是选择 "**资源组**", 选择 iot 中心所在的资源组, 然后从资源列表中选择 iot 中心。

2. 在 IoT 中心的左侧窗格中, 选择 "**共享访问策略**"。

3. 从策略列表上方的顶部菜单中, 选择 "**添加**"。

4. 在 "**添加共享访问策略**" 窗格中, 输入策略的描述性名称;例如: *serviceAndRegistryReadWrite*。 在 "**权限**" 下, 选择 "**服务连接**" 和 "**注册表写入**" (选择 "**注册表写入**时, 自动选择**注册表读取**")。 然后选择“创建”。

    ![显示如何添加新的共享访问策略](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. 返回 "**共享访问策略**" 窗格, 从策略列表中选择新策略。

6. 在 "**共享访问密钥**" 下, 选择连接字符串的复制图标 **--primary key**并保存值。

    ![显示如何检索连接字符串](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

有关 IoT 中心共享访问策略和权限的详细信息, 请参阅[访问控制和权限](./iot-hub-devguide-security.md#access-control-and-permissions)。

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>安排作业，用于调用直接方法和更新设备孪生的属性

在本部分中，将创建一个 Python 控制台应用，它使用直接方法在设备上启动远程 lockDoor 并更新设备孪生的属性。

1. 在命令提示符处，运行以下命令以安装 azure-iot-service-client 包：

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > 用于 azure iothub 和 azure iothub 设备客户端的 pip 包目前仅适用于 Windows 操作系统。 对于 Linux/Mac OS, 请参阅为[Python 发布准备开发环境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)中的 linux 和 Mac OS 特定部分。
   >

2. 使用文本编辑器，在工作目录中创建一个 scheduleJobService.py 文件。

3. 在 scheduleJobService.py 文件`import`的开头添加以下语句和变量。 将占位符替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串。 `{IoTHubConnectionString}` 将`{deviceId}`占位符替换为在[IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中注册的设备 ID:

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. 添加以下用于查询设备的函数：

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. 添加以下方法，用于运行调用直接方法和设备孪生的作业：

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. 添加以下代码以安排作业和更新作业状态。 此外还包含 `main` 例程：

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. 保存并关闭 scheduleJobService.py 文件。

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 在工作目录的命令提示符处，运行以下命令以开始侦听重启直接方法：

    ```cmd/sh
    python simDevice.py
    ```

2. 在工作目录的另一命令提示符处运行以下命令，以触发作业进行锁门和孪生项的更新：
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. 可在控制台查看针对直接方法和设备孪生的设备响应。

    ![IoT 中心作业示例 1-设备输出](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT 中心作业示例 2--设备输出](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>后续步骤

在本教程中，使用了作业来安排用于设备的直接方法以及设备孪生属性的更新。

若要继续完成 IoT 中心和设备管理模式（如远程无线固件更新）的入门内容，请参阅[如何更新固件](tutorial-firmware-update.md)。